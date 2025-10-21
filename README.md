# Miura Media HUB
MIURA is a ecosystem i'm building to replace all devices i use for my own ecosystem. Since There's my Lenovo Thinkpad something something, phone, laptop yada yada. The MIURA Media HUB is my Lenovo Thinkpad repurposed into being a Linux machine to be a... well, a media hub. 

## Linux Installation
For the version 1 I decided to go with debian LTS. Make sure to download one that fits the architecture of your device. My laptop is a Intel Celeron N3550 something. So it's based on the AMD64 architecture. If it's a mobile chip like a raspberry Pi it should be ARM.

During the installation process, pick **Graphical Install**. This will prompt you an intuitive Installation GUI. When asked to install packages, only install,

 - System Utilities
 - SSH server 
 - Web Server

Do not install GNOME or DESKTOP ENVIRONMENT thingies. Just those two.

This will install a CLI version of Debian without anything. Basically bare minimum Linux. After that we customise with custom packages.

### Installing Essential Packages
connect to the internet from the installation process. otherwise its going to be very hard as no packages are installed to make it easier to connect. I wont bother to put on how to get connected to the internet via WiFi here.
Once Connected run,

> sudo apt update && upgrade

This will update the list of packages installed. and update if there are available updates.

if it says unable to find or some shit that means the sources are missing. run,

> sudo nano /etc/apt/source.list

and add trixie sources.

Then install these which are essential.
 
- xorg
- xinit
- openbox
- lightdm
- pcmanfm
- firefox-esr

these are ciritcal as lightdm is the user manager, openbox is the window manager, pcmanfm is the file manager and we have Fire Fox to browse the internet.

for media,

- vlc
- ffmepg
- mlv

# Installing Nekoray

download the .deb file from the official release. Then navigate to the downloads folder with cd. Run,

> sudo apt install wmctrl
> sudo dpkg -i 'Nekoray.deb'

this will install nekoray and it will be installed in /usr/share/applications

Nekoray wont run on the wrapper Nekoray. It will run on Nekobox. So running,

> /opt/nekoray/nekobox -appdata

 will open nekoray. After you put the profile in, run

> sudo nano ~/.config/openbox/autostart

in there paste,

> sudo /opt/nekoray/nekobox -appdata -m tun
> sleep 2
> wmctrl -c "nekobox"

Since we have to run this in sudo, we need to make this command not need a password. Run,

> sudo visudo

Then add this at the end. replace yourusername with the actual username.

`yourusername ALL=(ALL) NOPASSWD: /opt/nekoray/nekobox -appdata -m tun`

This will open nekoray, autoconnect and the close the window as soon as it boots up and logs in. 

## Auto Login

Since you’re using LightDM, the simplest way:

`sudo nano /etc/lightdm/lightdm.conf` 

Look for these lines (or add them):

>[Seat:*]  
autologin-user=chethy 
autologin-session=openbox

this will autologin into our window manager, OpenBox.

ChethySignOut. (2025/10/20)
