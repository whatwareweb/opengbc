---
title: "OpenGBC"
author: "whatware"
description: "Open source Game Boy Color recreation using all new parts"
created_at: "2025-07-15"
---

### total time spent - 70hr

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

<img width="686" height="708" alt="Screenshot_20250720_200115" src="https://github.com/user-attachments/assets/73531541-168c-46ac-874f-71e7f5be3446" />


Then, I added the RAM IC to the schematic. However, this presented another problem, the RAM was going to be a huge mess on the schematic with the pin positions as-is on the symbol, so I went ahead and edited that too to make it much more similar to the original GBC schematic. In the original GBC schematic, they just used a bus for the RAM, which would make the schematic much simpler, but I had never used buses before, so I looked up a tutorial on youtube. One thing that was confusing was the RAM read enable/write enable pins, because on the original GBC schematic the pin labels I think were mistyped or something, but I eventually got it figured out with the help of some of the schematics from the Leggomyfroggo mod.

After making all the connections, I cross-referenced them with Natalie the Nerd's schematics, just to make sure all the pins were connected correctly.  This took a significant amount of time, but it was definitely worthit, I didn't want the whole gameboy to not work just because one of the RAM pins was connected wrong.

<img width="1049" height="839" alt="Screenshot_20250720_212544" src="https://github.com/user-attachments/assets/f8fe91ce-1cc2-4076-8bc5-f8993b682f6d" />

Next up was the controls. I wanted to have tactile buttons like found in the GBA SP included, but also the option for normal button membranes if desired, which I would need to add in the footprint. For now though, I needed to just add the 8 buttons required by the GBC, and wire them up to the correct pins.

<img width="570" height="629" alt="Screenshot_20250721_133619" src="https://github.com/user-attachments/assets/6063250f-2b5e-4e97-8768-49c9fa10374f" />

Time spent: 8h

# July 21 - more schematics
First thing I did today was to create hierarchical schematics for the EXT connector and IR port. I didn't fill them out just yet because they are not critical for functionality. I then got started creating the symbol for the cartridge connector, and hooking all of the data lines up to the CPU. 

<img width="455" height="700" alt="Screenshot_20250721_200458" src="https://github.com/user-attachments/assets/c6dc2c06-abb0-4d8e-972c-8fd061803ce0" />


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

<img width="255" height="765" alt="Screenshot_20250722_230507" src="https://github.com/user-attachments/assets/257e477c-f79d-4595-ad4e-a2ca87275e87" />

<img width="261" height="641" alt="Screenshot_20250722_230517" src="https://github.com/user-attachments/assets/69939786-40ae-4d1f-b02c-73014b83a55c" />


After doing that, I zoomed out on the schematic and realized how close to complete it really was, and I just needed a couple more smaller components really.

<img width="1167" height="804" alt="Screenshot_20250722_233553" src="https://github.com/user-attachments/assets/34aa2b90-f271-4d47-95a7-6e646f05c87a" />


time spent: 5h


# July 26 - audio system
First thing I did today was to start the hierarchical schematic for the audio circuitry. I wanted this to be compatible with the GBC, so I needed an amp that has a stereo channel input, but a mono speaker output since the GBC has one speaker but a stereo headphone jack. I looked on TI's website for this, and found the LM4853. This chip fits the bill perfectly. I looked at the typical application schematic to get it set up, and cross-referenced the original GBC schematic to see the other circuitry necessary for e.g. the volume control. I also had to download the symbol off Mouser's website. The symbol I downloaded would make the schematic messy, so I cleaned up the layout.

I then got started on the circuitry, basing off of the typical application but making a few important changes. One change was to adjust the gain of the amp because the GBC CPU outputs at logic level, which needs to be amplified negatively. The amp chip is still necessary though, the CPU pins can't handle enough current to drive the output.

<img width="1198" height="629" alt="Screenshot_20250727_135226" src="https://github.com/user-attachments/assets/46e197eb-8a65-4aa2-9e6f-ebd15b702377" />


