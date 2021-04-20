# Set up a base logger

- Make sure your ArduSimple is running the stock configuration. If you are not sure, you can use U-Center to configure it with the file in ```ublox_configurations/stock_ardusimple_base.txt```
- Set up the serial pinouts for your ArduSimple and Raspberry Pi [note to self: Ivan, add more instructions here]()
- Flash a Pi SD card with the latest Raspberry Pi OS Lite from [here](https://www.raspberrypi.org/software/operating-systems/) (I use [Balena Etcher](https://www.balena.io/etcher/) for this). 
- Add empty ```ssh``` file and appropriate ```wpa_supplicant.conf``` [like this](https://www.raspberrypi.org/documentation/configuration/wireless/headless.md) with wifi info to boot partition on that SD card. Put it in the Pi and start it up.
- SSH into the parent pi ```ssh pi@raspberrypi.local``` with password ```raspberry```.
- Change the default password ```passwd```, update, upgrade ```sudo apt update && sudo apt upgrade -y``` and install Git ```sudo apt install git -y```.
- Clone this repo ```git clone https://github.com/localdevices/RTK_GNSS```
- Copy the logger script into the home folder ```cp RTK_GNSS/provisioning/simple_python_logger/log_ubx.py /home/pi```
- Copy the service file so that Systemd will start logging on startup ```sudo cp RTK_GNSS/provisioning/simple_python_logger/gnss_base_logger.service /etc/systemd/system/```
- Enable the service ```sudo systemctl enable gnss_base_logger.service```