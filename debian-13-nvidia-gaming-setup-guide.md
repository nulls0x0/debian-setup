# General Gaming Setup for Debian 13 "Trixie"

This guide is tailored for users running **Debian 13 "Trixie"**. Please replace placeholders like `<YOUR_USERNAME>` and `<your_home_folder>` with your actual username and home directory path.

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
We use the Nvidia Debian 12 repo until Nvidia publishes a repo for Debian 13
```bash
sudo apt install curl -y # Ensure curl is installed
curl -fsSL https://developer.download.nvidia.com/compute/cuda/repos/debian12/x86_64/3bf863cc.pub | sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/nvidia-cuda.gpg

sudo apt update
```

### Add NVIDIA repo
```bash
echo "deb https://developer.download.nvidia.com/compute/cuda/repos/debian12/x86_64/ /" | sudo tee /etc/apt/sources.list.d/nvidia-cuda.list

sudo apt update
```

### Install kernel headers and build tools
```bash
sudo apt install linux-headers-$(uname -r) build-essential dkms -y
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
