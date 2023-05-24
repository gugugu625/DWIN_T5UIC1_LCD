# DWIN_T5UIC1_LCD

## Python class for the Ender 3 V2 LCD runing klipper3d with OctoPrint / Moonraker 
## This Repo is running on Orangepi zero2, you need to install OPi.GPIO Library.
### For Orange Pi Zero2, OrangePi Zero 2, Orange Pi3, and Orange Pi Lite2:
  `https://github.com/NadavK/OPI.GPIO`

https://www.klipper3d.org

https://octoprint.org/

https://github.com/arksine/moonraker


## Setup:

### If you already open the API socket, you can edit the socket in printerInterface.py line 256.

## For OrangePi zero2
### Open UART5 according to the reference manual, Connect TX,RX to UART5. Skip To "Enabling Klipper's API socket" and change LCD_COM_Port in run.py

## The following is the installation process provided in the original article:
### [Disable Linux serial console](https://www.raspberrypi.org/documentation/configuration/uart.md)
  By default, the primary UART is assigned to the Linux console. If you wish to use the primary UART for other purposes, you must reconfigure Raspberry Pi OS. This can be done by using raspi-config:

  * Start raspi-config: `sudo raspi-config.`
  * Select option 3 - Interface Options.
  * Select option P6 - Serial Port.
  * At the prompt Would you like a login shell to be accessible over serial? answer 'No'
  * At the prompt Would you like the serial port hardware to be enabled? answer 'Yes'
  * Exit raspi-config and reboot the Pi for changes to take effect.
  
  For full instructions on how to use Device Tree overlays see [this page](https://www.raspberrypi.org/documentation/configuration/device-tree.md). 
  
  In brief, add a line to the `/boot/config.txt` file to apply a Device Tree overlay.
    
    dtoverlay=disable-bt

### [Enabling Klipper's API socket](https://www.klipper3d.org/API_Server.html)
  By default, the Klipper's API socket is not enabled. In order to use the API server, the file /etc/default/klipper need to be updated form

    KLIPPY_ARGS="/home/pi/klipper/klippy/klippy.py /home/pi/printer.cfg -l /tmp/klippy.log"
To:

    KLIPPY_ARGS="/home/pi/klipper/klippy/klippy.py /home/pi/printer.cfg -a /tmp/klippy_uds -l /tmp/klippy.log"

### Library requirements 

  Thanks to [wolfstlkr](https://www.reddit.com/r/ender3v2/comments/mdtjvk/octoprint_klipper_v2_lcd/gspae7y)

  `sudo apt-get install python3-pip python3-gpiozero python3-serial git`

  `sudo pip3 install multitimer`

  `git clone https://github.com/odwdinc/DWIN_T5UIC1_LCD.git`


### Wire the display 
  * Display <-> Raspberry Pi GPIO BCM
  * Rx  =   GPIO14  (Tx)
  * Tx  =   GPIO15  (Rx)
  * Ent =   GPIO13
  * A   =   GPIO19
  * B   =   GPIO26
  * Vcc =   2   (5v)
  * Gnd =   6   (GND)

### Run The Code

This Code is for OrangePi, if you are using other boards, please follow the original repository.
Enter the downloaded DWIN_T5UIC1_LCD folder.
Make new file run.py and add

```python
#!/usr/bin/env python3
from dwinlcd import DWIN_LCD
import OPi.GPIO as GPIO

GPIO.setboard(GPIO.H616)
GPIO.setmode(GPIO.BOARD)
encoder_Pins = (13, 15)
button_Pin = 11
LCD_COM_Port = '/dev/ttyS5'
API_Key = 'xxxx'

DWINLCD = DWIN_LCD(
	LCD_COM_Port,
	encoder_Pins,
	button_Pin,
	API_Key
)
```

Run with `python3 ./run.py`

# Run at boot:

	Note: Delay of 30s after boot to allow webservices to settal.
	
	path of `run.py` is expected to be `/home/pi/DWIN_T5UIC1_LCD/run.py`

   `sudo chmod +x run.py`
   
   `sudo chmod +x simpleLCD.service`
   
   `sudo mv simpleLCD.service /lib/systemd/system/simpleLCD.service`
   
   `sudo chmod 644 /lib/systemd/system/simpleLCD.service`
   
   `sudo systemctl daemon-reload`
   
   `sudo systemctl enable simpleLCD.service`
   
   `sudo reboot`
   
   

# Status:

## Working:

 Print Menu:
 
    * List / Print jobs from OctoPrint / Moonraker
    * Auto swiching from to Print Menu on job start / end.
    * Display Print time, Progress, Temps, and Job name.
    * Pause / Resume / Cancle Job
    * Tune Menu: Print speed & Temps

 Perpare Menu:
 
    * Move / Jog toolhead
    * Disable stepper
    * Auto Home
    * Z offset (PROBE_CALIBRATE)
    * Preheat
    * cooldown
 
 Info Menu
 
    * Shows printer info.

## Notworking:
    * Save / Loding Preheat setting, hardcode on start can be changed in menu but will not retane on restart.
    * The Control: Motion Menu
