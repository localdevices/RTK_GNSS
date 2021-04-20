# Set up a base logger

- Flash a Pi SD card with the latest Raspberry Pi OS Lite from [here](https://www.raspberrypi.org/software/operating-systems/) (I use [Balena Etcher](https://www.balena.io/etcher/) for this). 
- Add empty ```ssh``` file and appropriate ```wpa_supplicant.conf``` [like this](https://www.raspberrypi.org/documentation/configuration/wireless/headless.md) with wifi info to boot partition on that SD card. Put it in the Pi and start it up.
- SSH into the parent pi ```ssh pi@raspberrypi.local``` with password ```raspberry```.
- Change the default password ```passwd```, update, upgrade ```sudo apt update && sudo apt upgrade -y``` and install Git ```sudo apt install git -y```.
- Clone this repo ```git clone https://github.com/localdevices/RTK_GNSS```

