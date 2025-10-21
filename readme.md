# **MIURA Media HUB - Project Documentation**

_Project Goal: Repurpose my Lenovo Thinkpad (Intel Celeron N3550, AMD64) into a minimal, custom Linux-based media hub._ _Date: 2025/10/20_

## 1. Linux Installation: The Bare Minimum

I'm building this on **Debian LTS** for stability. Since my Thinkpad has an **Intel Celeron N3550**, the architecture I needed was **AMD64**.

### The Installation Process

I opted for the **Graphical Install** for convenience. The critical step was package selection—I ensured I was only installing the absolute essentials for a lean, CLI-first system:

-   **System Utilities**
    
-   **SSH server** (Essential for easy remote management)
    
-   **Web Server** (Good for future network-based services)
    

I specifically avoided any full **Desktop Environment** like GNOME or Xfce to keep the footprint as small as possible. This leaves me with a powerful, customizable bare-metal Linux.

### 2. Customizing the Base System

After the bare-bones install, I needed to get the graphical environment and core tools installed.

#### System Update and Sources

First, update the package list and upgrade the existing packages.

```
sudo apt update && sudo apt upgrade

```

If the package manager throws an error about missing sources (which is common after a minimal install), I had to manually edit the source list to include the repositories. I'm using **Trixie** sources for now.


```
sudo nano /etc/apt/sources.list

```

I added the standard main, updates, and security lines, making sure to include `non-free-firmware` to handle my Wi-Fi card and other proprietary needs:

```
deb http://deb.debian.org/debian trixie main contrib non-free non-free-firmware
deb http://deb.debian.org/debian trixie-updates main contrib non-free non-free-firmware
deb http://security.debian.org/debian-security trixie-security main contrib non-free non-free-firmware

```

#### Installing Essential Packages

Next, I installed the core components for the desktop environment:

-   **`xserver-xorg`** and **`xinit`**: The foundation of the graphical system.
    
-   **`openbox`**: My chosen lightweight **window manager**.
    
-   **`lightdm`**: The display/user manager.
    
-   **`pcmanfm`**: A simple, fast **file manager**.
    
-   **`firefox-esr`**: A solid browser.
    

For media playback, I installed **`vlc`**, **`ffmpeg`**, and **`mplayer`** (used `mplayer` as the more common utility in minimal setups).


```
sudo apt install xserver-xorg xinit openbox lightdm pcmanfm firefox-esr vlc ffmpeg mplayer

```

### 3. Nekoray Setup and Autostart

I need the Nekoray network client to launch automatically on boot and then minimize itself, all without asking for a password.

#### Installation and `wmctrl`

First, I downloaded the `.deb` file and installed it, then installed the window-control utility I'd need later:

```
cd ~/Downloads
sudo dpkg -i 'Nekoray.deb'
sudo apt install -f # To fix any missing dependencies after dpkg
sudo apt install wmctrl

```

The application runs via the `/opt/nekoray/nekobox` binary.

#### Permission Bypass (NOPASSWD)

Since the `nekobox` launch command needs to run as root for its tunnel mode, I have to grant my user (`chethy`) permission to run that specific command with `sudo` but no password.


```
sudo visudo

```

I added this line at the end, replacing `yourusername` with `chethy`:


```
chethy ALL=(ALL) NOPASSWD: /opt/nekoray/nekobox -appdata -m tun

```

#### OpenBox Autostart Configuration

Finally, I set up **OpenBox** to launch Nekoray, wait a moment for it to connect, and then immediately close the window.


```
nano ~/.config/openbox/autostart

```

I added the following lines (the `&` is crucial to run the command in the background, allowing the script to continue to the next step):

```
# Launch Nekoray with the privileged command, run as chethy using NOPASSWD setting
sudo /opt/nekoray/nekobox -appdata -m tun &

# Wait for the window to open and connect
sleep 2

# Find the window titled "nekobox" and close it
wmctrl -c "nekobox"

```

### 4. Auto Login for a True Media Hub

The final step is to make the machine boot directly into the OpenBox session without a login prompt, making it instant-on for media use.

```
sudo nano /etc/lightdm/lightdm.conf

```

I located (or added) the `[Seat:*]` section and configured the auto-login:


```
[Seat:*]
autologin-user=chethy
autologin-session=openbox

```
