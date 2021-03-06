# Intro to Raspberry Pi

Through this course, we're going to learn the basics of working with a Raspberry pi.  

Want to learn more? See a typo? Please leave me some feedback [here :smiley:](https://github.com/matthova/rpi-intro/issues)

1. [Supplies](#supplies)
- [Download a “Raspbian” Image](#raspbian)
- [Plug it in and power it up](#power)
- [Logging in / Where’s my pi?](#ssh)
- [Customizing your pi](#config)
- [Updating your pi](#update)
- [Connecting to wifi](#wifi)
- [Edit pi files from your computer](#samba)
- [Blinking our first LED](#blink)
- [Writing our first server](#server)
- [Making a program run “on-boot”](#systemd)
- [Making a full screen web UI](#ui)

### <a name="supplies"></a>Supplies needed
For this course, we're going to need the following hardware:
- A computer (The one you're using now looks great!)
- 1 Raspberry Pi (The version doesn't matter, although the newer models are faster)
  * If your Raspberry Pi model is < V3.0 then you will also need a wifi dongle
- 1 Micro SD card, 4 gigabytes or larger
- 1 Micro SD card adapter (Or some means for reading/writing to your micro SD card from your computer)
- 1 Ethernet cable
- Access to your wifi router

### <a name="raspbian"></a>Downloading a "Raspbian" Image
In order to run your Raspberry Pi, you will need to download an Operating System.
- Download Raspbian Jessie Lite zip file [here](https://www.raspberrypi.org/downloads/raspbian/).  
- Once the download is complete, unzip it. The unzipped file should have a file extension ".img"

##### Writing Raspbian to SD
On Mac, we will use the Pi Filler Script, to write our .img file onto the SD card.
- Download Pi Filler [here](http://ivanx.com/raspberrypi/files/PiFiller.zip).
After you unzip the file, feel free to drag it to your "Applications" folder.
- Double click on "Pi Filler" and follow its instructions.

(Note: An uncompressed Raspberry Pi image, is referring to the ".img" file that we just unzipped.)

Next you will see some scary messages saying that the program is going to "erase" the sd card. It's okay, that's a part of the process. Writing the OS onto the SD card should take 2-3 minutes. When it's done, a message should pop up on the screen to let you know it's okay to remove the SD card.  

##### Setting up wifi login
If you're not connecting the pi over ethernet you can set up the image to connect to wifi by entering your credentials on the sd card as follows:
- Enable ssh login  
```touch /Volumes/boot/ssh```
- Write you wifi login info onto the sd card  
```nano Volumes/boot/wpa_supplicant.conf```  
And then past in the following:
```country=US
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
update_config=1

network={
    ssid="NETWORK-NAME"
    psk="NETWORK-PASSWORD"
}
```  

### <a name="power"></a>Plug in your SD card and power up your Raspberry Pi
Once the Pi Filler script is done running:
- Remove the micro SD card from your computer and place it into the Raspberry Pi's micro SD card slot. (note: On the newer models, there's no satisfying "snap" when you slide the SD card in place. Don't worry, it should work just fine.)
- Connect your wifi router to your Raspberry pi's Ethernet port using an ethernet cable.
- Finally, plug your power supply in to the wall, and connect the micro usb end of it into your raspberry pi. You should immediately see lights flashing. Hooray!

### <a name="ssh"></a>SSH'ing in to your Pi
We need to find out where our raspberry pi is. One way to do this is using Bonjour.
- Download Bonjour Browser [here](http://www.tildesoft.com/files/BonjourBrowser.dmg). Install the download and run it.
- Once it's running, you should see a list of devices available on your network.
- Open the "Workgroup Manager" tab. You should see your device listed there, as "raspberrypi".
- Open the "raspberrypi tab" next. You will see two numbers listed. The first one is your IP address. We're going to use this number to sign in to our raspberry pi. For example, if your IP address shows up as 100.101.102.103:9 then drop the ":9" and use "100.101.102.103".

Open your "Terminal" program and type in the following below:
```
ssh pi@yourIpAddress
```
(replace yourIpAddress with your ip address)

Once you hit enter you may be prompted with a dialog about "fingerprints". Just type "yes" and hit enter.
You should then be prompted to enter a password. Type in "raspberry" for the password. No quotes.

Hooray, you just ssh'd in to your Raspberry Pi!

### <a name="terminal"></a>Using terminal with your pi
Using terminal can be a bit confusing, especially when you're just getting started. One thing to pay close attention to is knowing "where" your terminal is signed in to. On each line, to the left of where you type in your text, you will see 
```
pi@raspberrypi":~ $
```
This means that you are signed in to your raspberry pi, and that when you type, you are typing in the context of the raspberry pi's terminal.
If, however the text on the left is anything else, you are most likely typing in the context of your computer. 

****************************************************************************
MAKE SURE THAT YOU ARE SSH'D IN TO YOUR RASPBERRY PI BEFORE RUNNING ANY LINE OF CODE BELOW  
DO NOT ENTER ANY CODE INTO YOUR TERMINAL UNLESS THE LEFT SIDE OF YOUR DISPLAY LOOKS LIKE THIS:  
```
pi@<your_user_name>":~ $
```
Where "your_user_name" is replaced with raspberrypi or whatever you have set your username to.  
****************************************************************************

### <a name="config"></a>Customizing your Pi
Now we're going to set up our raspberry pi to be more personalized. In terminal, type in:
```
sudo raspi-config
```
Use your arrow keys and "enter" button to navigate the menu.
We're going to do the following:

1. Expand Filesystem
  With "#1 Expand Filesystem" selected, hit enter. This will allow your pi to use it's full capacity. Hit ok.
2. Change User password
  With "#2 Change User Password" selected, hit enter. Follow the dialog and change the password to whatever you want.
9. Update your username
  With "#9 Advanced Options" hit enter. Now select "A2 Hostname" and hit enter. Follow the dialog and update the hostname from "raspberrypi" to any name you want. You can only use characters, numbers, and dashes (no underscores).
Once you have completed all of these steps, you will want to reboot your pi. Do this by hitting the "->" right arrow twice, and then hitting enter, to select "Finish". Hit ok.

It will take about a minute for the pi to reboot. Don't worry.

Remember what you renamed your "raspberrypi" hostname to? You can now use that name to ssh in to your pi. For example, if my pi was called "hovapi" I would type the following:
```
ssh pi@hovapi.local
```

### <a name="update"></a>Updating your Pi
Ssh back into your pi, as described in the last step.
This next part will take some time, so prepare yourself for a nice break.
Once you are ssh'd in, in terminal type in the following:
```
sudo apt-get update
```
This will let your pi know about all of the latest and greatest versions of the code that are available.
```
sudo apt-get upgrade -y
```
This will download the code and install it.
```
sudo apt-get install git -y
```
This will install the program "git" onto our pi

### <a name="wifi"></a>Connecting to WiFi
In order to connect over wifi, we need to setup our wireless credentials. To do this, enter the following into the pi's Terminal:
```
sudo nano /etc/wpa_supplicant/wpa_supplicant.conf
```
The file that you open will look something like this:
```
country=GB
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
update_config=1
```
We're going to update it with our wifi info:
The file that you open will look something like this:
```
country=GB
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
update_config=1
network={
    ssid="name_of_wifi_network"
    psk="wifi_password"
}
```
Once you have updated the text, hit ctrl + "x" to exit, and hit "y", then "enter" to save the file with your update.

Now in terminal, type in the following, and in a few seconds your wifi should be up and running! -->
```
sudo service networking restart
```

Assuming everything goes according to plan, you now have a working wifi connection on your raspberry pi. Feel free to disconnect your ethernet cable, it's no longer necessary.

### <a name="samba">Set up Samba so you can edit pi files from your computer</a>
Install samba
```
sudo apt-get install samba samba-common-bin
```

Then edit samba's configuration so that we can edit files.
```
sudo nano /etc/samba/smb.conf
```

Lets change the following line (roughly 3/4 down the page):
```
read only = yes
```
to
```
read only = no
```

Now let's add a user "pi" and setup a password for logging in to edit the Pi's files:
```
sudo smbpasswd -a pi
``` 
On Mac, you should now see the Raspberry Pi's "shared" folder available via Finder

### <a name="blink"></a>GPIO, aka the Pi's pins
![Rasbperry Pi Pinout](https://az835927.vo.msecnd.net/sites/iot/Resources/images/PinMappings/RP2_Pinout.png)  

Learn how to blink an LED from the repo [here](https://github.com/matthova/blink).  


### <a name="button"></a>Let's add a button
Learn how to process input from a button from the repo [here](https://github.com/matthova/button).  

### <a name="server"></a>Let's make a server
We are going to make a basic server using Python and Flask.  

Follow the instructions from the repo [here](https://github.com/matthova/basic-flask-server)

### <a name="neopixel"></a>Let's blink some neopixels
Now that we understand how to use a server and how to control hardware with our Raspberry Pi's GPIO pins, lets combine the two ideas.  
We are going to use a website to change the color of a neopixel.  

Follow the instructions from the repo [here](https://github.com/matthova/neopixel_demo.git)


### <a name="systemd"></a>Running a server "On Boot"
One way that we can ensure a script will run when the raspberry pi is powered on, is to put that script into /etc/rc.local
```
sudo nano /etc/rc.local
```
When referring to any program, it's best practice to refer to their full path. For example, instead of python, you would use /usr/bin/python. In order to find out what is the full path for a program, for example, node, you would type
```
which node
```
In order to find out the current path of the file you are editing you can type
```
pwd
```

When writing your scripts, you will put a "&" character at the end of the command, to flag that this process is running in the background
To test this code, rather than rebooting, you can type
```
/etc/rc.local
```
Which will run all of the code in that file. If everything worked, reboot your pi and it should be running again.

### <a name="ui"></a>Running a ui "On Boot"
Follow the instructions from the repo [here](https://github.com/guysoft/FullPageOS)
