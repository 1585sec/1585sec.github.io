---
layout: blog_post_video_ftdi
title: Hardware Reversing to find UART and shell
---

It's been a long time, but I finally posted a follow up video! In this one we'll dip a toe into hardware reversing by finding a UART (serial port) and using it to get shell access on the device. If you've never done anything with hardware this is a great place to start, or at least get some initial exposure to the concept. 

For those who want to try this on there own I've posted a list of tools/equipment, a set of quick reference steps from the walkthrough, and finally some troubleshooting ideas in case you hit any snags.

### Tools and Equipment

Here's a breakdown of what we used in the video. You don't need specific makes/models of anything except the 931-L; whatever equivalent tools you have will probably do the trick.

|What was it?|How was it used?| Notes|
|----------------|-------------|-------------|
|D-Link 931L | Target | Almost identical to the 932, bought mine on Amazon|
|FCC.gov<br>FCCID.io|Device Recon: Viewing internal photos from FCC filings|The 931-L’s FCC ID is KA2CS931LA1|
|* Shim made from an aluminum can<br>* 1mm guitar pick<br>* Phillips head screwdriver (PH0)|Cracking the case||
|Multimeter| * Finding ground<br>* Measuring DC voltage levels on the UART|I used an Innova 3320, but any will do|
|*3.3v FTDI UART<br>* Kali Linux VM<br>* Jumper Wires<br>*	Grabber Probes<br>*	Header Pins<br>* USB cable| Communicating with the UART|I used a SparkFun DEV-09873 FTDI|
|Misc. | * Benchtop Vise <br> * Switched outlet| I used a Panavise 201 "Junior"|

### Walkthrough: Quick Reference

#### Finding Internal Photos in FCC Filings

- We needed to know if the 931-L had the kind of attack surface we were looking for (specifically a UART).
- The FCC filing for the 931-L is a matter of public record, and contains internal photos of the camera.
- It's a lot easier to find a device's FCC filing is you know the FCC ID assigned to the device (KA2CS931LA1).
- To see the filing and photos from the video browse to the [FCC's search tool](https://www.fcc.gov/oet/ea/fccid) and enter the FCC ID of the 931-L: KA2CS931LA1
- If you want to find the filing for a device but don't know the FCC ID, I recommend using [FCCID.io's free search tool](https://fccid.io/search.php) (it's a lot faster/easier than trying to pry it out of the FCC's search tool).

#### Cracking the Case and Prepping the Board

- The camera's case is held together with ~8 plastic clips, which were removed using a shim cut from an aluminum can and a 1mm guitar pick. There are some great kits out there for cracking cases, but I wouldn't recommend investing in one for your first project.

- 


To see what the inside of the camera looked like before buying it we checked 
would be a good candidate for the project a We looked at internal photos of the 931-L to 




#### Wiring up the FTDI

![]({{ site.baseurl }}/img/blog/2019/ftdi-wiring.jpg)

Followed by text?
