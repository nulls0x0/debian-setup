# AMD Radeon Gaming Setup for Debian 13 "Trixie"

This guide is tailored for users running **Debian 13 "Trixie"** with AMD Radeon graphics cards.

# AMD Radeon Drivers Install

**Note:** AMD Radeon drivers (AMDGPU) are included in the Linux kernel. This section focuses on installing updated graphics libraries for optimal gaming performance.

Reboot the system before installing the drivers (especially if you just updated the kernel).

### Add 32-bit platform support
This ensures 32-bit graphics libraries will be installed which are required for Steam later.
```bash
sudo dpkg --add-architecture i386
sudo apt update && sudo apt upgrade -y
```

### Install Latest Kernel from backports
- Updated Linux Kernel is available in backports as of December 6, 2025.
- List of Backported packages for Trixie: https://packages.debian.org/trixie-backports/kernel/
```bash
sudo apt update
sudo apt install -t trixie-backports linux-image-amd64 linux-headers-amd64 -y
sudo reboot
```

## AMD Drivers Update

### Install Latest Mesa 3d Lib from backports
```bash
sudo apt install mesa-utils -y
glxinfo | grep "OpenGL version"

sudo apt install -t trixie-backports \
    mesa-vulkan-drivers \
    libgl1-mesa-dri \
    libglx-mesa0 \
    libegl-mesa0 \
    mesa-va-drivers \
    mesa-vdpau-drivers \
    firmware-amd-graphics \
    -y

glxinfo | grep "OpenGL version"
```

## Enable NTSYNC Kernel Module
- Prerequisite: The NTSYNC kernel module is not available with the Debian 13 base Kernel (6.12). You must install the backported Kernel first using the instructions above.

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

## Verify non-free video codecs
```bash
sudo apt install vainfo -y
vainfo
```

### Install Steam
```bash
sudo apt install steam-installer -y
```

### Install Google Chrome
```bash
wget https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb
sudo apt install ./google-chrome-stable_current_amd64.deb -y
```

## Chrome Browser: Hardware Accelerated Video
To help reduce power usage and make our system more efficient when watching video using Chrome, we must add some launch options.

The steps below use the AMD launch options. Substitute the correct options based on your GPU model.

```bash
cp /usr/share/applications/google-chrome.desktop ~/.local/share/applications/
nano ~/.local/share/applications/google-chrome.desktop

# Look for the lines starting with Exec= and add these launch options
--enable-features=AcceleratedVideoDecodeLinuxZeroCopyGL,AcceleratedVideoDecodeLinuxGL,VaapiIgnoreDriverChecks

# Here is a scripted method
FILE_PATH=~/.local/share/applications/google-chrome.desktop
FLAGS=" --enable-features=AcceleratedVideoDecodeLinuxZeroCopyGL,AcceleratedVideoDecodeLinuxGL,VaapiIgnoreDriverChecks"
sed -i 's/ --enable-features=[^ ]*//g' "$FILE_PATH"
sed -i "s/^\(Exec=.*google-chrome-stable\)\(.*\)/\1${FLAGS}\2/g" "$FILE_PATH"
```

### Install Flatpak
```bash
sudo apt install flatpak plasma-discover-backend-flatpak -y
```
*Note: `plasma-discover-backend-flatpak` is specific to KDE Plasma's Discover software center. If you are not using KDE Plasma (e.g., on GNOME, XFCE), you might not need this package, or for GNOME, `gnome-software-plugin-flatpak` might be more appropriate. You can omit this package if you only intend to use Flatpak from the command line.*
```bash
sudo flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo
```

### ProtonUp-Qt (Flatpak)
1. Open **Discover Software Manager**
2. In the search input enter **proton**
3. Select the **ProtonUp-Qt** Flatpak (From Flathub) package
4. Click **Install**

### Minecraft Launcher 'Prism Launcher' (Flatpak)
1. Open **Discover Software Manager**
2. In the search input enter **prism**
3. Select the **Prism Launcher** Flatpak (From Flathub) package
4. Click **Install**

### Roblox Launcher 'Sober' (Flatpak)
1. Open **Discover Software Manager**
2. In the search input enter **sober**
3. Select the **Sober** Flatpak (From Flathub) package
4. Click **Install**

