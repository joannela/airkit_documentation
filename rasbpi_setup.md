# AirKit Setup with Raspberry Pi Zero W (RPZW)

## Setting up The RPZW Headless

We are setting up the RPZW in headless mode which means that we don’t require any peripheral devices (screen, keyboard, mouse etc.). For this we need:

* Rasberry Pi Zero W
* USB cable
* SD card > 4GB
* Laptop with SD card reader
* Access to a WLAN

###Flash the SD Card
* Download the Raspbian Buster Lite image as a zip file from this website: https://www.raspberrypi.org/downloads/raspbian/
* Download and install balenaEtcher from: https://www.balena.io/etcher/
* Connect a blank SD card to your computer and open the balenaEtcher app.
* Click Select Image and navigate to the Raspbian Buster Lite zip file that you have just downloaded. Select the drive it may select your SDcard reader by default. Double check and then press Flash. You may need to enter the password of your computer at this point.

![belaEtcher Screenshout](/rasbpi_git/balenaEtcher.png)
Format: ![Alt Text](url)

* It will take a few minutes to flash the drive. Once it is complete you will need to take the SD card out of your SD card reader and then reinsert it. You will notice that the SD card is now called ‘boot’ and will contain files that look a bit like this.

![file layout for pi boot drive](/rasbpi_git/boot_pi.png)
Format: ![Alt Text](url)

###Enable SSH
You can access the command line of a Raspberry Pi remotely from another computer or device on the same network using SSH.

This is not enabled by default on the RPZW so we have to create a file to enable it. We just need to add a file ‘ssh’ no extensions to the SD card.
On Mac OS or Linux

* Go to the Terminal application
* Write this command and press enter.
`touch /Volumes/boot/ssh`
* It will create a file called ssh in the ‘boot’ directory.

![file layout for pi boot drive](/rasbpi_git/boot_pi_ssh.png)
Format: ![Alt Text](url)


On Windows
Run Notepad
In a new file put in one space and nothing more
Click File / Save As ...
Be sure to set Save as type to All Files (so the file is NOT saved with a .txt extension)
Call the file ssh and save it
Close the file and place it in the ‘boot’ directory
Connect to the WLAN Network
For this we need to create a .conf file and place the network login details inside. You can place the details of multiple networks in this file (i.e. home and work).

On Mac OS or Linux
Go to the Terminal application
Write this command and press enter.
touch /Volumes/boot/wpa_supplicant.conf
Navigate to the ‘boot’ directory and open the wpa_supplicant.conf file you have created in a text editor (we used Atom). Copy and paste the following text with some adjustment. Change ssid to your network name—we used AirKit_Node_1 and psk to the corresponding password.
country=GB
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
update_config=1

network={
    ssid="NETWORK-NAME"
    psk="NETWORK-PASSWORD"
}

If working outside the UK, you can change the country according to the ISO 3166 alpha-2 country code.
Your directory should look something like this.

On Windows
Run Notepad
Paste in the contents above (adjusting for the name of your country code, network name and network password)
Click File / Save As ...
Be sure to set Save as type to All Files (so the file is NOT saved with a .txt extension)
Call the file wpa_supplicant.conf and save it
Close the file and copy it to the ‘boot’ directory.

Boot the Raspberry Pi
Eject the SD card from your reader and place the micro SD in your RPZW. Connect a micro USB cable to the ‘PWR IN’ (not ‘USB’) micro USB connector on the RPZW. This can be connected to any power source that will provide 5V: either a laptop or a USB power connector.
It may take some time (up to 90 seconds) for the RPZW to boot and connect to the network.
You can check that it is connected by logging in to your router and navigating to connected devices (on the Huawei B315 this is under XXXX). You can also do this using nmap. <- update instructions here.

On Mac
Open up a terminal window and run the following commands.
ssh-keygen -R raspberrypi.local
ssh pi@raspberrypi.local
The first command just clears out any references to raspberrypi.local so don’t worry if you get an error.
The last command initialises SSH between your computer and the Pi. You will need to confirm that you want to connect with it by entering yes and pressing return.
You now need to type in the password for the RPZW this is raspberry as default. If you see the pi@raspberrypi: ~ $ you are now able to connect with the RPZW through the command line.

On Windows
You need to install Putty



Changing the Hostname and Password
You now have access to the Pi command line through WiFi, change the hostname and password for security reasons. This will open the software configuration tool. You can navigate up and down the lists  with corresponding arrows on your keyboard. To navigate back and forward (i.e. <select> and <back>) use the left and right arrows. To select something press return.
Run the script below:

sudo raspi-config


Navigate to Network Options > Hostname. Select OK to the host name instructions. We are using the naming convention AirKitX where X is the next number in the sequence.
Then navigate to change user password and update the password (make a note of it). You will have to type the password twice.
After this is complete then exit the software configuration tool by selecting <Finish>. You will be prompted to reboot select <Yes>.
You can login as before but using the new hostname and password, change X for your corresponding device.
ssh pi@AirKitX.local


Update the RPZW

Once you are connected to the command line update and upgrade the RPZW by running the following scripts.

sudo apt-get update -y
sudo apt-get upgrade -y



Adapted from: https://desertbot.io/blog/headless-raspberry-pi-3-bplus-ssh-wifi-setup

Instructions for setting up DHT22 (Temperature and Humidity sensor) with Raspberry Pi Zero

Parts Needed:
A Raspberry Pi Zero that is connected to the internet.
DHT22 sensor.
A resistor. We have used 1k Ohm resistor. But it has been suggested that you can use either a 4.7k Ohm resistor or a 10k Ohm resistor as well.
Jumper cables to connect the parts.
A breadboard, which may make it easier to connect the parts.

