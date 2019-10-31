---
layout: blog_post_video_ftdi
title: Hardware Reversing to find UART and shell
---

It's been a long time, but I finally posted a follow up video! In this one we'll dip a toe into hardware reversing by finding a UART (serial port) and using it to get shell access on the device. If you've never done anything with hardware this is a great place to start, or at least get some initial exposure to the concept. 

For those who want to try this on there own I've posted a list of tools/equipment, a set of quick reference steps from the walkthrough, and finally some troubleshooting ideas in case you hit any snags.

### Tools and Equipment

Target Device: D-Link 931-L Camera

<table>
	<tbody>
		<tr>
			<td markdown="span">Target Device</td>
			<td markdown="span">D-Link 931-L</td>
		</tr>
		<tr>
			<td markdown="span">Cracking the Case</td>
			<td markdown="span">* Aluminum Can Shim<br> * 1mm Guitar Pick<br> * PH0 Screwdriver</td>
	</tbody>
</table>

To crack the case:
* Shim made from an aluminum can
* 1mm guitar pick
  * This is a sub list item
* Back to list items again

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


**Finding Internal Photos in FCC Filings**

We needed to know if the 931-L had the kind of attack surface we were looking for (specifically a UART). To get a peek at the inside of the camera before buying it we looked at the internal photos included in the camera's FCC filing.

To see the filing and photos from the video browse to the [FCC's search tool](https://www.fcc.gov/oet/ea/fccid) and enter the FCC ID of the 931-L: KA2CS931LA1

If you want to find the filing for a device but don't know the FCC ID, I recommend using [FCCID.io's free search tool](https://fccid.io/search.php) (it's a lot faster/easier than trying to pry it out of the FCC's search tool).


**Cracking the Case and Prepping the Board**

The camera's case is held together with ~8 plastic clips, which we removed using a shim cut from an aluminum can and a 1mm guitar pick (there are great kits available for this kind of thing but I wouldn't recommend investing in one for your first project).

We put the board into a benchtop vise to hold it steady, then plugged the power supply into a switched outlet. This allowed us to power the device up and down easily, without fumbling around to pull the cable out. If you don't have a switched outlet, a surge protector or a lamp cord switch will do the trick just fine.

A bit out of order here, but one last board prep item: we also placed a row of 4 header pins into the row of plated through holes (TPHs). We didn't solder it, but we got really lucky there. If you're doing this on your own I'd recommend soldering them in, if only to remove a possible point of failure. It's also worth noting that the pin size and pin pitch (distance between pins) is smaller than the more commonly sized connectors out there. If you run into problems, consider just soldering jumper wires to the PTHs themselves (to save time remember you don't actually need the one marked V!)

**Finding the UART**

The JST connectors shown in the FCC photos weren't on our camera, but the four plated through holes (PTHs) that the JST connector used where still present. As shown below the reference designator on the board was J2, and PTHs themselves were labeled "G", "R", "T", and "V". 

![]({{ site.baseurl }}/img/blog/2019/UART-on-board.jpg)


**Testing the UART**

It seemed likely that the labels on those PTHs stood for Ground, Receive, Transmit, and some kind of voltage, but we ran a hasty survey to be sure. We began by checking for Ground on "G" by using the multimeter to run a continuity test between "G" and a spot on the board where the ground plane was exposed.  With ground confirmed we switched the multimeter to measure DC voltage, powered on the device and checked the voltage levels on "R", "T", and "V". Observations and conclusions from the survey are summarized below.


<table>
	<colgroup>
		<col width="20%" />
		<col width="50%" />
		<col width="30%" />
	</colgroup>
	<thead>
		<tr class="header">
			<th>Label</th>
			<th>Observations</th>
			<th>Conclusions</th>
		</tr>
	</thead>

	<tbody>
		<tr>
			<td markdown="span">**G**</td>
			<td markdown="span">Continuity with device's ground plane.</td>
			<td markdown="span">G = Ground</td>
		</tr>

		<tr>
			<td markdown="span">**R**</td>
			<td markdown="span">Pulled down when device running.</td>
			<td markdown="span">R = Receive (Serial In)</td>
		</tr>

		<tr>
			<td markdown="span">**T**</td>
			<td markdown="span">Jitters ~3.3V when the device is booting</td>
			<td markdown="span">T = Transmit (Serial Out)</td>
		</tr>
		
		<tr>
			<td markdown="span">**V**</td>
			<td markdown="span">Pulled up to 3.31, stays there.</td>
			<td markdown="span">V = Voltage (supply)</td>
		</tr>

	</tbody>

</table>


_Heads Up!_ This section talks about using a multimeter. If you've never used one or are just a bit rusty, there are loads of tutorials out there. SparkFun's [How to Use a Multimeter](https://learn.sparkfun.com/tutorials/how-to-use-a-multimeter/) is solid and includes walkthroughs of the continuity and voltage measurements we did in the video.




#### Wiring up the FTDI

![]({{ site.baseurl }}/img/blog/2019/ftdi-wiring.jpg)

Followed by text?

