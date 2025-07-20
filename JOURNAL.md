---
title: "OpenGBC"
author: "whatware"
description: "Open source Game Boy Color recreation using all new parts"
created_at: "2025-07-15"
---

# July 15 - Lots and lots and lots of research (did i mention lots of research)
Let me explain first, so I have this Chinese knockoff Game Boy Color called the GB Boy Colour, which surprisingly uses an actual clone Game Boy Color CPU. The electronics hardware is actually surprisingly good, the only major downside is that the overall quality is just terrible, for example the plastics and such. Also, it does not improve much overall on the original Game Boy Color, for example it still uses AA batteries, and the quality of the screen is still pretty bad. There are also some quality-of-life improvements that can be made, like adding tactile buttons, adding Li-ion charging directly on the motherboard, upgrading the audio circuitry (maybe stereo speakers?), and refreshing the case design to make it more modern and thinner.

To get started, I first looked for any kind of reverse engineering effort of the Game Boy Color. From this, I found Natalie the Nerd's Game Boy Color reference at https://wiki.nataliethenerd.com/docs/devicereferences/gameboy/colorref/. I also found Bucket Mouse's project https://github.com/MouseBiteLabs/Game-Boy-Pocket-Color which aimed to fit the Game Boy Color inside a Game Boy Pocket shell. However, I couldn't get Natalie's schematics to load correctly in recent versions of Kicad, and Bucket Mouse's project was built in Altium or Eagle or something which I don't have a license for. So, I was left with just the schematics of the original GBC, and the pinouts of the CPU and cartridge ports from Natalie's wiki page.

Natalie's CPU pinout and schematics proved very helpful in understanding how the circuitry of the GBC worked, so with that I wrote up a little block diagram for myself to figure out exactly what I needed to do.

# July 16 - Even more research
Today, I found a great Youtube video explaining how exactly the power circuitry works in the GBC. https://www.youtube.com/watch?v=Mbp_mAcxLUQ . It also had the Nintendo official service manual schematics linked in the description, so I was able to cross-reference that as well with NtN schematics. With that as well as my block diagram, I got started with creating the Kicad symbols as laid out in the Nintendo schematics.

I also found a great resource ReverseRetroLib, which had footprints and symbols for various parts of the Gameboy, which would make my job a whole lot easier.
Time spent: 3h

# July 19 - Start pcb design!
Today I started the PCB design. First thing I did was to create a new Kicad project, and then start looking at symbols for the GCB CPU, as it's the main component on the board everything is based around. I looked at the ReverseRetroLib symbol for the CPU
