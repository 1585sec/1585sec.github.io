---
layout: blog_post_standard
title: Hardware Reversing Basics: Finding a UART and Getting a Shell
category: HowTo
tags: [hardware, reverse engineering]
---

Heads up! This is a companion to the YouTube video, if you're just reading this standalone, you'll miss a lot of context.

### Tools and Equipment

|What was it?|How was it used?| Notes|
|----------------|-------------|-------------|
|D-Link 931L | Target | Almost identical to the 932, bought mine on Amazon|
|FCC.gov<br>FCCID.io|Device Recon: Viewing internal photos from FCC filings|The 931-L’s FCC ID is KA2CS931LA1|
|* Shim made from an aluminum can<br>* 1mm guitar pick<br>* Phillips head screwdriver (PH0)|Cracking the case||
|Multimeter| * Finding ground<br>* Measuring DC voltage levels on the UART|I used an Innova 3320, but any will do|
|*3.3v FTDI UART<br>* Kali Linux VM<br>* Jumper Wires<br>*	Grabber Probes<br>*	Header Pins<br>* USB cable| Communicating with the UART|I used a SparkFun DEV-09873 FTDI|
|Misc. | * Benchtop Vise <br> * Switched outlet| I used a Panavise 201 "Junior"|


### Walkthrough

#### 1.) Confirm the likely presence of a UART from internal FCC photos, then buy the camera

Early-stage recon was conducted using internal photographs found in the FCC license filing for the camera (FCC ID: KA2CS931LA1). A 4-pin JST connector was observed that appeared to be a likely UART (serial interface). The camera was subsequently bought on Amazon.

#### 2.) Crack the case and prep the board

The camera’s case is held together with 8 plastic clips, which were popped using an aluminum shim (cut from a can) and a 1mm guitar pick. With the case open, the single Phillips head screw holding the board to the front half of the case was removed using a PH0 driver.

With the board free, it was mounted into a desktop vise, with the camera lens facing up. The camera’s power cord was plugged into a switched outlet, and with the switch turned off, the power supply was connected to the camera.

During setup it was noted that the 4-pin JST connector seen in the FCC photos was not present on the unit under test. However, an underlying row of four plated through holes (TPHs) was present and became the focus of the test.

#### 3.) Verify Ground and Voltage Levels

A multimeter was used to verify ground and measure voltage for the remaining TPHs. This was done to both identify the voltage level of the UART (3.3V and 5V are common), and as an 
exercise in hypothesizing which was “talking” vs. “listening”. 

|TPH|Multimeter Setting|Observation|Conclusion|
|---|------------------|-----------|----------|
| G | Continuity (Power Off) | Has continuity with the device’s ground plane. | G = Ground |
| R | DC Voltage (Power On) | Pulled low during boot (0V) | R = Serial Input |
| T | (DC Voltage (Power On) | jitters around 3.27V during boot | T = Serial Output |
| V | (DC Voltage (Power On) | Pulled high during boot (3.31V) | V = Voltage (likely supply)|

Based on the above a 3.3V UART seems highly likely.


#### 4.) Wire up the FTDI

A row of 4 header pins were placed into the TPHs (I didn’t solder them, and it worked, but I got really lucky I suggest soldering them to prevent any flakiness). Grabber probes were then used to attach jumper wires to the pins, which were in turn connected to the FTDI. Finally, the FTDI was connected to the host laptop via USB.

![]({{ site.baseurl }}/img/blog/ftdi-wiring.jpg)

#### 5.) Verify the FTDI and find the device TTY

`lsusb`<br>
`ls /dev|grep USB`<br>

The lsusb command was run on the Kali VM to it recognized the FTDI had been connected via USB. The device TTY was found by grepping the /dev directory for “USB”. Because it was the only attached USB device, it was the only returned result.

#### 6.) Open a serial connection

`screen /dev/ttyUSB0 115200`<br>
`screen /dev/ttyUSB0 57600`<br>


The screen command was used to open a serial connection to the FTDI at /dev/ttyUSB0. Because the baud rate for the UART wasn’t known, guesses were made using a short list of common rates (115200 was tried and failed, but 57600 worked). To ensure a comprehensive capture of the boot process, make sure to open the connection before powering on the camera.

Heads up! Detaching from the screen command isn’t intuitive; it’s ctrl+A+D. If you exit a screen session non-gracefully (like just closing the window) it may leave the device in a bad state- see the troubleshooting section if this happens to you.

#### 7.) Record console messages and verify the shell

`pwd`<br>
`ls -l`<br>
`ps -ef`<br>


During boot the camera printed a significant amount of information to the serial port. That information was captured in a scrollback buffer and copied to a text file for offline review. Once the boot up messages subsided (approx. 3 minutes after power-up) the interactive shell was verified by viewing the contents of the present working directory (/) and viewing a list of running processes with ps -ef.



###Troubleshooting Tips

| Problem | Things to consider |
|-------|--------|
|Laptop/VM doesn’t recognize FTDI| * Check USB cable<br>* Verify/Update driver<br>* If using a VM, verify control of USB device is granted to VM (vs. host system)<br>* Unplug, reboot, replug|
|Can’t open a serial connection due to “device busy” errors|* Screen is notorious for hanging onto the device, especially if it’s not detached gracefully.<br>* Check for fouled screen processes that may be hanging onto the device. Worst case, a reboot usually fixes it.<br>* To prevent this, make sure to detach from connections with _ctrl+A+D_|
|Can open a serial connection but nothing happens (blank)|* Check the light status on the FTDI. When you bang on the keyboard, the light should flash. If it doesn’t, likely an issue between you and the FTDI, rather than FTDI to camera.<br>*Check your wires, and make sure the camera is powered on.|
|Can open a serial connection but output is garbage|* Check the baud rate<br> * Triple-check (srsly) the ground wire, especially if “everything was working fine before”|
|Camera not booting or otherwise being weird|* You may a short in your wires/on the board.<br>* Check the connections and power cycle<br>* If things get really funky you might want to do a factory reset (see instructions)<br>* The hardware itself might have been damaged at some point- fixing that is beyond this guide.|
