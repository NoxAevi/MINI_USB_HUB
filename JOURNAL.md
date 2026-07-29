---
title: "Mini USB Hub"
author: "NoxAevi"
description: "Mini USB Hub mainly used as a demo for USB PD pass-through and swapping the host port w/ the charge/data port"
created_at: "2026-07-25"
---

# 7/29/26: Moar schematic pains (new OCS detector research too)

I started off today with finishing the stuff for the over-current IC before sourcing and starting on this downstream USB C port

<img width="514" height="304" alt="image" src="https://github.com/user-attachments/assets/591d408d-c644-4222-91db-77f1a935a55e" />

When I got to the CC pin configuration, I scrolled through an application note for the usb hub, and found that the swap is meant to make it easier to route. This makes me wonder about what would happen if I were to enable the swap while it's powered on rather than tying it to low/high

<img width="1174" height="208" alt="image" src="https://github.com/user-attachments/assets/ab150599-8ee5-4c97-b46b-ab348288ab29" />


This was when I noticed that there currently wasn't a way to swap the two ports from/to downstream to/from upstream

The datasheet mentions that this can only be done by changing the registers. Thus, I'm gonna have to write down the current configuration I have and then do the stuff required for SMBus/I2C (while also hoping that the FUSB15200/PD controller has I2C host support, which it luckily does)

<img width="726" height="186" alt="image" src="https://github.com/user-attachments/assets/7cc5c087-a6b4-443a-95e8-ebfca135fe2b" />

To enable SMBus, i need pullup resistors, but it wasn't really mentioned in a lot of diagrams

<img width="897" height="291" alt="image" src="https://github.com/user-attachments/assets/f85c0434-ddd5-4d21-8b7b-941ce02c9a2c" />

When going through the datasheet some more, i found that there's supposed to be a third configuration pin? (and yes I confirmed the part number this time)

<img width="1182" height="660" alt="image" src="https://github.com/user-attachments/assets/6e8ef984-a9b2-43eb-99d7-f35fe755e178" />

<img width="1192" height="324" alt="image" src="https://github.com/user-attachments/assets/87ed135e-8029-497d-87ba-18b93a7f3d74" />

My first thought was that the numbers in the first picture were referring to pin numbers (because what else would they realistically point to)

But in reality they dont (especially since the IC only has 36 pins to begin with)

I'll probably just ignore this 

When looking at the schematic for the 2534, I think the resistors I'm supposed to use are the 10k ones

<img width="1315" height="673" alt="image" src="https://github.com/user-attachments/assets/fd17552b-a56e-4995-8ac3-33d61d374cc6" />

What I'm kinda confused about is the need for a usb CC controller since I thought I could do smth similar to how a device's usb cc ports work and use constant resistors

Either way, I was able to source one that I could use for this (which also seems to include an OCS detector since it has a fault pin similar to the existing OCS i'm using)

<img width="682" height="373" alt="image" src="https://github.com/user-attachments/assets/6850b6dc-64d9-4e24-a89a-5413ba389072" />

This makes me think that the over-current stuff might be handled by the PD controller

After looking at the datasheet for the FUSB, it seems that it isn't handled by the PD controller directly, but they do recommend a current limiter to use, which also works (and also means that I won't need to ask about OCS on downstream port one, but probably still need to ask about it during flex operation)

I also believe changing flex mode on the fly should work just fine considering this statement in the register datasheet

<img width="718" height="126" alt="image" src="https://github.com/user-attachments/assets/29c488c1-1616-473f-b87a-db2bfa89e96e" />

Anyways, the current limiting stuff that I already have is kinda useless cuz the TI stuff replaces it, but before removing it i have to also remove the notes in my sourcing doc related to the specific capacitors

In Kicad, there isn't an existing symbol for the new current limiter/cc controller that I'm gonna be using, so I'll probably be starting tomorrow by working on that  (though there is an existing footprint which is pretty nice)


**Total Time Spent:** 1h 50m

# 7/28/26: Started Schematic

