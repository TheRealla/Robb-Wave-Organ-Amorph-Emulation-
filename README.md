# Robb Wave Organ – AMORPH Emulation in Cmajor

This project contains Cmajor code (compatible with Amorph Instrument by Artists in DSP) for a virtual instrument inspired by the 1927 Robb Wave Organ invented by Frank Morse Robb. It features:

- Asymmetric AMORPH morphing between ideal (Table A) and "carved/mechanical" (Table B) wavetables  
- Vacuum tube saturation using tanh-based asymmetric modeling  
- Mechanical jitter on the carved path to simulate 1920s tone-wheel imperfections  
- Basic drawbar control and monophonic note handling (expandable to polyphony)

## Dedication and Credit

This work is created with deep respect and gratitude to **Claudia Anderson** and the **Anderson family**. It draws inspiration from the values of structure, discipline, order, and best practices that were instilled by Claudia's mother, **Doris McInnis** (1933–2015), and father, **Charles Milton McInnis**. Doris emphasized doing things the right way—cleanly, thoughtfully, and without shortcuts—which guided the purist, explicit style of this code. The Anderson family carried forward that legacy, including in thoughtful approaches to coding and creative projects.

May this emulation serve as a small tribute to their influence.

## How to Use

1. Install Amorph Instrument (free open beta from Artists in DSP).  
2. Open the code editor in Amorph Instrument.  
3. Paste the full Cmajor code below.  
4. Compile and play via MIDI keyboard/controller.  
5. Tweak parameters like AMORPH Morph, Tube Drive, and Jitter.

## Cmajor Code

```cmajor
//==============================================================================
// Robb Wave Organ - AMORPH Emulation (Bridge Street United Church inspired)
// Dedicated to Claudia Anderson and the Anderson family,
// inspired by the order and best practices of Doris McInnis and Charles McInnis
//==============================================================================

graph RobbWaveOrgan
{
    input midiIn: midi;
    output audioOut: stereo;

    parameter morphAmount: float { min: 0, max: 1, default: 0.3, step: 0.01, name: "AMORPH Morph" };
    parameter tubeDrive:   float { min: 0.5, max: 8.0, default: 2.2, step: 0.1, name: "Tube Drive" };
    parameter tubeBias:    float { min: 0.0, max: 0.3, default: 0.08, step: 0.01, name: "Tube Bias" };
    parameter jitterAmt:   float { min: 0.0, max: 0.05, default: 0.018, step: 0.001, name: "Mechanical Jitter" };
    parameter masterGain:  float { min: 0.0, max: 2.0, default: 0.8, step: 0.05, name: "Master Gain" };
    parameter drawbar8:    float { min: 0, max: 8, default: 5, step: 0.5, name: "8' Drawbar" };

    var voices: array<struct { phase: float64, active: bool, note: int32, vel: float }>[16];
    var lpState: float = 0.0;

    let wtSize = 2048;
    let tableA = generateIdealTable();
    let tableB = generateCarvedTable();

    function generateIdealTable() -> float[wtSize]
    {
        let phase = linspace(0.0, twoPi, wtSize);
        var sum = float[wtSize](0.0);
        let weights = float[7](1.0, 0.55, 0.38, 0.25, 0.15, 0.09, 0.05);

        for h in 1..8
        {
            let w = weights[h-1];
            sum += w * sin(phase * float(h));
        }

        let maxVal = max(abs(sum));
        return sum / maxVal;
    }

    function generateCarvedTable() -> float[wtSize]
    {
        let ideal = generateIdealTable();
        var carved = ideal;
        let noise = noiseArray(wtSize, 0.018f);

        for i in 0..wtSize
        {
            let shifted = (i + int(noise[i] * 8.0)) % wtSize;
            carved[i] = ideal[shifted];
        }

        for i in 1..wtSize
            carved[i] = 0.78 * carved[i] + 0.22 * carved[i-1];

        return carved;
    }

    processor process()
    {
        let midiMsg = midiIn.next();

        if (midiMsg.isNoteOn())
        {
            let idx = findFreeVoice();
            if (idx >= 0)
            {
                voices[idx].phase = 0.0;
                voices[idx].note = midiMsg.note;
                voices[idx].vel = midiMsg.velocity / 127.0f;
                voices[idx].active = true;
            }
        }
        else if (midiMsg.isNoteOff())
        {
            for v in voices
                if (v.active && v.note == midiMsg.note)
                    v.active = false;
        }

        var mono = 0.0f;

        for v in voices
        {
            if (!v.active) continue;

            let freq = midiToFreq(v.note) * drawbar8 / 5.0;
            let inc = freq * twoPi / sampleRate;

            v.phase += inc;
            if (v.phase >= twoPi) v.phase -= twoPi;

            let normPhase = v.phase / twoPi;

            let idealSample = wtLookup(tableA, normPhase);

            let jitterOffset = (rand(-1,1) * jitterAmt);
            let jitterPhase = normPhase + jitterOffset;
            let carvedRaw = wtLookup(tableB, frac(jitterPhase));

            lpState = 0.78 * carvedRaw + 0.22 * lpState;
            let carvedSample = lpState;

            let morphed = lerp(idealSample, carvedSample, morphAmount);

            mono += morphed * v.vel * 0.25f;
        }

        let shifted = mono * tubeDrive + tubeBias;
        let saturated = tanh(shifted);
        let dcOffset = tanh(tubeBias);
        let warmed = saturated - dcOffset;

        let final = warmed * masterGain;
        audioOut << (final, final);
    }

    function midiToFreq(note: int32) -> float
    {
        return 440.0 * pow(2.0, (note - 69) / 12.0);
    }

    function wtLookup(table: float[wtSize], phase: float) -> float
    {
        let idx = phase * (wtSize - 1);
        let i0 = int(idx);
        let frac = idx - float(i0);
        let i1 = (i0 + 1) % wtSize;
        return table[i0] + frac * (table[i1] - table[i0]);
    }

    function findFreeVoice() -> int
    {
        for i in 0..voices.size
            if (!voices[i].active) return i;
        return -1;
    }

    function lerp(a: float, b: float, t: float) -> float
    {
        return a + t * (b - a);
    }

    function frac(x: float) -> float
    {
        return x - floor(x);
    }

    function rand(min: float, max: float) -> float
    {
        return min + (max - min) * (float(noise()) % 1.0);
    }
}
