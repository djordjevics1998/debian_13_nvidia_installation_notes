# Modern GPUs on Debian 13

* This is a summed-up note from the [NvidiaGraphicsDrivers](https://wiki.debian.org/NvidiaGraphicsDrivers) for installing proprietary _open_ flavor drivers (Currently 550 version)
* Tested on the KDE Desktop Wayland

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
# ctrl + x, y, enter

# installation itself
sudo apt install nvidia-open-kernel-dkms nvidia-driver firmware-misc-nonfree

sudo nano /etc/default/grub.d/nvidia-modeset.cfg
# if this entry doesn't exist, add it:
GRUB_CMDLINE_LINUX="$GRUB_CMDLINE_LINUX nvidia-drm.modeset=1 nvidia-drm.fbdev=1"
# ctrl + x, y, enter

sudo systemctl enable --now nvidia-suspend.service
sudo systemctl enable --now nvidia-hibernate.service
sudo systemctl enable --now nvidia-resume.service

sudo nano /etc/modprobe.d/nvidia-power-management.conf
# if this entry doesn't exist, add it:
options nvidia NVreg_PreserveVideoMemoryAllocations=1
# ctrl + x, y, enter

sudo systemctl enable --now nvidia-persistenced.service

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
