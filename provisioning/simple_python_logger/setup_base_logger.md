# Set up a base logger

Here are the instructions for setting up an Ardusimple GNSS base station with a Raspberry Pi to log raw satellite observations over serial (in UBX format, which we'll later convert to RINEX format for processing) for Precise Point Positioning.

This is useful to get a very precise stand-alone position, either for direct use, or to provide a base station with a good absolute reference. The basic idea is to run an ArduSimple for at least 2.5 hours (4 is better) and log all of the raw satellite data, then send it to Natural Resources Canada (or another PPP provider) for processing.

You will:
- Set up jumper cables for serial transmission between the ArduSimple and a Raspberry Pi
- Configure the ArduSimple to output raw satellite observations on its UART1 serial port at 460800 baud
- Configure a Raspberry Pi to read those observations and log them to a file on its SD card for retrieval and processing later.

Ok, let's get to it.

## Wire it up
jumper cables are required to log the raw data from UART1. For this we need 5 Female-Female connectors.

Only connect jumper cables if the device is unplugged from the power supply!

Looking at the raspberry pi holding it in front of you with the SD card slot on the top, place all 5 cables on the right side of the header pins from the top. These are:
-	5V
-	5V
-	GROUND
-	TX
-	RX

Connect these to the GNSS receiver:
-	5V_IN
-	IOREF
-	GND
-	RX
-	TX

So it should look like this:
-	5V - 5V_IN
-	5V - IOREF
-	GROUND - GND
-	TX - RX
-	RX - TX

![Jumper cable setup](/images/Ardusimple_RPi0_jumper_wiring_schematic.jpeg)

![Jumper cable setup example photo](/images/Ardusimple_RPi0_jumper_wiring_photo.jpeg)

Make really sure that these connections are correct! If these ones are connected wrong, then the GNSS receiver may get destroyed. Note that for GND at the GNSS receiver are two pins. You can use either one of them, but make sure you are not using the ‘empty’ pin on the top.

## Configure the ArduSimple

- Make sure your ArduSimple is running the stock configuration. If you are not sure, you can use U-Center to configure it with the file in ```ublox_configurations/stock_ardusimple_base.txt```. [Here's how you do that]()
- Set up the ArduSimple base to output raw UBX on serial at 460800 baud [note to self: Ivan, add more instructions()

## Set up the Pi as a serial logger

- Flash a Pi SD card with the latest Raspberry Pi OS Lite from [here](https://www.raspberrypi.org/software/operating-systems/) (I use [Balena Etcher](https://www.balena.io/etcher/) for this). 
- Add empty ```ssh``` file and appropriate ```wpa_supplicant.conf``` [like this](https://www.raspberrypi.org/documentation/configuration/wireless/headless.md) with wifi info to boot partition on that SD card. Put it in the Pi and start it up.
- SSH into the parent pi ```ssh pi@raspberrypi.local``` with password ```raspberry```.
- Change the default password ```passwd```, update, upgrade ```sudo apt update && sudo apt upgrade -y``` and install Git ```sudo apt install git -y```.
- Clone this repo ```git clone https://github.com/localdevices/RTK_GNSS```
- Copy the logger script into the home folder ```cp RTK_GNSS/provisioning/simple_python_logger/log_ubx.py /home/pi```
- Copy the service file so that Systemd will start logging on startup ```sudo cp RTK_GNSS/provisioning/simple_python_logger/gnss_base_logger.service /etc/systemd/system/```
- Enable the service ```sudo systemctl enable gnss_base_logger.service```