time spent: 5h


# july 27 - A ton of miscellaneous circuitry
First thing I did was just add a couple bypass capacitors:

<img width="187" height="163" alt="Screenshot_20250727_135313" src="https://github.com/user-attachments/assets/26538e9e-6b82-4abb-ac61-bd602c9595ef" />


then i got started on the reset circuitry. The original reset IC was of course out of production, so I had to look for an alternative, which was actually surprisingly difficult, because i had no idea what the ic would actually be called. The function of the IC is to pull the reset line low (active) on the cpu when the voltage is too low, to prevent erratic behavior by the cpu. I eventually found the kind of ic i needed, which is called an input voltage supervisor. Instead of the PST9135N, which is out of production, I went with a TPS3840 in the 3.5v variant.

I first downloaded the symbol from Mouser, then read over basically the entire datasheet to figure out how to actually use the chip. After reading the datasheet, I modified the symbol again so it would be laid out nicely. I then placed the IC, but realized it would be annoying to place, so I just had to basically move the whole schematic over. After adding most of the schematics for the reset IC, i then had to calculate the delay capacitor value. I found with some research that the GBC uses an 18ms time delay for this. I used TI's formula to calculate the capacitor value which was on their datasheet, and found a value of .029uF, but .033uF caps were way cheaper and close enough so i went with that.

<img width="340" height="347" alt="Screenshot_20250727_183205" src="https://github.com/user-attachments/assets/e744921d-421f-4cb1-a90b-e0741c1575b9" />


Next up was the IR circuitry. For this, I basically copied over the original GBC schematic into my hierarchical schematic, so it wasn't terribly complicated, but the pinouts of the transistors were confusing because the manufacturer had multiple similar packages with all different pinouts.

Once that was done, I ran ERC and fixed a couple issues on there, and had to fix some symbols that I messed up before. Then, I moved onto the serial link port. This was another matter of copying over from the original schematic, and wasn't super complicated. I did have to create another symbol for the port though.

Next up was the power circuit. I did a little trick for this, where you just add a hierarchical schematic, but it doesn't have any hierarchical pins, it's just there to separate it from the rest of the circuit. 

I deliberated over the power section, whether I wanted to use a lipo battery and usb-c, or just use the original AAs, and I also looked up design documentation from TI for each of these. For some reason, TI's Li-ion charging portfolio includes basically only chips that can be i2c controlled, which are practically useless for this application. Due to this, and the fact that rechargeable NIMH batteries are honestly more convenient in my opinion, I decided to just go with the original GBC power circuitry. The original circuitry is fairly simple, and is mostly passive components. However, it relies on an extra PCB to actually provide the 5v from the 3v battery, probably to reduce switching noise since it's a boost converter.

It was at this point when I got sidetracked, and got concerned about the crystal oscillator not working, so I reached out to Leggomyfroggo just to see what his experience was with that.

After doing that, I continued on the power circuitry. The GBC power switch is custom for some reason, so I needed to create the symbol for it. I then had to create another custom symbol for the regulator board, and I then hooked that up. I changed the original schematic by putting the -15v and 13v rails as NC, as they are not needed with the IPS screen. I then added in the 3.3v LDO regulator, which was super simple, only 3 pins. Luckily I didn't have to make a footprint because it was already in the Kicad libraries.

<img width="1356" height="663" alt="Screenshot_20250728_013014" src="https://github.com/user-attachments/assets/5c2c92cd-432a-437b-9449-eddab92b3a72" />


and with that, i'm done with the schematic!!

time spent: 10h

# july 28 - double check schematic, and BOM
First thing I did today was just to double check the schematic. I double checked every connection, and I'm glad I did because I found that the cartridge port power was connected to 3.3v instead of 5v, oops. After fixing that, I started to get ready to assign footprints and edit the BOM. This is a very time consuming process because it requires looking at every single component and finding the right footprint on Mouser or wherever.

