# linux_mint_22_install_on_framework_13_12th_gen_intel
A logbook for installing Linux Mint 22 on a Framework 13'' Laptop 12th Gen Intel

# First things first
## Update software
```console
sudo apt update && sudo apt upgrade
```

## Enable mouse natural scrolling
I'm using a Synaptics driver, I'll add the option to the pointer and touchpad InputClasses
```
Option "NaturalScrolling" "true"
```

Edit the file with the cmd below:
```
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

