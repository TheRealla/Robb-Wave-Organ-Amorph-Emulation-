![Robb Wave Organ Console (1937) – National Music Centre, Calgary](https://upload.wikimedia.org/wikipedia/commons/5/54/Robb_Wave_Organ_console_%281937%29%2C_National_Music_Centre.jpg)

# Robb Wave Organ – AMORPH Emulation in Cmajor  
**For Amorph Instrument by Artists in DSP**

A faithful software recreation of the legendary 1927 Robb Wave Organ invented by Frank Morse Robb in Belleville, Ontario. This virtual instrument uses **asymmetric AMORPH** wavetable morphing to recreate the unique “carved” tone wheels that were photographed from real pipe organ stops at Bridge Street United Church.

Written by **theRealla**.  
Dedicated with love and gratitude to his mother **Doris McInnis**, his sister **Claudia Anderson** (and the entire Anderson family), and **Lilibet** — whose values of discipline, order, and doing things the right way (without shortcuts) inspired the clean, explicit, purist style of this code.

---

### Visual Gallery

**Tone Wheel Mechanism** (the heart of the original instrument)  
![Tonewheel Diagram – Public Domain](https://upload.wikimedia.org/wikipedia/commons/8/81/Tonewheel-p.svg)

**Bridge Street United Church, Belleville** (the actual source of the waveforms photographed in the 1920s)  
![Bridge Street United Church](https://upload.wikimedia.org/wikipedia/commons/thumb/0/0e/Bridge_Street_United_Church.JPG/800px-Bridge_Street_United_Church.JPG)

**Classic Pipe Organ Interior** (inspiration for the cathedral-like sound and reverb space)  
![Cathedral Pipe Organ – Public Domain](https://www.publicdomainpictures.net/pictures/30000/velka/pipe-organ-153668.jpg)

---

### Features
- Asymmetric **AMORPH** morphing between ideal (Table A) and mechanically “carved” (Table B) wavetables  
- Mechanical jitter + 1-pole magnetic induction low-pass on the carved path  
- Authentic asymmetric tanh vacuum-tube saturation with bias  
- Simple 8' drawbar control and velocity-sensitive monophonic play (easily expandable)  
- Procedural Diapason-style wavetables with Bridge Street harmonic profiles  
- Parameters auto-mapped to knobs in Amorph Instrument  

### How to Use
1. Download and install **Amorph Instrument** (free open beta from Artists in DSP).  
2. Open the code editor.  
3. Paste the entire content of `RobbWaveOrgan.cmaj`.  
4. Click **Compile** — the instrument appears instantly with knobs.  
5. Play via any MIDI keyboard or controller.  
6. Tweak **AMORPH Morph**, **Tube Drive**, **Jitter**, and **Drawbar** for everything from pristine 1927 factory sound to warm, aged growl.

### The Code File
`RobbWaveOrgan.cmaj` (full source included in this repository) contains the complete, ready-to-run Cmajor graph.

### License
MIT License — free to use, modify, and share. Please keep the dedication section intact.

### Acknowledgments
- Historical research and inspiration from the National Music Centre (Calgary) and Bridge Street United Church.  
- Cmajor language and Amorph Instrument platform by Artists in DSP.  
- Special thanks to the family legacy that shaped the disciplined approach to this project.

---

**theRealla** — March 2026  
A small tribute to history, family, and the joy of building beautiful instruments.
