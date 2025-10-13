# General Gaming Setup for Linux Mint 22.2 "Wilma"

This guide is tailored for users running **Linux Mint 22.2 "Wilma"** (based on Ubuntu 24.04 LTS). 

# Nvidia Drivers Install

**Important:** Disable secure boot in your bios. If you use Secure Boot, you might need to enroll MOK keys for the NVIDIA modules. Refer to the Ubuntu/Mint documentation for "Secure Boot" if you encounter issues after driver installation.

Reboot the system before installing the drivers (especially if you just updated the kernel).

### Add 32-bit platform support
This ensures 32bit drivers will be installed which are required for Steam later.
```bash
sudo dpkg --add-architecture i386
sudo apt update && sudo apt upgrade -y
```

### Add the graphics-drivers PPA
This PPA provides the latest NVIDIA drivers for Ubuntu-based distributions:
```bash
sudo add-apt-repository ppa:graphics-drivers/ppa -y
sudo apt update
```

### Install kernel headers and build tools
These are required for building the NVIDIA kernel modules:
```bash
sudo apt install linux-headers-$(uname -r) build-essential dkms -y
```

### Install NVIDIA Open Kernel Driver
The NVIDIA open kernel driver provides better performance and integration with the Linux kernel.
This is recommended for RTX 20 series (Turing) and newer GPUs.

First, check what drivers are available for your system:
```bash
ubuntu-drivers devices | grep 'nvidia-driver.*-open'
```

Then install the latest NVIDIA open driver: i.e. nvidia-driver-[version]-open
If you aren't sure which to select, use the driver with 'recommended' tagged on the end
```bash
# Install the latest available NVIDIA open driver
sudo apt install nvidia-driver-[version]-open -y
```

### Reboot
```bash
sudo reboot
```

### Verify Nvidia Driver Install
```bash
nvidia-smi
```

# Gaming Setup

### Install Steam
```bash
sudo apt install steam-installer -y
```

### Installing Gamemode for on-demand performance
Gamemode automatically optimizes system performance when games are running.
```bash
sudo apt install gamemode -y
```

To verify gamemode is working:
```bash
gamemoded -t
```

### Enable Gamemode for Steam Games

To enable gamemode for your Steam games, you need to add a launch option to each game:

**For Individual Games:**
1. Open your Steam Library
2. Right-click on the game you want to optimize
3. Select **Properties**
4. In the **Launch Options** field, add:
   ```
   gamemoderun %command%
   ```
5. Close the properties window and launch the game

**Verify Gamemode is Active:**
While a game is running with gamemode enabled, you can check if it's active:
```bash
gamemoded -s
```

This will show you the status and which processes are currently using gamemode.

### Install Useful Gaming Tools

**ProtonUp-Qt** - Manage Proton-GE and other compatibility tools for Steam:
```bash
flatpak install flathub net.davidotek.pupgui2 -y
```

# System tweaks

### Set Maximum Refresh Rate for Your Display

Ensuring your display is set to its maximum refresh rate is crucial for smooth gaming. Higher refresh rates provide a better gaming experience with reduced input lag and smoother motion.

1. Open **System Settings**
2. Go to **Display**
3. Select your monitor
4. Under **Refresh rate**, select the highest available option (e.g., 144 Hz, 165 Hz, 240 Hz)
5. Click **Apply**

### Disable Mouse Acceleration

Mouse acceleration can negatively impact gaming performance, especially in FPS games where precise aim is crucial. Disabling it provides consistent 1:1 mouse movement.

**Using Cinnamon Settings**

1. Open **System Settings**
2. Go to **Mouse and Touchpad**
3. Under the **Mouse** tab
4. Under **Acceleration** set it to **Constant**

### Enable CPU Performance Governor (Optional)

For better gaming performance, you can set your CPU governor to performance mode:
```bash
# Install cpufrequtils
sudo apt install cpufrequtils -y

# Set to performance mode (temporary - until reboot)
sudo cpufreq-set -g performance

# To make it permanent, edit the config file:
echo 'GOVERNOR="performance"' | sudo tee /etc/default/cpufrequtils > /dev/null
sudo systemctl restart cpufrequtils
```

### Makes the system prefer using RAM over disk swap
```bash
sudo sysctl vm.swappiness=10
echo 'vm.swappiness=10' | sudo tee /etc/sysctl.d/99-swappiness.conf > /dev/null
```

### Disable Compositing for Full Screen Applications (Cinnamon)

Disabling the compositor for full screen applications can significantly improve gaming performance by reducing input lag and increasing FPS. This is especially beneficial for fast-paced games.

1. Open **System Settings**
2. Go to **General** (or search for "compositor")
3. Under the **Desktop effects** section, enable the option:
   - ☑ **"Disable compositing for full-screen windows"**

**Note:** After enabling this setting, the compositor will automatically disable when you run games or applications in full screen mode, and re-enable when you exit. This provides the best of both worlds - smooth desktop effects when needed, and maximum performance during gaming.

# Troubleshooting

### Uh oh, did your video stop working after a Kernel Update?

It can be a bit of a shock when your video doesn't work after updating your system. This usually happens because the Nvidia driver's Kernel modules need a little nudge to rebuild for your new Kernel. Don't worry, we can walk you through the steps to get things back up and running!

First things first, let's get you to your system's command line. You have a couple of options here:

*   **SSH to the rescue!** If you have an SSH server set up on your computer and another device handy, you can simply SSH in.
*   **No SSH? No problem!** You can also try switching to a different tty session. Once your system has booted up to the point where it seems stuck, just press **(CTRL + ALT + F2)**. This should bring up a text-based login prompt where you can enter your username and password to get to the command line.

Now that you're in, let's make sure your kernel headers are installed and up to date. This is an important step for building the new modules.

```bash
sudo apt update
sudo apt install linux-headers-$(uname -r) -y
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