# Setting up Raspberry Pi units

- Get a decent quality micro SD card. Absolute minimum 8GB, but wouldn't recommend bigger than 64GB (compatibility issues can come up). Read [this](https://www.raspberrypi.org/documentation/installation/sd-cards.md) or do some research if you want to fiddle with it, otherwise buy a bog-standard brand-name SD card and get on with it. I'm using SanDisk Extreme 64GB cards. They seem fine.
- Work somewhere where there's a WiFi router without any complicated settings. You'll need the SSID and password of the router.
  - If there's a captive portal/login page or other complexities (as in a lot of hotels and some co-working spaces) be warned: your life will suck.
  - If need be (for example if the only WiFi you can find has a captive portal) you can do it all tethered to a WiFi Hotspot from your phone.
- You'll need a computer with an SD card reader.

## Set up base infrastructure on the Pi
- Download the Raspberry Pi OS (32-bit) Lite image from [here](https://www.raspberrypi.org/downloads/raspberry-pi-os/). The Lite image doesn't have a graphical interface (it's "headless") and is therefore smaller to download and will run more efficiently on the limited Pi hardware.
- Unzip the archive and flash the image to your SD card using [Balena Etcher](https://www.balena.io/etcher/). You can mess around with other ways to burn images if you are feeling masochistic; Etcher just works, and it's open source.
- After flashing the image, it's a good idea to eject the card. You'll need to put it back in momentarily, but it seems that sometimes things go wrong if you don't eject and re-insert the card.
- Now you need to get the Pi on the WiFi, enable SSH, and connect to it. The SD card you just flashed should appear in your file explorer, open it up.
  - The SD card should now have two partitions, a small one called ```boot``` and a larger one called ```rootfs``` (if you're on Windows you might not see the ```rootfs``` partition; too bad for you—you should be using Linux anyway—but it doesn't matter for now). Put a ```wpa_supplicant.conf``` file in the ```boot``` partition of the SD card. Explanation of that file [here](https://www.raspberrypi.org/documentation/configuration/wireless/headless.md).

```
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
update_config=1
country=TZ

network={
 ssid="OMDTZ-Guest"
 psk="apassword"
}

```

I copy the contents of that example, navigate to the root of the ```boot``` partition on the SD card and type ```nano wpa_supplicant.conf``` and paste in the contents, replacing the SSID, password, and country code with the appropriate ones.
  - Also in the ```boot``` partition, you need to place a file called ```ssh```. It doesn't matter what's in it (an empty file is fine) and there's no extension on the filename. This enables [SSH](https://en.wikipedia.org/wiki/Secure_Shell) on the Pi (disabled by default). You can do this on Linux or Mac by navigating to the root of the ```boot``` partition and typing ```touch ssh```.
- Eject the SD card and remove it from your computer, put it in the Pi, and power it up. Wait about 90 seconds to make sure it has time to boot up before you try to connect to it.
- Make sure your computer is connected to the same WiFi network as the Pi, open a terminal, and type ```ssh pi@raspberrypi.local```.
  - Didn't work? ```Could not resolve hostname...``` or something like it? Sigh. Ok, try to figure out if the Pi is on the WiFi. You can try [nmap](https://nmap.org/) to attempt to identify every device on your local subnet with ```nmap -sP 192.168.X.0/24```, but I recently found a lovely FOSS application called [Angry IP Scanner](https://angryip.org/); it's great. Try it. If you're lucky, you'll see the Pi on the network along with its IP address, and maybe you can ssh into it using the IP address instead of the ```raspberrypi.local``` alias. If not, you'll have to dive into the world of Google, StackExchange, and the Raspberry Pi forums until you get it sorted.
- Log into the Pi using the default password, which is ```raspberry```. Once you're in, immediately type ```passwd``` (_without_ ```sudo```) and—at the prompts—enter first the old and then the new password (twice). Try not to forget the new password.
- Get everything up to date with ```sudo apt update && sudo apt upgrade -y```. This will take a few minutes, more if your Internet connection is slow.

- Disable the default serial console to free up UART serial line ```sudo sed -i 's/console=serial0,115200 //g' /boot/cmdline.txt```

That'll get you a Raspberry Pi ready to specifically configure and use for most of the applications related to this project.