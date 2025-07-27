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

time spent: 5h

# July 16 - Even more research
Today, I found a great Youtube video explaining how exactly the power circuitry works in the GBC. https://www.youtube.com/watch?v=Mbp_mAcxLUQ . It also had the Nintendo official service manual schematics linked in the description, so I was able to cross-reference that as well with NtN schematics. With that as well as my block diagram, I got started with creating the Kicad symbols as laid out in the Nintendo schematics.

I also found a great resource ReverseRetroLib, which had footprints and symbols for various parts of the Gameboy, which would make my job a whole lot easier.

Funnily enough, watching Youtube videos on various PCB mods available already for the GBC was extremely helpful to learn about the different sections of the Game Boy Color, and gave me a really good grasp on how the hardware worked and was laid out.

Time spent: 3h

# July 20 - Start pcb design!
Today I started the PCB design. First thing I did was to create a new Kicad project, and then start looking at symbols for the GCB CPU, as it's the main component on the board everything is based around. I looked at the ReverseRetroLib symbol for the CPU, which seemed pretty good except some of the pin names were different from the schematics. I edited this in the symbol, and also cross-referenced another symbol I found from researching the Leggomyfroggo mod. After that, I needed to add the RAM IC. While I could use the original RAM IC, I desided it was best to use a new off-the-shelf one, because from what I could tell, different Gameboy variants used different RAM chips. I found a suitable RAM IC from Alliance off of Mouser, which luckily had the footprint and symbol available to download, so I imported those as well.

I then started adding the symbol for the CPU to the schematic, but it was way too tall to fit on the schematic page. Therefore, I went back and split up the symbol to be 2 separate modules so that it would fit.

Then, I added the RAM IC to the schematic. However, this presented another problem, the RAM was going to be a huge mess on the schematic with the pin positions as-is on the symbol, so I went ahead and edited that too to make it much more similar to the original GBC schematic. In the original GBC schematic, they just used a bus for the RAM, which would make the schematic much simpler, but I had never used buses before, so I looked up a tutorial on youtube. One thing that was confusing was the RAM read enable/write enable pins, because on the original GBC schematic the pin labels I think were mistyped or something, but I eventually got it figured out with the help of some of the schematics from the Leggomyfroggo mod.

After making all the connections, I cross-referenced them with Natalie the Nerd's schematics, just to make sure all the pins were connected correctly.  This took a significant amount of time, but it was definitely worthit, I didn't want the whole gameboy to not work just because one of the RAM pins was connected wrong.

Next up was the controls. I wanted to have tactile buttons like found in the GBA SP included, but also the option for normal button membranes if desired, which I would need to add in the footprint. For now though, I needed to just add the 8 buttons required by the GBC, and wire them up to the correct pins.

Time spent: 8h

# July 21 - more schematics
First thing I did today was to create hierarchical schematics for the EXT connector and IR port. I didn't fill them out just yet because they are not critical for functionality. I then got started creating the symbol for the cartridge connector, and hooking all of the data lines up to the CPU. 

I next got started on the crystal. As my GB Boy has a 25MHZ crystal and some division circuitry, I wanted to simplify the design by using a standard crystal. In Leggomyfroggo's tadboy, he used a crystal from Mouser that should work for this project as well, so I went ahead and used the same model. However, this crystal was used in a very strange way on the TBC, so I had to reverse engineer that part of the schematic. From the TBC, the crystal pinout was:

| pin | connects to | function      |
|-----|-------------|---------------|
| 1   | 3.3v        | enable output |
| 2   | gnd         | gnd           |
| 3   | clk1        | clock output  |
| 4   | 3.3v        | vcc           |

time spent: 6h

# July 22 - even more schematics
First I got started on figuring out which pins were actually needed for the display. Lots of the pins on the display were for power for the original GBC screen, but since I don't have an original screen, those were not needed at all. I looked on Aliexpress for the screen I was using, and the one that seemed the best was the "Drop In GBC 2.45" IPS LCD Mod Kits Replacement Easy Installation OSD Touch Control Screen For Game boy Color GBC". I also managed to find the matching FFC connector that was in the Bucket Mouse repo, which was the Amphenol 62684-502100AHLF. While the GBC uses a standard FFC connector, I decided to make my own symbol for it because the pinout is very complicated, and a custom symbol will make it easier to understand.

After creating that symbol, I then tried to fit in onto the schematic, however it was extremely large so I decided to add it to another hierarchical schematic to leave room for other things. I then added all the circuitry which was just a couple capacitors and a bunch of no-connect pins basically, because the original GBC screen has a ton of random voltages, but modern screens don't use those. It was then a matter of adding all the schematic flags. I then realized I forgot to add the pin specifications for the symbol, so I finished that as well.

After doing that, I zoomed out on the schematic and realized how close to complete it really was, and I just needed a couple more smaller components really.


time spent: 5h
# July 26 - audio system
First thing I did today was to start the hierarchical schematic for the audio circuitry. I wanted this to be compatible with the GBC, so I needed an amp that has a stereo channel input, but a mono speaker output since the GBC has one speaker but a stereo headphone jack. I looked on TI's website for this, and found the LM4853. This chip fits the bill perfectly. I looked at the typical application schematic to get it set up, and cross-referenced the original GBC schematic to see the other circuitry necessary for e.g. the volume control. I also had to download the symbol off Mouser's website. The symbol I downloaded would make the schematic messy, so I cleaned up the layout.

I then got started on the circuitry, basing off of the typical application but making a few important changes. One change was to adjust the gain of the amp because the GBC CPU outputs at logic level, which needs to be amplified negatively. The amp chip is still necessary though, the CPU pins can't handle enough current to drive the output.