Wiring:
Wire the sensor as shown in the following picture. The first sensor pin is 3V-5V, the second is bidirectional serial data, the third is not used and the fourth is ground. The resistor is used as a pull-up resistor.



How to get temperature and humidity

Log in to the Pi using Terminal by running: ssh pi@AirKit2.local
Run sudo apt-get update and sudo apt-get upgrade -y to ensure your system is up to date.
Correct the timezone by checking if it is correct. It can be done using date.  To set the correct timezone, use sudo raspi-config.
Install setup tools for python by using this command sudo apt-get -y install python-setuptools python-dev build-essential.
To communicate with the sensor, the Adafruit_Python_DHT library is used. It talks to the GPIO pins. To install it, get the dependencies with sudo apt-get install -y build-essential python-dev git and then download and install the library with
    mkdir -p /home/pi/sources
    cd /home/pi/sources
git clone https://github.com/adafruit/Adafruit_Python_DHT.git
cd Adafruit_Python_DHT
sudo python setup.py install

To get temperature and humidity, use this command

sudo /home/pi/sources/Adafruit_Python_DHT/examples/AdafruitDHT.py 22 4
The first argument is the sensor type, it can be 11 or 22 or other type of sensor. The second argument is the RPi GPIO pin which is connected to the sensor data pin.




Instructions for setting up BME680 (Temperature and Humidity sensor) with Raspberry Pi Zero

The first step is to solder the BME680 to the breakout.
There is a piece of right-angle female header that you can solder on and then pop the breakout right onto pins 1, 3, 5, 7, and 9 of your Raspberry Pi.


Changing the default I2C address
The breakout has a default I2C address of 0x76, but this can be changed so that you can use up to two breakouts on the same Raspberry Pi or Arduino. To change the I2C address to 0x77, simply flow a small blob of solder across the two solder pads so that it bridges the pads. If you decide to change it back at a later date, then you can use a solder sucker or some solder braid to remove the solder and un-bridge the pads.

You'll need to have I2C enabled on your Raspberry Pi. You can do this in the Interfaces section of the Raspberry Pi Configuration menu, by typing sudo raspi-config in the terminal.

Libraries to install:
The Adafruit library for the GPIO lines is required by the Adafruit BME680 library to access I2C bus. It is advisable to install also some packages from the Raspbian distro, otherwise the python setup will try to download and install a local version:
  sudo apt-get install python-dev python-setuptools python-spidev
The Adafruit Python library is installed from the Git repository:
cd /usr/local/src/
sudo git clone https://github.com/adafruit/Adafruit_Python_GPIO.git
cd Adafruit_Python_GPIO
python setup.py install
Required software
On the Raspberry Pi we need some extra packages containing some tools for the I2C bus:
sudo apt-get install python-smbus i2c-tools
To detect I2C device, run this command
i2cdetect -y 1
Connecting the sensor and getting the data
Type the following to clone the repository and install it:
git clone https://github.com/pimoroni/bme680
cd bme680/library
OR
You can also use pip to install the BME680 library
sudo pip install bme680
Note: If you don’t have pip or git, please install it in the Pi before proceeding with these commands.
Create a new python file by using this code:
 nano data.py
Edit the file with this code and save it.
import bme680
import time
From datetime import datetime
print("""temperature-pressure-humidity.py - Displays temperature, pressure, and humidity.
If you don't need gas readings, then you can read temperature,
pressure and humidity quickly.
Press Ctrl+C to exit
""")
try:
    sensor = bme680.BME680(bme680.I2C_ADDR_PRIMARY)
except IOError:
    sensor = bme680.BME680(bme680.I2C_ADDR_SECONDARY)

# These oversampling settings can be tweaked to
# change the balance between accuracy and noise in
# the data.

sensor.set_humidity_oversample(bme680.OS_2X)
sensor.set_pressure_oversample(bme680.OS_4X)
sensor.set_temperature_oversample(bme680.OS_8X)
sensor.set_filter(bme680.FILTER_SIZE_3)

print('Polling:')
try:
    while True:
        if sensor.get_sensor_data():
            output = datetime.now().strftime('%Y-%m-%d,%H:%M:%S,')+
'{0:.2f} C,{1:.2f} hPa,{2:.3f} %RH'.format(
                sensor.data.temperature,
                sensor.data.pressure,
                sensor.data.humidity)
            print(output)
        sleep.time(60)
except KeyboardInterrupt:
    pass
Once you run the file, you will get an output like this:


Adapted from: https://learn.pimoroni.com/tutorial/sandyj/getting-started-with-bme680-breakout
https://www.rigacci.org/wiki/doku.php/doc/appunti/hardware/raspberrypi_air


Instructions for setting up PMS5003 (PM sensor) with Raspberry Pi Zero

Setting up the Pi Zero W
Connect PMS5003 with RaspberryPi Zero

Install pip and other dependencies:
sudo apt-get install python3-pip
sudo apt-get install python-pip (For Python 2)
sudo apt install git
git clone https://github.com/pimoroni/enviroplus-python
cd enviroplus-python
sudo ./install.sh
Once that's all done, type sudo reboot to reboot your Pi and apply the changes to the Pi's interfaces.
The  scripts mentioned above enable I2C, SPI, and serial, disables the serial console, and enables the miniuart interface that the Pi uses to talk to the PMS5003 particulate sensor.
There are several examples in the examples folder within the enviroplus-python folder.
cd examples
python particulates.py



Reference: https://learn.pimoroni.com/tutorial/sandyj/getting-started-with-enviro-plus
