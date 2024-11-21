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

## Add more 3:2 resolutions
To enable the 1920x1280, 1620x1080, and 1500x1000 resolutions, add the following commands to the bottom of the/.profile file.
Note: While some resolutions below differ slightly these were the values generated by cvt. 
```console
nano ~/.profile
```

```text
# Adding custom xandr cmds for more resolutions
xrandr --newmode "1920x1280_60.00"  206.25  1920 2056 2256 2592  1280 1283 1293 1327 -hsync +vsync
xrandr --addmode eDP-1  "1920x1280_60.00"
xrandr --newmode "1624x1080_60.00"  145.50  1624 1728 1896 2168  1080 1083 1093 1120 -hsync +vsync
xrandr --addmode eDP-1  "1624x1080_60.00"
xrandr --newmode "1504x1000_60.00"  124.25  1504 1600 1752 2000  1000 1003 1013 1038 -hsync +vsync
xrandr --addmode eDP-1  "1504x1000_60.00"
```

# Productivity Suite
## Installing the Albert Launcher
```console
echo 'deb http://download.opensuse.org/repositories/home:/manuelschneid3r/xUbuntu_24.04/ /' | sudo tee /etc/apt/sources.list.d/home:manuelschneid3r.list
curl -fsSL https://download.opensuse.org/repositories/home:manuelschneid3r/xUbuntu_24.04/Release.key | sudo gpg --dearmor | sudo tee /etc/apt/trusted.gpg.d/home_manuelschneid3r.gpg > /dev/null
sudo apt update
sudo apt install albert
```
