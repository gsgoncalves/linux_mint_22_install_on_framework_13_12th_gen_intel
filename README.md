# Install Linux Mint 22 (XFCE) on Framework 13 Inch (12th Gen Intel) Laptop
A logbook for installing Linux Mint 22 (XFCE) on a Framework 13'' Laptop 12th Gen Intel

# First things first
## Update software
```console
sudo apt update && sudo apt upgrade
```

## Enable mouse natural scroll
I'm using a Synaptics driver, I'll add the option to the pointer and touchpad InputClasses
```text
Option "NaturalScrolling" "true"
```

Edit the file with the cmd below:
```console
sudo nano /usr/share/X11/xorg.conf.d/40-libinput.conf
```

It should look like this:
```text
Section "InputClass"
        Identifier "libinput pointer catchall"
        MatchIsPointer "on"
        MatchDevicePath "/dev/input/event*"
        Driver "libinput"
        Option "NaturalScrolling" "true"
EndSection

Section "InputClass"
        Identifier "libinput keyboard catchall"
        MatchIsKeyboard "on"
        MatchDevicePath "/dev/input/event*"
        Driver "libinput"
EndSection

Section "InputClass"
        Identifier "libinput touchpad catchall"
        MatchIsTouchpad "on"
        MatchDevicePath "/dev/input/event*"
        Driver "libinput"
        Option "NaturalScrolling" "true"
EndSection

Section "InputClass"
        Identifier "libinput touchscreen catchall"
        MatchIsTouchscreen "on"
        MatchDevicePath "/dev/input/event*"
        Driver "libinput"
EndSection

Section "InputClass"
        Identifier "libinput tablet catchall"
        MatchIsTablet "on"
        MatchDevicePath "/dev/input/event*"
        Driver "libinput"
EndSection
```

## Disable pesky battery notifications
```text
From the main menu:
- "All applications" -> "Notifications" -> "Applications" -> "Power Manager" -> "Mute application"
```

## Add more 3:2 resolutions
To enable the 1920x1280, 1620x1080, and 1500x1000 resolutions, add the following commands to the bottom of the/.profile file.
Note: While some resolutions below differ slightly these were the values generated by cvt. 
```console
nano ~/.profile
```

```text
# Adding custom xandr cmds for more resolutions
xrandr --newmode "1920x1280-60.00"  206.25  1920 2056 2256 2592  1280 1283 1293 1327 -hsync +vsync
xrandr --addmode eDP-1  "1920x1280-60.00"
xrandr --newmode "1624x1080-60.00"  145.50  1624 1728 1896 2168  1080 1083 1093 1120 -hsync +vsync
xrandr --addmode eDP-1  "1624x1080-60.00"
xrandr --newmode "1504x1000-60.00"  124.25  1504 1600 1752 2000  1000 1003 1013 1038 -hsync +vsync
xrandr --addmode eDP-1  "1504x1000-60.00"
```

## Increase Font Sizes across the OS
The following configs are comfortable:
- Settings -> Display -> Resolution: 1500x1000 (1504x1000)
- Settings -> Appearance -> Fonts:
  - Style: Mint-X-Blue
  - Font Size: 14
  - Custom DPI Setting: 110
- Settings - > Window Manager -> Style -> Font size: 16

## Add fingerprint to reader
```command
fprintd-enroll -f right-middle-finger
sudo pam-auth-update
```

## Shortcuts
### Disable opening the applications menu when pressing the super key (Useful for Albert and Window Tiling with small keyboards)
- Settings -> Keyboard -> Application Shortcuts -> xfce4-popup-whiskermenu -> Remove

### Fix Window Tiling
- Settings - > Window Manager -> Keyboard:
  - Look for "Tile window ..." and fix the shortcuts if needed.

### Lock Screen
- Settings -> Keyboard -> Application Shortcuts:
  - Add this command: light-locker-command --lock
  - Set to combo: Super + L

## Stack Windows on the taskbar
- Right click on the taskbar -> Panel -> Panel Preferences
- Items -> Double click Window Buttons -> Group windows by application

## Installing sensors visualization on the taskbar
```console
sudo apt update
sudo apt install xfce4-sensors-plugin
```

- Right click on the taskbar -> Panel -> Panel Preferences
- Items -> Add:
  - Sensor plugin
  - System Load Monitor
- Select options needed and sort at will with the panel preferences arrows 

## Enable hibernation
```console
swapon --show
```
Create swap partition and enable it if needed.