On the UART from yesterday, it seems that it's a part of a USB to UART thingy that I could use (but also have no use for), so I'll just put those as no connects on the schematic

Some configuration pins have other purposes, such as I2C, and it mentions a programming utility for the chip

<img width="969" height="100" alt="image" src="https://github.com/user-attachments/assets/96009e0c-8d84-439d-9123-25cacec98562" />

However, when looking at the docs for the utility, it doesn't really mention how we're supposed to flash it, so I'll have to determine if i'm going to need the tool or if just the configuration pins should be enough

However, it's also programmable via I2C (which i'd prefer to avoid, but would probably be the option i go with if i need to do more programming)

Though in the end, it seems that the configuration pins are plenty to achieve what I want

Then, for the BC_EN pins, i had to confirm whether USB's battery charging protocol (and disabling/enabling it) would have any impact on USB PD

Thanks to an [article from TI](https://www.ti.com/lit/an/slvae17a/slvae17a.pdf?ts=1785274748578), I learned that BC uses the data pins to advertise while I believe that usb PD only uses the CC pins

<img width="1233" height="231" alt="image" src="https://github.com/user-attachments/assets/4ced98c7-54cd-40ca-bf47-bec24322336e" />

When I got to the overcurrent pins, I had to think a bit about what to do about USB PD (since the current would 1: have to exceed stuff like 5V 2A, and 2: potentially go in the reverse direction)

I believe that the best way to move forward would be to exclude an OCS detector on the downstream 1 port (the one that can be swapped with the host port)
But since I'm not really sure, I'll put this pin on hold and ask around on somewhere like reddit or the Kicad discord server (maybe both idk, depends)

For the VBUS_DET pin, I was going to use the PD controller (which has GPIO pins n stuff), but it seems that i'm able to just keep it high (since it won't be directly connected to the host anyways, and I have a way to reset it using the RESET_N pin already)

<img width="1105" height="66" alt="image" src="https://github.com/user-attachments/assets/eeaa8541-b7c5-41a4-b218-61ac78bdf445" />

For the PRTCTL pins, I initially thought that I should use a pulldown to disable BC, but in the schematic from earlier, they have headers to enable/disable them with shunts, which probably means that leaving it floating is fine

They also use it as a part of the OCS stuff

<img width="589" height="132" alt="image" src="https://github.com/user-attachments/assets/df201265-9993-43cc-9382-4ae5f60a9741" />

<img width="748" height="397" alt="image" src="https://github.com/user-attachments/assets/efaed61f-bf87-4c86-83ce-189e32dbb66a" />

Since downstream port 2 is only going to be a downstream port, I decided that it'd be best to include the OCS stuff for that port now 

The example schematic uses the AP2111 from diodes inc, and since it isn't that expensive, I'll probably be using it too

However, after a quick search on digikey I was able to find one that was cheaper by about 10 cents, so I'll be using that one instead

<img width="1753" height="543" alt="image" src="https://github.com/user-attachments/assets/67059f79-cfcd-4836-8c1a-8128353bec51" />


<img width="1374" height="636" alt="image" src="https://github.com/user-attachments/assets/44301b5f-5bf2-4640-b176-3a041a75f4a4" />

I didn't finish adding the capacitors for it, so I'll be leaving that for tomorrow

Anyways, this is what I got so far

<img width="660" height="421" alt="image" src="https://github.com/user-attachments/assets/9de71415-7721-4954-876c-e14860d712df" />


**Total Time Spent:** 2h

# 7/27/26: Clearing up stuff

I just realized that the part number on the board isn't the same one as on the part page I was on, which accounts for the discrepency

I do feel bad that I emailed them about this (though I still wanna know what use the UART has)

I still kinda don't get why they have this under the design resources for a clearly different port

<img width="1759" height="886" alt="image" src="https://github.com/user-attachments/assets/dc22cd3f-ce04-4936-9a57-0c3c4465b78e" />

**Total Time Spent:** 5m

# 7/27/26: USB2532 Symbol

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
 
**Total Time Spent:** 1h 50m

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