During this process, I had to do some research to figure out that the GBC CPU is a 14x20mm 128-pin LQFP package with .5mm pin pitch, which luckily has a default Kicad footprint. I also realized that the library package that I was using had a great Kicad library for the buttons and battery, so I switched to using that instead of having it separate like before.

For the passives, it was annoying but simple, I just had to add the Mouser PN to the bom and add the footprint.

For the link port, I found an adapter for the link port to the Flipper Zero, but it had the footprint available in the library, so I downloaded that file and included it. Here's the Github link for that adapter: https://github.com/ethanpierce/flipperzero-gbc-link-adapter

time spent: 7hr

# july 29 - more BOM
I am spending more time on the BOM because I had to go to sleep yesterday. idk why this is so time consuming tbh, just searching for some of the more obscure components is a pain. The only really unique thing that I had to do was to switch the symbol of the buttons and battery contacts to one in the gbc kicad library, because it had a corresponding footprint that I wanted to use.

time spent: 3h

# july 30 - more BOM, board layout, schematics & footprints
Today I did even more component searching and BOM filling, and the first thing I did was the potentiometer. This was a huge pain, because I could not find any that seemed to be correct on Mouser. However, I eventually found a potentiometer that was close enough, the footprint was a little bit off but it seemed that it would theoretically work. However, the footprint was different, so I had to manually edit that.

WHY DOES THE SYMBOL FIELDS EDITOR KEEP CRASHING RAAHHHHH

The passives were easy but tedious as always.

One thing that was very annoying was the FFC connector, because there was no 3d model or anything for the specific FFC connector for the GBC on Mouser. However, after looking around for a while, I found the footprint on the manufacturers website: https://www.amphenol-cs.com/product/62684502100ahlf.html and it also had the 3d model which was nice.

Next I went through and did all of the resistors, these were slightly easier because they were all the same footprint so that made it go a lot faster.

The next thing I did was look for the fuse. This gameboy needed a 1A SMD fuse, that of course the original was not being built anymore. Luckily I found a compatible fuse in Bucket mouse's repo.

Next thing i needed to do was to create the footprint for the DC jack. I found a method online where you can set the transparency of the windows in KDE, which allows you to overlay boards so that you can create the footprints without having access to the original part. Using this, I overlayed the GBC board scans onto the KIcad window.

Through some research on Aliexpress, i discovered that the GBC uses a 2.5x0.7mm DC barrel jack, which is very strange and nonstandard. I couldn't find barrel jacks on Mouser so I asked Google Gemini which was actually very helpful and led me to the "DC Power connectors" section on Mouser. I couldn't find it on Mouser, so looking on DIgikey I found one that looked like it should work.

It was at this point that I realized I completely forgot to add the power LED indicator, so I went back into the schematic and added that.

At that point, the only thing I had left to do was to add the regulator footprint, but I had to go to bed.

time spent: 6h

# july 31 - footprints and board layout
First thing I did today was just finish up the footprints with the footprint for the voltage regulator board. I cross-referenced Bucket Mouse's power board footprint to recreate this footprint in Kicad. After that, that was the very last thing to do in the BOM, so I updated the PCB from the schematic. Of course there were a ton of errors, so I went through and fixed those. Once that was done, I first updated the PCB from the schematic, and then pasted in the GBC board outline from the GBC_repro_lib repo. I then overlayed the original GBC board underneath my replacement board, in order to line up the components. I did this with the KDE transparency feature I mentioned before.

Then, it was just a matter of placing components. I started with the largest ICs first, and lined them up just how they were on the original GBC schematic. I then did the connectors, smaller ICS, and passives.

After that, I did the routing of the PCB. I first routed the RAM, which just had a bunch of pins. I had to redo it a couple times to get a good alignment.

I then routed all the pins to the display connector, and again had to do it a couple times to get it all right, the capacitor in the middle was a pain to deal with. I also routed a whole bunch of passives to the right.

Next, I routed all the audio portions of the board.

time spent: 12h
