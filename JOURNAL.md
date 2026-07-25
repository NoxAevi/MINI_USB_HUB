---
title: "Mini USB Hub"
author: "NoxAevi"
description: "Mini USB Hub mainly used as a demo for USB PD pass-through and swapping the host port w/ the charge/data port"
created_at: "2026-07-25"
---

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