### OBS Studio (Flatpak)
1. Open **Discover Software Manager**
2. In the search input enter **obs**
3. Select the **OBS Studio** Flatpak (From Flathub) package
4. Click **Install**

# Part III: KDE Desktop Tweaks
### Set Maximum Refresh Rate for Your Display

Ensuring your display is set to its maximum refresh rate is crucial for smooth gaming. Higher refresh rates provide a better gaming experience with reduced input lag and smoother motion.

1. Open **System Settings**
2. Go to **Display & Monitor**
3. Select your monitor
4. Under **Refresh rate**, select the highest available option (e.g., 144 Hz, 165 Hz, 240 Hz)
5. Click **Apply**

### Enable Gsync or FreeSync

To enable variable refresh rates for your games.

1. Open **System Settings**
2. Go to **Display & Monitor**
3. Select your gaming monitor
4. Under **Adaptive sync**, select the **Automatic** or **Always** option
5. Under **Screen tearing** check **Allow in fullscreen windows**
6. Click **Apply**

Automatic: 
Adaptive Sync is only enabled when an opaque fullscreen window is in focus. This is typically a full-screen game or a full-screen video player. Adaptive Sync will not be active for windowed games, desktop navigation, or other non-fullscreen content.

Always:
Adaptive Sync is permanently enabled. The display's refresh rate will constantly adjust to match the frame rate of the entire screen, including desktop elements, windows, and video content. It can cause the screen to noticeably flicker or feel sluggish with low-framerate content.

### Disable Mouse Acceleration

Mouse acceleration can negatively impact gaming performance, especially in FPS games where precise aim is crucial. Disabling it provides consistent 1:1 mouse movement.

1. Open **System Settings**
2. Go to **Mouse and Touchpad**
3. Select **Mouse**
4. Select your mouse in the **Device** drop down
5. Un-check the **Enable pointer acceleration** check box
6. Click **Apply**

### Enable CPU Performance Governor

For better gaming performance, you can set your CPU governor to performance mode:

1. On the Taskbar left click **Show hidden icons** arrow
2. Open **Power Management**
3. Move the **Power Profile** slider to **Performance**

### Installing Gamemode for on-demand performance
```bash
sudo apt install gamemode -y
```

### Configure Gamemode User Permissions
After installing Gamemode, you need to add your user to the `gamemode` group. This grants the Gamemode daemon permission to renice (prioritize) processes and change CPU power states for optimal gaming performance.

Add your user to the gamemode group:
```bash
sudo usermod -a -G gamemode $USER
```

**Important:** You must log out and log back in (or reboot) for the group membership to take effect.

After logging back in, verify that you're now in the gamemode group:
```bash
groups | grep gamemode
```

If the command returns `gamemode`, you're all set! The Gamemode daemon will now be able to optimize your system during gaming sessions.

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

### Makes the system prefer using RAM over disk swap
```bash
sudo sysctl vm.swappiness=10
echo 'vm.swappiness=10' | sudo tee /etc/sysctl.d/99-swappiness.conf > /dev/null
```

# Build and Install Gamescope
Gamescope is a micro-compositor that can improve gaming performance by providing better frame pacing and allowing games to run at different resolutions than your display. Building from source ensures you get the latest features and optimizations.

**Prerequisite:** This process requires build tools. If you followed the kernel installation steps earlier, these should already be installed.

### Install build prerequisites
```bash
sudo apt install devscripts build-essential -y
```

### Download and build gamescope
```bash
mkdir ~/gamescope-backport && cd ~/gamescope-backport

# Download the gamescope source package
dget -u https://deb.debian.org/debian/pool/contrib/g/gamescope/gamescope_3.16.15-2.dsc

cd gamescope-*/

# Create and install build dependencies
mk-build-deps
sudo apt install ./gamescope-build-deps_*.deb -y

# If apt warns about missing packages, run: sudo apt --fix-broken install

# Build the package (-uc, -us: Skip GPG signing for personal use)
dpkg-buildpackage -b -uc -us

# Install the built package
sudo apt install ../gamescope_*.deb -y
```

### Configure gamescope for high priority scheduling
For gamescope to run smoothly without stuttering, it needs permission to set "High Priority":
```bash
sudo setcap 'CAP_SYS_NICE=eip' $(which gamescope)
```

### Cleanup build files
```bash
cd ~
sudo apt autoremove gamescope-build-deps -y
rm -rf ~/gamescope-backport
```