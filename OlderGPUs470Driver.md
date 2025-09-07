# 470 Drivers on Debian 13

* I tried installing drivers for _Nvidia GTX 660_, however, it was a bit difficult. I tried various approaches, none were successful, until this one. On top of that, it's quite simple.
* Drivers for these generations of GPUs are _Tesla_ drivers. This one's named _nvidia-tesla-470-driver_.
* I tried installing them from _Sid_, however, I don't like that it prompts me to upgrade all the rest of packages along.
    * I only want to install nvidia drivers, remaining system packages should be from the stable channel.
    * I tried _apt pinning_, but it didn't work as intended.
* However, while looking at [nvidia-tesla-470-driver on the Sid channel](https://packages.debian.org/sid/nvidia-tesla-470-driver), I noticed they are also available in _bookworm-backports_. So, I decided to install them from there.
* Tested on the KDE Desktop X11. It's not recommended to use Wayland for this type of scenario.

```
# if there were previous failed attempts to install nvidia
sudo apt purge "*nvidia*"
sudo apt autoremove

# for enabling 32bit applications (mostly Windows games on Wine)
sudo dpkg --add-architecture i386 && sudo apt update

# prerequisites
sudo apt install linux-headers-amd64

sudo nano /etc/apt/sources.list
# add contrib non-free at the end of every line in sources.list
# also, add bookworm-backports:
deb http://deb.debian.org/debian bookworm-backports main contrib non-free non-free-firmware   
# ctrl + x, y, enter
sudo apt update

# installation itself
sudo apt install -t bookworm-backports nvidia-tesla-470-driver firmware-misc-nonfree

sudo reboot

sudo systemctl enable --now nvidia-suspend.service
sudo systemctl enable --now nvidia-hibernate.service
sudo systemctl enable --now nvidia-resume.service

sudo nano /etc/modprobe.d/nvidia-power-management.conf
# if this entry doesn't exist, add it:
options nvidia NVreg_PreserveVideoMemoryAllocations=1
# ctrl + x, y, enter

sudo systemctl enable --now nvidia-persistenced.service

sudo apt update && sudo apt upgrade
# you can freely upgrade non-free-firmware

# apply all changes and test the 
sudo reboot
```

## Laptop GPUs Dynamic Boost (hybrid mode)

* Only for laptops
* Tested on KDE Desktop
* Weirdly, the _nvidia-powerd_ package installs, but doesn't configure the required files for immediate use

```
sudo apt install nvidia-powerd
sudo cp /usr/share/doc/nvidia-powerd/examples/nvidia-powerd.service /etc/systemd/system/nvidia-powerd.service
sudo nano /etc/systemd/system/nvidia-powerd.service
# Replace /usr/bin/nvidia-powerd with /usr/sbin/nvidia-powerd
# ctrl + x, y, s
sudo cp /usr/share/doc/nvidia-powerd/examples/nvidia-dbus.conf /etc/dbus-1/system.d/
sudo systemctl enable --now nvidia-powerd
```