- Take note of the UUID of swap with blkid
```console
blkid
```

Edit the grub line and add the UUID to the GRUB_CMDLINE_LINUX_DEFAULT. For example:
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash resume=UUID=52ce6d2e-ee1f-44f7-b741-95716f377051"

```console
sudo nano /etc/default/grub
sudo update-grub
```

Create a PolicyKit Rule:
```console
sudo xed /etc/polkit-1/rules.d/10-enable-hibernate.rules
```

Add the following lines:
```text
polkit.addRule(function(action, subject) {
    if (action.id == "org.freedesktop.login1.hibernate" ||
        action.id == "org.freedesktop.login1.hibernate-multiple-sessions" ||
        action.id == "org.freedesktop.upower.hibernate" ||
        action.id == "org.freedesktop.login1.handle-hibernate-key" ||
        action.id == "org.freedesktop.login1.hibernate-ignore-inhibit") {
        return polkit.Result.YES;
    }
});
```

## Enable auto-connection on headphones and other Bluetooth devices

1. Edit the following file:
   ```bash
   sudo nano /etc/bluetooth/main.conf
   ```
   1.1. Make sure the following option is enabled:
   ```text
   AutoEnable=true
   MultiProfile = multiple
   ```
   
   1.2. (Optional) Get device UUIDs to configure the reconnection policy:
   - Enter Bluetooth control, list devices, trust devices (using MAC ID), and get the UUIDs (using MAC ID).
   ```bash
   bluetoothctl
   devices
   trust XX:XX:XX:XX:XX:XX
   info XX:XX:XX:XX:XX:XX
   ```
   **Note**: Depending on the device type, it may list multiple UUIDs. Important UUIDs for the reconnection policy include:

   - **Headphones**:
     - UUID: Headset
     - UUID: Advanced Audio Distribution Profile (A2DP)
     - UUID: Audio Sink
   - **Mice**:
     - UUID: Human Interface Device
   - **Keyboards**:
     - UUID: Human Interface Device

   1.3. (Optional) Configure the reconnection policy:
   ```text
   ReconnectUUIDs=00001108-0000-1000-8000-00805f9b34fb,0000110b-0000-1000-8000-00805f9b34fb,0000110d-0000-1000-8000-00805f9b34fb,00001812-0000-1000-8000-00805f9b34fb,00001124-0000-1000-8000-00805f9b34fb
   ReconnectAttempts=13
   ReconnectIntervals=1,2,4,8,16,32,32,32,64,64,64
   ```
## Auto restart Bluetooth upon boot
For some quirky reason, Linux Mint does not let me change my Headphones (WH-1000XM4) codec from HFP to A2DP when the laptop starts.
If I restart the Bluetooth service it will pick the A2DP. Hence I created an auto restart service as follows:
```console
sudo nano /etc/systemd/system/bluetooth-restart.service
```

Copy the following contents to the service:
```text
[Unit]
Description=Restart Bluetooth Service
After=bluetooth.service

[Service]
Type=oneshot
ExecStart=/bin/systemctl restart bluetooth

[Install]
WantedBy=multi-user.target
```

Save the file, enable the service, and reload daemon.
```console
sudo systemctl enable bluetooth-restart.service
sudo systemctl daemon-reload
```

Optional: Test the new service
```console
sudo systemctl start bluetooth-restart.service
```

# Productivity Suite

I felt that Mint was missing a snappy all-round search functionality like the one that Ubuntu implements.
I found this cool project, even better than what I used in Ubuntu, and resembles Mac's Spotlight.

## Installing the [Albert Launcher](https://albertlauncher.github.io/)
```console
echo 'deb http://download.opensuse.org/repositories/home:/manuelschneid3r/xUbuntu_24.04/ /' | sudo tee /etc/apt/sources.list.d/home:manuelschneid3r.list
curl -fsSL https://download.opensuse.org/repositories/home:manuelschneid3r/xUbuntu_24.04/Release.key | sudo gpg --dearmor | sudo tee /etc/apt/trusted.gpg.d/home_manuelschneid3r.gpg > /dev/null
sudo apt update
sudo apt install albert
```
### Shortcut
To enable the Alt+Space shortcut, you need to disable the "Window operations menu" shortcut.
- Settings - > Window Manager -> Keyboard -> Clear the "Window operations menu" shortcut
- Then enable the shortcut on Albert's Settings

### Autostart
```console
cp /usr/share/applications/albert.desktop ~/.config/autostart/
chmod +x ~/.config/autostart/albert.desktop
```
