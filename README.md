# How to install OctoPi on a Raspberry Pi 3B
By far, the easiest method of installing OctoPrint for your printer is to use the inexpensive Raspberry Pi 3B single-board computer, taking advantage of the image file which has been created to make this simple.

> **OctoPrint** is the web interface for your 3D printer. It's the product of the talented Gina Häußge.

> **OctoPi** is a distribution image for the Raspberry Pi computer as maintained by Guy Sheffer.

> **Raspbian** is the operating system for the Raspberry Pi which is based on Debian.

## Preparation
Before starting your installation, it's best to gather some things together first. It's also a good idea to read through all this before beginning.

### Checklist

- [x] 5V 2.5A power adapter for the Raspberry Pi 3B [1](#SunFounder)
- [x] Short USB serial cable (usually Type B to Type A style) with either internal metallic shielding or with one or two ferrite cores
- [x] Raspberry Pi 3B (or 3B+)
- [x] 4GB or larger microSD card
- [x] Workstation of some kind (OSX, Windows, Linux)
- [x] A means of inserting a microSD card into this workstation (SD adapter or a USB-based microSD adapter)
- [x] 3D printer which has a Type B USB connector and which runs a compatible firmware: Marlin, RepRap, Repetier. Check the [compatibility list](https://github.com/foosel/OctoPrint/wiki/Supported-Printers) first. In a few cases, you may need a plugin for your printer to work with OctoPrint.

### Optional
- [ ] [Raspberry Pi webcam](https://www.adafruit.com/product/3100) and ribbon cable
- [ ] USB-based webcam, verifying that it's on the [compatibility list](https://github.com/foosel/OctoPrint/wiki/Webcams-known-to-work)
- [ ] Local TFT or similar touch screen for the Raspberry [1](#SunFounder)

### Required software
1. If you don't otherwise have it on your workstation, download and install [Etcher](https://www.balena.io/etcher/).
2. Download the latest OctoPi image and save it as a zip file on your computer's **Desktop** folder. To minimize browser-unzip problems, the recommended method is:

```
curl -L -o ~/Desktop/octopi.zip https://octopi.octoprint.org/latest
```

Copy/paste this into a command prompt on your workstation.

![desktopwithoctopi](https://user-images.githubusercontent.com/15971213/50716686-99238b80-1037-11e9-925d-d40c00fae788.png)

## Installation
1. If you don't otherwise have it on your workstation, download and install [Etcher](https://www.balena.io/etcher/)
2. Insert the microSD card into your workstation, start Etcher, select the `octopi.zip` file on your Desktop, verify that your microSD media is selected and press `Flash!`

![etcherscreenshot](https://user-images.githubusercontent.com/15971213/50716913-d6d4e400-1038-11e9-9645-3a4004a37971.png)

## Next, edit the wi-fi configuration
**Etcher** should dutifully eject the media when it's finished. Since we need to make one edit, follow along:

&nbsp;1. remove/replace the microSD card so that it's mounted again

> NOTE: It's important to NOT allow your operating system to format or otherwise to "fix" the media. Neither OSX nor Windows will be able to mount the second (`ext4`) partition and may complain. This is to be expected.
> 
> Regardless, DO NOT choose any options to format the media!

&nbsp;2. Using a safe editor, you need to edit a file called `octopi-wpa-supplicant.txt` which is located on the `boot` partition.

   * **OSX**: From **Terminal**, `sudo nano /Volumes/boot/octopi-wpa-supplicant.txt`
   * **Windows**: In **Explorer**, go to the `boot` disk, right-mouse click on `octopi-wpa-supplicant.txt` and choose **Open With...** -> **Notepad++**
   * **Linux**: If you're in a Desktop interface, you should be able to double-click this file in the `boot` partition. Otherwise, using `sudo nano` to edit the file would be necessary once you find the mount point.
   * Erase all the file's original contents and replace with this to begin with.

```
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
update_config=1
country=US
 
network={
        ssid="insert_your_SSID_here"
        psk="insert_your_wifi_password_here"
        key_mgmt=WPA-PSK
}
```

   * Now **edit the wi-fi zone's SSID and password**, noting that these are **both case-sensitive**. Make sure that you've left the double quotes on either side of each.
   * Finally, **edit the country code** making sure to enter this in UPPERCASE as a two-digit code.
   * **Save the file and exit the editor**.
   * **Safely eject the media** and only then **remove it from your workstation**.

## First boot
Put the microSD into the Raspberry Pi 3B. Connect the power adapter to the micro USB connector and plug it into a power outlet.

Watch for activity on the LEDs which are on the Raspberry Pi.

> **Name resolution**: 
Assuming that the Raspberry connected to your wi-fi network, both OSX and Linux will likely be able to find the new installation using its hostname of `octopi.local`.
>
> Windows installations which have iTunes installed should also be able to resolve `octopi.local` into its IP address.
>
> Users with Windows installations *without* iTunes might need to do some detective work in order to find and use the IP address of the Raspberry. Check your router's DHCP area to see what it issued.

## Connect remotely to the Raspberry Pi 3B
* **OSX** or **Linux**: From a command prompt, `ssh pi@octopi.local` using `raspberry` as the password
* **Windows**: If you don't otherwise have it, download and install [PuTTY](https://www.ssh.com/ssh/putty/windows/install) then connect as the `pi` user to the IP address your router has issued to the Raspberry Pi. It may be necessary to log into your router to determine which address was given. If you have previously installed iTunes, then you should be able to more easily use the Pi's hostname of `octopi.local`. See the Name "Resolution section" above. As above, the password is `raspberry`.

## Visit the web interface
Assuming that your Raspberry Pi connected to your wi-fi network, try visiting the address [http://octopi.local](http://octopi.local) from your workstation.

For Windows users without iTunes, you might have to enter an IP address instead of `octopi.local` to reach your instance.

## Shutting down OctoPrint before poweroff
Before removing power from the Raspberry Pi, make sure to shutdown the OctoPrint service:

1. From the OctoPrint web interface: *System menu -> Shutdown*
2. From a remote shell to the Raspberry Pi: `sudo poweroff`

--

# Troubleshooting

## Can't connect remotely from ssh/PuTTY
There are some common reasons why you might not be able to remotely connect on first attempt.

1. The most likely problem is that the SSID/password were not entered correctly into the configuration file.
2. If you're attempting to use an IP address, you might have the wrong IP address.
3. If you're attempting to use the hostname `octopi.local` and you're on Windows, it doesn't have the **Bonjour** name resolution service loaded by default. You might have to resort to using the IP address.
4. You could try the command `ping octopi.local` to see if the hostname can be resolved and to determine if packets can be routed to the Raspberry Pi.
5. For OSX/Linux, remember to prefix the hostname with which user you're trying to connect as:  `ssh pi@octopi.local`
6. For Windows, remember to attempt to connect as the `pi` user.
7. Booting the Raspberry Pi with its own monitor/keyboard/mouse is sometimes helpful to verify that Raspbian is at least booting. Feel free to log in when prompted. The command `ifconfig` will reveal if the `wlan0` wi-fi adapter is in the RUNNING state. This means that it connected successfully to your wi-fi network and it received an IPv4 IP address. If you see the address, you can try to use it to remotely connect from your workstation. If the adapter is not in the RUNNING state then try again to edit the configuration file from earlier.

--

# Optional topics

## <a name="SunFounder"></a>10" local display
I will note that the  [SunFounder 10" Capacitive TFT Display](https://www.amazon.com/Raspberry-Inch-Touch-Screen-Touchscreen/dp/B0776VNW9C/ref=sr_1_3?ie=UTF8&qid=1545388346&sr=8-3&keywords=sunfounder+10.1+touch+screen+for+raspberry+pi) has a 12V 2A power adapter and its board supplies 5V power to the Raspberry, making the 5V power adapter unnecessary.
