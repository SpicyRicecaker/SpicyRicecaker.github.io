---
layout: "../../layouts/BlogPost.astro"
title: "A Fix for the FX-Audio DAC-X6"
description: ""
pubDate: "January 6 2024"
# heroImage: "/placeholder-hero.jpg"
---

## The Procedure

Assuming you are on Windows, the steps are as follows:

1. Go to device manager
	- under `sound, video, and game controllers`, uninstall `dac-x6`.
2. Unplug the USB from the DAC
3. Power off the DAC
4. Unplug the power from the DAC.

Now pretend you are trying to setup the DAC for the first time

5. Plug the power adapter into the DAC
6. **Plug the USB into the DAC** 
7. Power the DAC on

## Why Does This Work? 

I think Windows first installs the audio drivers for the DAC-X6 when the DAC-X6 is first powered on while plugged in. Windows then takes note of the sound level of the powered on DAC-X6 as the "default" sound level. However, if the user turns off the DAC-X6, and Windows starts up when the DAC-X6 is not powered on, Windows sometimes still recognizes the unpowered DAC-X6 and believes the DAC-X6 to be turned on. Thus Windows expects a highly amplified signal to be normal volume, so when an unpowered DAC-X6 sends a low-amp signal, the actual output volume is nonexistant.

By uninstalling the sound controller driver and resimulating the setup of the DAC-X6, we again force Windows to recognize the powered on version of the device as the actual, working device.

Though this is just a theory.

## Description of the Problem

Occasionally, after unplugging or turning the DAC-X6 off, the DAC-X6 appears to stop amplifying audio. This requires the user to turn the volume knob up a significant amount to achieve the same sound levels as a working, out-of-the box DAC-X6. However, this solution causes two big issues: first, the sound coming through is unamplified, making the sound quality worse. The DAC essentially becomes gratuitous black box sitting on the user's desk. Second, and importantly, by turning the volume knob up, what is actually happening is that the resistance carrying the audio signal is lowered. This causes the dreaded "popping" sound when the DAC detects that an audio stream has stopped playing to be made particularly audible. This is a huge problem, because any time the user pauses a youtube video, or someone stops speaking in a video, or an ability with a sound effect is used a videogame, etc., the popping sound is heard. This is enough to drive anyone crazy.

Fortunately, the procedure above seems to resolve this particular issue 100% of the time.