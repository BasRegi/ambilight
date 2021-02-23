# Ambilight System

This project involves building an ambilight system similar to that of the Philips Hue Play HDMI Sync Box (https://bit.ly/3pI3A1v) but at a much lower price point. 
The ambilight system requires some form of external entertainment system from which the video output can be captured to be able to sync the lights with. 
I currently use a Chromecast for this project but this in theory could be used for anything like games consoles or any other media players 
(you could possibly even use a HDMI splitter to switch between multiple inputs).

Please note this tutorial is based on the myriad of tutorials already out there. 
Be sure to check out the Useful Links section at the end of this tutorial for all the tutorials I used to carry out this project.

Below is the list of the main components required for this project:
- WS2812B LED Light Strip
- 5V 10-15A Power Supply (Preferably with barrel connector and adapter)
- Raspberry Pi Zero W (Preferably with pre-soldered headers)
- Micro-SD Card and Micro-SD Card Reader
- Video Capture Card with Loop
- HDMI Cable
- 2 x Micro-USB to USB-A Cable
- Dupont Wires
- Jumper Wires
	
I have included the links for the things that I specifically bought, however you may be able to find better/cheaper options with more up to date research.

Here is a high-level system diagram of what we are trying to build:

Note: The Smart Socket is an optional extension to be able to turn the ambilight system on and off easily. 

## Setting Up the Raspberry Pi:
1. Install the Raspberry Pi Imager (https://www.raspberrypi.org/software/) software on a computer.
2. Plug in the Micro-SD Card using the Micro-SD Card reader into the computer.
3. Install the Raspberry Pi Lite OS image on to the Micro-SD Card.
4. Open up the Micro-SD Card folder
5. Add an empty file with the name `ssh` and no extension
6. Add a file called wpa_supplicant.conf file with the following content 
		`
		country=gb
		update_config=1
		ctrl_interface=/var/run/wpa_supplicant
		network={
		       scan_ssid=1
		        ssid="WIFI_NETWORK_NAME_HERE"
        psk="WIFI_PASSWORD_HERE"
    }`
	Make sure to replace the ssid and psk fields with the correct ssid and password for your wifi network
7. Unplug and reboot the pi and you should now see it appear in your router as a connected device
8. While your pi is powered and connected, open up a command line window on your computer and type in the following command:
		`ssh pi@<ip-address>` *(Replace the ip-address with the ip address of the pi that you find in Step 7)*
9. This should prompt you to enter a password. By default the password for the raspberry pi is `raspberry`, so go ahead and type this in. You should now have a terminal interface on the pi.
10. Navigate to the following web page and click on the latest release: https://github.com/hyperion-project/hyperion.ng/releases
11. Scroll to the bottom of the page and copy the link for the "Linux-armv6l.deb" version of the release.
12. On the terminal type in the following command and replace <link> with the link copied in Step 11
		`wget <link>`
13. From the hyperion release page, copy the title of the link that you copied previously (e.g. Hyperion-2.0.0-alpha.6-Linux-armv6l.deb). 
14. Then type the following command into the pi terminal, replacing <title> with the title copied in Step 13:
		`sudo dpkg -i <title>`
15. Enter the following commands into the pi terminal:
		`sudo systemctl disable --now hyperiond@pi`
		`sudo systemctl enable --now hyperiond@root`
16. Once this has been done, reboot your pi and verify that it is running by navigating to <ip-address-of-pi>:8090 on a web browser on your computer. This should load up the Hyperion configuration page.

## Wiring up the LED Strip:
The WS2812B LED Strip has 3 inputs on a female connector like in the image below:

Out of the 3 inputs, the red wire input is for the 5V power supply, the white wire input is for the ground and the green wire input is the data line to control the LED's. You will connect up the red and the white inputs to the power supply directly using jumper cables and then use a Dupont wire to connect the green input to GPIO18 on the pi. You will also need a jumper wire from a GND on the pi into the GND port of the power supply. I chose to wire it up so that the pi is also powered using the power supply, and I did this by stripping the USB-A end off of Micro-USB cable and then connecting that to the 5V and GND ports on the power supply. The wiring diagram below shows the full set up:

This should provide power to the pi and enable the pi to communicate with the LED light strip. To provide video input to the pi, connect up the Chromecast to the video capture card and then connect the video capture card to the second micro-usb port on the pi. 
Ensure that the Video capture card is also connected to power.
You can then connect a HDMI cable from the video capture card to your TV to view the video being shown by the Chromecast. 

## Configuring Hyperion:
Now configuring Hyperion can be quite an involved task and can require some fine tuning before it is perfect. As such I am not going to go in depth into how to configure Hyperion, but instead can point you towards the hyperion configuration page:
https://docs.hyperion-project.org/en/user/Configuration.html

But here are a few things to make sure is configured correctly:
- Under Configuration > LED Hardware > LED Controller:
  - Ensure that the Controller type is set to ws281x
  - RGB Byte order will have to be experimented with (for the light strip I linked above I used GRB) 
  - Maximum LED Count should be the number of LED's you plan to use
  - GPIO number should be 18
- Under Configuration > LED Hardware > LED Layout:
  - Ensure that the correct number of LED's are listed for Top, Bottom, Left and Right
  - Input position can be used to move the starting position of the LED's

*If you have any issues or queries, please feel free to reach out to me at 08regib@googlemail.com*
