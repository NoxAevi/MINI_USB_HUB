---
title: "Mini USB Hub"
author: "NoxAevi"
description: "Mini USB Hub mainly used as a demo for USB PD pass-through and swapping the host port w/ the charge/data port"
created_at: "2026-07-25"
---

# 7/27/26:

I started out by creating a kicad project and then going through the main components and adding the connections on the schematic

For the USB2532 symbol, I wound up creating my own because there was no suitable one (I checked the USB2514B symbol in kicad, but certain stuff wasn't really applicable for the USB2532)

I also couldn't really use this diagram for some of the pins (should be self-explanatory why)

<img width="942" height="919" alt="image" src="https://github.com/user-attachments/assets/28c25124-f376-4ada-8e9f-a01d2d16dcdf" />

When going through the documentation, there was an ability to disable ports, and I had to confirm that there would be integrated resistors to prevent this (so I wouldn't have to add external ones)

<img width="966" height="103" alt="image" src="https://github.com/user-attachments/assets/bffedc53-acbb-4cc3-8245-72c1721dc18a" />

When looking at the direction for one of the pins, I got confused on whether or not it was supposed to be an output or input

<img width="955" height="108" alt="image" src="https://github.com/user-attachments/assets/b335a123-a11e-4054-be62-6c9f8837d8d8" />

This makes it seem like we have a choice to either enable or disable this pin,but O8 is an output buffer type

<img width="952" height="37" alt="image" src="https://github.com/user-attachments/assets/33bda040-2a50-419c-b971-4be3231bc685" />

I decided to just make the symbol and deal with this later

When looking at the application usage to see what the UART would be used for, I saw that they included 5 ports (including upstream)


<img width="984" height="622" alt="image" src="https://github.com/user-attachments/assets/bf1e53c0-7a7c-4297-a284-cd3647df72cc" />

However, it's clear that there should only be 3 ports (including 1 upstream)

<img width="1014" height="334" alt="image" src="https://github.com/user-attachments/assets/09035556-e21f-421e-a966-ce6ef7e37ff5" />

This doesn't really make sense at all, so I guess I gotta email microchip about this (to also find out what the UART is supposed to be used for, if anything). The datasheet for the hub doesn't really mention it aside from it existing, even excluding it from the features part at the top

Anyways, this is what I ended up with in the end for this symbol (so much time spent on just one symbol :sob:, though I believe that the onsemi docs are better, so it should be much quicker tmr [since there also doesn't exist a symbol for the FUSB15200/PD controller])

<img width="553" height="490" alt="image" src="https://github.com/user-attachments/assets/f43b5389-e39e-40e3-80db-db5db3d95fca" />


Also sourced a 24 MHz crystal
 
**Total Time Spent**: 1h 50m

# 7/25/26: Sourced main parts (hub controller & PD controller)

*This continues from the journal on 7/23/26 of my bigger USB hub project*

When I tried to contact TI for support, I found out that they used forums where you can't post a question unless you have a company email. Thus, that option was kinda closed off.

I then asked reddit and got a reply on two chips by onsemi that I should take a look at

<img width="897" height="231" alt="image" src="https://github.com/user-attachments/assets/82691029-48e5-49e5-854b-a440c6f28395" />

The only thing was that the latter was no longer going to be in production (but I could still look at other ICs by onsemi, which I don't believe I checked before)

<img width="1414" height="285" alt="image" src="https://github.com/user-attachments/assets/1f4996fb-4d7f-4c17-ac32-25e5724f6d85" />

It's also out of stock on suppliers like lcsc and digikey

<img width="852" height="424" alt="image" src="https://github.com/user-attachments/assets/1e6c417c-62ad-4137-9fdb-b4462d85cba0" />

<img width="951" height="385" alt="image" src="https://github.com/user-attachments/assets/b492aa75-dcf0-4b9d-99fe-ca4d40eefa9e" />

I also quickly checked the FUSB302B to fast role swap, and it unfortunately did not have such a feature

I then found the FUSB15200, which seemed to have all the features that I would need for it, thought it unfortunately only supports up to 60W instead of 100W (i think?)

<img width="712" height="463" alt="image" src="https://github.com/user-attachments/assets/9f26e976-fb37-479f-9594-329402fa0de7" />

(Yet they have an example where it's changed)

<img width="1266" height="328" alt="image" src="https://github.com/user-attachments/assets/c5ce9209-c52d-40a3-bec0-f003fc1673db" />

Also took a look at the firmware, and it looks pretty confusing, but I think that it'll manage to work

Then, I sourced a USB hub controller (USB2532) that has flex connect (ability to swap host ports, similar to what i'd be doing in the main project)

Some of the options on microchip's website weren't available on digikey/LCSC (and it didn't even have a page up, which was weird), but I was able to find another IC that seems to work just fine

I was initially a bit worried when i saw the VBUS_DET pin require 3V3 volts to determine if a host has been connected (since there's going to be PD support), but then I realized that I could just use the GPIO pins on the PD controller for this

<img width="1126" height="462" alt="image" src="https://github.com/user-attachments/assets/7fe3eb1e-2dc5-4f5a-84d7-fca7ae8dcc8d" />


**Total Time Spent:** 1h
