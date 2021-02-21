# SpeedRacer - 4 Lane with Phototransistors

## What is SpeedRacer? 

SpeedRacer is a free, Open Source track timer for Pinewood Derby tracks. It was a homebrew job for my PrincTech course, in which
we built a pinewood derby track to test car aerodynamics. 

SpeedRacer serves a single purpose, which is to keep time on each car. SpeedRacer accomplishes this incredibly well, too: 
Time data is exposed in realtime via a highly optimized web interface, accessible via Wi-Fi. 

This version has been modified to run 4 lanes and use phototransistors for the finish sensors.

## How does SpeedRacer work? 

SpeedRacer monitors sensors mounted on your Pinewood Derby track in order to determine times for each car. This data is then exposed via a web interface on a privately broadcast WiFi network that you can connect to with any device, mobile or desktop. 

## Installing and using SpeedRacer

Step one is to prepare your [Raspberry Pi Model B Version 3](https://www.raspberrypi.org/products/raspberry-pi-3-model-b/) by flashing the [SpeedRacer 4 Lane system image](https://drive.google.com/file/d/1sHoFOG6Qr3VXYvG6kzO1O6hSGpbitpeV/view?usp=sharing) to an SD card. You'll need an SD card with at least 2GB of storage. To do this, extract the image file from SR_4Lane.zip and refer to the [Raspberry Pi OS Installation Guide](https://www.raspberrypi.org/documentation/installation/installing-images/)

Step two is to wire the appropriate sensors to your track, and connect them to the corresponding pins on your Pi. You may want to refer to the Fritzing diagrams in the Wiring Schematics directory before doing this. Essentially, you'll need to wire it like so: 

- The RELEASE bumper switch is CLOSED when the release mechanism is UP (e.g. holding cars at the top of the track). Connect to GPIO6
- The RESET button may be pushed to manually reset the track or application state. Connect to GPIO12
- The LED INDICATOR relays various information about the track and system state, which may be useful, but is not critical. Wire to GPIO5

Wire sensors at the end of each lane between 3.3v and the following GPIO pins: 
- Lane 1: GPIO13
- Lane 2: GPIO19
- Lane 3: GPIO26
- Lane 4: GPIO16

I ended up using some mystery phototransistors in series with 18k ohm resistors to trigger when something blocked the light from a LED striplight mounted in a gantry above the finish line.  WARNING:  if using a poorly regulated LED light source, the light may actually be flickering faster than the eye can see causing the sensors to trip.



Once everything has been flashed and wired appropriately, connect the Pi to power. The system will broadcast a wireless network named "SpeedRacer", which you can connect to with the password `chimchim`. Once connected, visit http://speedracer.local (or http://192.168.1.1) in your browser to view the scoreboard. You're off to the races!

## Modifying SpeedRacer

The system is wide open to modification, simply connect to the SpeedRacer WiFi network and `ssh pi@192.168.1.1`. From there, 
you can use the default password (`raspberry`) to modify code and system settings as you see fit. 

System settings are located in the following places: 

- Hostapd configuration (wireless AP configuration): /etc/hostapd
- SpeedRacer API Server: /root/speedRacer/main.js
- SpeedRacer Wiring Diagnostics Checker: /root/speedRacer/diag.js
- SpeedRacer Web Interface: /var/www/html/index.html

Before making modifications, it is recommended that you swutch to the root user and use extreme caution. 

## Debugging

For instructions on how to get "under the hood", see **Modifying SpeedRacer**.  

To restart the main application process, you may either power cycle the pi or run `pm2 restart 0` as root. 

To check your wiring, run the following commands as root:
- `pm2 stop 0`
- `nodejs /root/speedRacer/diag.js`

Press and release the switches you've wired in, and the diagnostics program will tell you which switches it thinks you're hitting. 
