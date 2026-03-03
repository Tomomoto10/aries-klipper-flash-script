WORK IN PROGRESS

Project fork of goopsie/aries-klipper-flash-script with extras from myself, other usefull sources are listed.

Adding extra config changes for LEDS, MACROS, leveling, slicer profiles, more to come! 

NOT COMPLETE, USE AT POWN RISK. will work in a basic sense but will need extras to work in a more complete way. 




PRE-INSTALL


-Install SRECORD, for converting klipper.bin file to work on the Voxelab (Works on Windows and Linux)

	*Windows: if installing srecord to a non-default directory, "to-hex.bat" will not work out of the box.

-Install Klipper host software to a Linux device of your choosing eg. Raspberry Pi.

	*KIAUH installer Recommended

-Connect printer to Klipper device via UART pins on main board (See .png) 

	Raspberry Pi: Wire Rx on Printer to Tx pin on Pi, Wire Tx on Printer to Rx pin on Pi (See *.png)

	USB Serial Converter: Wire Rx on Printer to Tx on Serial Adapter, Tx on Printer to Rx on Serial Adapter, no other pins needed.

	Onboard ch340 Serial converter: Can be used by connecting usb cable to pins on LCD Board (See *.png)

-Format USB drive and Download this repo as .zip, extract all files to a USB drive, Make sure the usb drive shows up in your printer's menus before continuing.



INSTALL


-On klipper host run "make menuconfig" with settings shown in makemenuconfig.png

	*(LCD) if using onboard ch340 Serial for usb connection
	
	*(PADS) if using rx-tx connections
	
-Use "Internal Clock" for Clock Reference

*If you cant get connection you will need to use 12Mhz clock
	-Modify klipper/src/stm32/Kconfig with the following:

    #default 128000000 if MACH_N32G45x # old line
    default 144000000 if MACH_N32G45x  # new line


-Run "make" on klipper host

-Drag resulting klipper.bin onto root of USB drive.

-Convert klipper.bin to firmware.hex

	Windows: run "to-hex.bat" file also located on USB drive.
	
  	Linux:  open terminal in root of USB drive, run: srec_cat klipper.bin -binary -offset 0x08000000 -output firmware.hex -Intel. 
	*You should see a new "firmware.hex" file was created on the USB drive.

-Turn off printer, if not off already. 

-Plug drive into printer, turn printer on.

-You will see progress images come up on-screen, firmware.hex file will be automatically flashed to the mainboard via voxelab's built in programming tool.

-On completion you will recieve a promt to connect your klipper host to the printer
	
*Suggested you unplug the ribbon cable connecting the main board to the screen board as this cant be used anymore. 

	-Screen can be removed and use KlipperScreen on Raspberry Pi Screen in place of original (Optional)
	
	-Stock USB port can be re-wired to a usb plug and connected to Pi (Optional)

-If using Raspberry Pi, you should already have rx-tx connected.

-If using other linux device, connect to printer via USB Serial Converter. 

-On another device, Open your chosen webUI (Fliudd/Mainsail) via the IP listed in the KIAUH UI during install, it wont have connection to your printer yet. 

	-Copy the printer.cfg located in this repo and replace the defaulkt on on the printer using the web interface (Fluidd/Mainsail) 
	
	-Restart Klipper
	
	-Printer should now be connected to Klipper

*If your printer isnt automatically found you may need to update the printer.config with the serial port your printer is using.

	Linux: ls /dev/serial/by-id/* 
	
	Windows: 

Change below in your printer.cfg, replace * with the device name show from command above 

	[mcu]
	serial: /dev/serial/by-id/*

-You should now have basic functionality of the printer, complete Leveling (Below) and any other optional extra steps to get extra functiality before printing for the first time.

	
LEVELING (NOT TESTED)

-Use the front bed screws to make any adjustments to pre level the bed as best as possible, have the front slightely lower than the rear if possible.

-On the Klipper terminal run Z_ENDSTOP_CALIBRATE and follow bed leveling using a piece of paper as per *Klipper Instructions
Procedure is 0: Rear Stationary screw, 1: Left adjustable screw, 2: Right adjustable screw



BLTOUCH / PROBES (NOT TESTED)


-BL touch or other clones can be plugged directly into the hotend board via the 5 pin socket 

-Copy below lines into your printer.config
	*

-Other sensors can be used but are not covered in this guide, see pinout if needed (*.png)


MACROS


Macros in the provided printer.config will provide basic functionality. To gain further control, you will need to add additional macros, you can use the macros provided in *.cfg 



LEDS 
-You can add additional ARGB leds eg. for chassis lighting 

	-See *.png for reference images 
	-Solder the "Data" line from the ARGB led strip to r99 on the N32 mainboard 
	-Solder 5v and Ground wires from ARGB led strip to LCD connector pins 

-The following lines in your printer.cfg adjust the control, adjust as needed for the led color range you want. 

	[neopixel chassis_lights]
	pin: PA15
	chain_count: 1   # NUMBER_OF_LEDS_HERE
	color_order: GRB # depends on strip
	initial_RED: 1   # if you want
	initial_GREEN: 1 # if you want
	initial_BLUE: 1  # if you want

-Led brightness and Color can now be adjusted in your chosen interface (Fluidd/Mainsail/KlipperScreen)


UNINSTALL
-Reverting to stock firmware is simple, just follow steps below

	-Un-solder uart pins from N32 mainboard, plug back in screen cables and any others you may have unplugged
	-Turn the printer as normal.
	-You'll get a prompt on-screen stating "The firmware failed to update. Press OK to retry", with an "OK" button.
	-Once you press the button, the LCD board will flash the original firmware to the stm32 board, you'll be able to use it as normal.



REFERENCES

Couldnt have done without, Original project owner, infinte help on reddit.

GOOPSIE (Git) OopsieGoopsie (Reddit)

https://github.com/goopsie/aries-klipper-flash-script

https://www.reddit.com/r/VoxelabAries/comments/1lna0p0/got_klipper_flashed_to_my_aries/
___________________

Bed Leveling Macros and Configs

@LaszloLitaus_2081172 (Printables.com) 

https://www.printables.com/model/881037-voxelab-aries-converter-plates/files

