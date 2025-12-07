# General Gaming Setup for Debian 13 "Trixie"

This guide is tailored for users running **Debian 13 "Trixie"**.

# Nvidia Drivers Install

**Important:** Disable secure boot in your bios. If you use Secure Boot, you might need to enroll MOK keys for the NVIDIA modules. Refer to the Debian Wiki for "Secure Boot" if you encounter issues after driver installation.

Reboot the system before installing the drivers (especially if you just updated the kernel).

### Add 32-bit platform support
This ensures 32bit drivers will be installed which are required for Steam later.
```bash
sudo dpkg --add-architecture i386
sudo apt update && sudo apt upgrade -y
```

### Add Nvidia GPG Key
- Updated: December 6, 2025
```bash
sudo apt install curl -y # Ensure curl is installed
curl -fsSL https://developer.download.nvidia.com/compute/cuda/repos/debian13/x86_64/8793F200.pub | sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/nvidia-cuda.gpg

sudo apt update
```

### Add NVIDIA repo
- Updated: December 6, 2025
```bash
echo "deb https://developer.download.nvidia.com/compute/cuda/repos/debian13/x86_64/ /" | sudo tee /etc/apt/sources.list.d/nvidia-cuda.list

sudo apt update
```

### Install kernel headers and build tools
```bash
sudo apt install linux-headers-amd64 linux-headers-$(uname -r) build-essential dkms -y
```

### Install NVIDIA Open driver
nvidia-open: NVIDIA Driver meta-package, Open GPU kernel modules, latest version Meta-package containing all the available packages related to the NVIDIA driver.modules.

```bash
sudo apt install nvidia-open -y
```

### Reboot
```bash
sudo reboot
```

### Verify Nvidia Driver Install
```bash
nvidia-smi

sudo dmesg | grep nvidia
```

# Gaming Setup

### Install Steam
```bash
sudo apt install steam-installer -y
```

### Installing Gamemode for on-demand performance
```bash
sudo apt install gamemode -y
```

### Install Flatpak
```bash
sudo apt install flatpak plasma-discover-backend-flatpak -y
```
*Note: `plasma-discover-backend-flatpak` is specific to KDE Plasma's Discover software center. If you are not using KDE Plasma (e.g., on GNOME, XFCE), you might not need this package, or for GNOME, `gnome-software-plugin-flatpak` might be more appropriate. You can omit this package if you only intend to use Flatpak from the command line.*
```bash
sudo flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo
```

### Install OBS Studio and ProtonUp-Qt
Use KDE Discover to install apps from Flatpak

### How to install an App from the command line
```bash
sudo flatpak install -y flathub net.davidotek.pupgui2
```

# System tweaks

### Install Latest Kernel from backports when available
List of Backported packages for Trixie: https://packages.debian.org/trixie-backports/
```bash
sudo apt update
sudo apt install -t trixie-backports linux-image-amd64 linux-headers-amd64 -y
sudo reboot
```

### Makes the system prefer using RAM over disk swap
```bash
sudo sysctl vm.swappiness=10
echo 'vm.swappiness=10' | sudo tee /etc/sysctl.d/99-swappiness.conf > /dev/null
```

## Enable NTSYNC Kernel Module
The ntsync (NT Synchronization Primitive) kernel module is a specialized driver for Linux 
designed to significantly improve the performance and compatibility of Windows applications, 
particularly games, when run through Proton and Wine.

To enable the kernel module to load at boot:
```bash
echo 'ntsync' | sudo tee /etc/modules-load.d/ntsync.conf

# Loads the module immediately without a reboot
sudo modprobe ntsync

# Verify the module is loaded
lsmod | grep ntsync
```

# Troubleshooting

### Uh oh, did your video stop working after a Kernel Update?

It can be a bit of a shock when your video doesn't work after updating your system. This usually happens because the Nvidia driver's Kernel modules need a little nudge to rebuild for your new Kernel. Don't worry, we can walk you through the steps to get things back up and running!

First things first, let's get you to your system's command line. You have a couple of options here:

*   **SSH to the rescue!** If you have an SSH server set up on your computer and another device handy, you can simply SSH in.
*   **No SSH? No problem!** You can also try switching to a different tty session. Once your system has booted up to the point where it seems stuck, just press **(CTRL + ALT + F2)**. This should bring up a text-based login prompt where you can enter your username and password to get to the command line.

Now that you're in, let's make sure your kernel headers are installed and up to date. This is an important step for building the new modules.

```bash
sudo apt update
sudo apt install linux-headers-amd64 linux-headers-$(uname -r) -y
```

Next, let's ensure you have the necessary tools for the job, including the wonderful Kernel module update tool (DKMS).

```bash
sudo apt install build-essential dkms -y
```

With everything in place, it's time to rebuild your Nvidia Kernel Modules for your current Kernel. This one simple command should do the trick!

```bash
sudo dkms autoinstall
```

All that's left is to reboot your computer so it can load the shiny new Nvidia driver.

```bash
sudo reboot
```

And that's it! Hopefully, your video is now working perfectly.
