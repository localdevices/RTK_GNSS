Setting up ArduSimple devices

## U-center installation
*Note: these are Linux-based instructions! If you're on Windows or Mac things will be different.*

Download [u-Blox u-center](https://www.u-blox.com/de/product/u-center). When you get to this page, scroll down a bit until you see the button for "Documentation & resources". There you'll find a section called "Evaluation software" where you can download a zip file.

Yes, it is a windows executable, but we assume you installed wine. Once you download the zip file, extract it. Then go with your command line to that folder and type ```wine u-center_vXX.xx.exe``` (whatever the latest version number is). That will launch a Windows installer.

Go through the installation process, but _don't accept the standard installation path_; that probably won't work (and will be a hassle to find anyway). Put it somewhere on your hard drive that you can remember and easily find.

After installation, plug your Ardusimple into your computer with a USB cable (it needs to be the USB port labelled ```power and GPS```, not the one on the other side, which is for the radio). Navigate again on the command line to where you installed the program and type ```wine u-center.exe```. This will start the u-center application. 

You may have to set the permission on the emulated Windows port to access the device via USB. For example, ```sudo chmod 777 ~/.wine/dosdevices/com33```.

Once 

## Configuring the base station for simple Python-based logging of raw UBX data
You need to activate transmission of raw data over UART1 (serial over jumper cables) at the right baud rate for the script on the Pi to read and log it.
- Connect the ArduSimple to U-Center
- Click on UBX-CFG-MSG (Messages)
  - Select the message 02-15 RXM-RAWX and activate it on UART1.
  - Select the message 02-13 RXM SFRBX and activate it on UART1.
  - Click on send.
- Make sure under UBX-CFG-PRT (Ports) and select UART1 as Target. Check that the Protocol out contains at least UBX and the Baudrate is set according for your needs (to match the Python logging script we use, select 460800).
- After that, go to UBX-CFG-CFG (Configuration)
- Select all 4 devices on the right (BBR, FLASH, I2C-EEPROM, SPI-FLASH) and click on send.