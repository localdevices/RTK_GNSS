# Set up a base logger

Here are the instructions for setting up an Ardusimple GNSS base station with a Raspberry Pi (here we're using a Raspberry Pi Zero, which is inexpensive and consumes very little energy) to log raw satellite observations over serial (in UBX format, which we'll later convert to RINEX format for processing) for Precise Point Positioning.

This is useful to get a very precise stand-alone position, either for direct use, or to provide a base station with a good absolute reference. The basic idea is to run an ArduSimple for at least 2.5 hours (4 is better) and log all of the raw satellite data, then send it to Natural Resources Canada (or another PPP provider) for processing.

You will:
- Set up jumper cables for serial transmission between the ArduSimple and a Raspberry Pi (this can be done via USB, but that consumes a USB port which limits what else can be done with the Pi Zero, so we're using serial). 
- Configure the ArduSimple to output raw satellite observations on its UART1 serial port at 460800 baud
- Configure a Raspberry Pi to read those observations and log them to a file on its SD card for retrieval and processing later.

Ok, let's get to it.

## Wire it up
jumper cables are required to log the raw data from UART1. For this we need 5 Female-Female connectors.

The following pins should be connected:

| Pi | ArduSimple |
| --- | --- |
| 5V | 5V_IN |
| 5V | IOREF |
| GROUND | GND |
| TX | RX |
| RX | TX |

![Jumper cable setup](/images/Ardusimple_RPi0_jumper_wiring_schematic.jpeg)

![Jumper cable setup example photo](/images/Ardusimple_RPi0_jumper_wiring_photo.jpeg)

Make really sure that these connections are correct! If these ones are connected wrong, then the GNSS receiver may get destroyed. Note that for GND at the GNSS receiver are two pins. You can use either one of them, but make sure you are not using the ‘empty’ pin on the top. Also, be sure to disconnect the power from both devices before hooking up serial cables. 

## Configure the ArduSimple

You'll need U-Center for this. Instructions to set that up (on Linux) are [here](/provisioning/U-center_setup.md)
- Before you start this configuration journey, make sure your ArduSimple is running the stock configuration. If you are not sure, you can use U-Center to configure it with the file in ```/ublox_configurations/stock_ardusimple_base.txt```. [Here's how you do that]()

You need to activate transmission of raw data over UART1 (serial over jumper cables) at the right baud rate for the script on the Pi to read and log it.
- Connect the ArduSimple to U-Center
- Open the Messages window. It may take a moment for the UBX options to appear.
- Navigate down to UBX-CFG-PRT (Ports) and select UART1 as Target. Check that the Protocol out contains only UBX (```0 - UBX```) and the Baudrate is set according for your needs (to match the Python logging script we use, select 460800).
- Click on UBX-CFG-MSG (Messages)
  - Select the message 02-15 RXM-RAWX and activate it on UART1.
  - Select the message 02-13 RXM SFRBX and activate it on UART1.
  - Click on send.
- After that, go to UBX-CFG-CFG (Configuration). Select all 4 devices on the right (BBR, FLASH, I2C-EEPROM, SPI-FLASH) and click on send.

## Set up the Pi as a serial logger

- Flash a Pi SD card with the latest Raspberry Pi OS Lite from [here](https://www.raspberrypi.org/software/operating-systems/) (I use [Balena Etcher](https://www.balena.io/etcher/) for this). 
- Add empty ```ssh``` file and appropriate ```wpa_supplicant.conf``` [like this](https://www.raspberrypi.org/documentation/configuration/wireless/headless.md) with wifi info to boot partition on that SD card. Put it in the Pi and start it up.
- SSH into the parent pi ```ssh pi@raspberrypi.local``` with password ```raspberry```.
- Change the default password ```passwd```, update, upgrade ```sudo apt update && sudo apt upgrade -y``` and install Git ```sudo apt install git -y```.
- Clone this repo ```git clone https://github.com/localdevices/RTK_GNSS```
- Copy the logger script into the home folder ```cp RTK_GNSS/provisioning/simple_python_logger/log_ubx.py /home/pi```
- Copy the service file so that Systemd will start logging on startup ```sudo cp RTK_GNSS/provisioning/simple_python_logger/gnss_base_logger.service /etc/systemd/system/```
- Enable the service ```sudo systemctl enable gnss_base_logger.service```