Of course! Let's give that guide a modern and visually appealing makeover. A well-formatted and stylish README can make the process much more engaging.

Here is a revamped version with emojis, badges, collapsible sections for advanced steps, and a more polished structure.

---

<br/>
<p align="center">
  <img src="https://commons.wikimedia.org/wiki/File:Fedora_icon_(2021).svg" alt="Fedora Logo" width="100"/>
</p>

<h1 align="center">Fedora 42 Post-Install Guide</h1>

<p align="center">
  Your one-stop guide to perfecting a fresh Fedora 42 installation. ✨
  <br/>
  <br/>
  <a href="https://github.com/YOUR_USERNAME/YOUR_REPO/issues">Report an Issue</a>
  ·
  <a href="https://github.com/YOUR_USERNAME/YOUR_REPO/pulls">Request a Feature</a>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Fedora-42-blue?logo=fedora" alt="Fedora 42">
  <img src="https://img.shields.io/badge/License-MIT-green.svg" alt="License">
  <img src="https://img.shields.io/badge/PRs-welcome-brightgreen.svg" alt="PRs Welcome">
</p>

---

This guide provides a curated set of steps to configure your system, install essential software and drivers, and apply performance optimizations.

## 🚀 Initial System Setup

Get your new system ready for action.

1.  **Update The System**

    First, let's get everything up-to-date. Open a terminal and run:
    ```bash
    sudo dnf -y update
    ```
    > **Note:** A reboot is recommended after a major update.

2.  **Enable RPM Fusion & Terra Repositories**

    Unlock a vast library of software (like Steam, Discord) and media codecs that aren't included in Fedora by default.
    ```bash
    sudo dnf install -y https://mirrors.rpmfusion.org/free/fedora/rpmfusion-free-release-$(rpm -E %fedora).noarch.rpm https://mirrors.rpmfusion.org/nonfree/fedora/rpmfusion-nonfree-release-$(rpm -E %fedora).noarch.rpm
    ```

3.  **Enable Flathub**

    Flathub is the biggest source for Flatpak apps. This ensures you have access to everything.
    ```bash
    flatpak remote-add --if-not-exists flathub https://dl.flathub.org/repo/flathub.flatpakrepo
    ```

## ⚙️ Hardware and Drivers

Ensure your hardware is working at its best.

### Update System Firmware
If your system supports it, LVFS can deliver firmware updates for your motherboard and peripherals.
<details>
  <summary>Click to see Firmware Update Commands</summary>

  ```bash
  sudo fwupdmgr refresh --force
  sudo fwupdmgr get-updates
  sudo fwupdmgr update
  ```
</details>

### Install NVIDIA Drivers
For those with NVIDIA GPUs (GeForce 600 series or newer). If you have an older card, the default open-source drivers are often best.
> **Important:** Make sure your system is fully updated before starting.

1.  **Install the driver and optional CUDA package:**
    ```bash
    # Main driver package
    sudo dnf install -y akmod-nvidia

    # Optional: For CUDA support in apps like Blender/DaVinci Resolve
    sudo dnf install -y xorg-x11-drv-nvidia-cuda
    ```

2.  **Wait for modules to build:** This can take 5-10 minutes. Grab a coffee! ☕

3.  **Reboot:** A reboot is required to load the new drivers.

## 🎬 Software & Media Support

Let's get all the necessary codecs and tools for a rich media experience.

### Install Multimedia Codecs
These commands install essential codecs and swap out the default `ffmpeg` for the full version.

```bash
sudo dnf groupupdate -y multimedia
sudo dnf swap -y 'ffmpeg-free' 'ffmpeg' --allowerasing
sudo dnf group install -y sound-and-video
```
<details>
  <summary>Click for advanced GStreamer codec installation</summary>

  This is mainly for apps like GNOME Videos (Totem).
  ```bash
  sudo dnf upgrade -y @multimedia --setopt="install_weak_deps=False" --exclude=PackageKit-gstreamer-plugin
  ```
</details>

### Enable Hardware Video Acceleration
Let your GPU handle video decoding to save CPU cycles and battery life.

```bash
sudo dnf install -y ffmpeg-libs libva libva-utils
```

### Enable OpenH264 for Firefox
Get native H.264 video playback in Firefox.

```bash
sudo dnf install -y openh264 gstreamer1-plugin-openh264 mozilla-openh264
sudo dnf config-manager --set-enabled fedora-cisco-openh264
```
> **Remember:** You still need to pop into Firefox's Add-ons page and enable the "OpenH264" plugin.

## 🔧 System Configuration & Tweaks

Fine-tune your Fedora experience.

### Set a Custom Hostname
Make your machine feel like your own.
```bash
hostnamectl set-hostname your-cool-hostname
```

<details>
  <summary>Manage Kernel Versions</summary>

  By default, Fedora keeps the last 3 kernels. You can reduce this to 2 to save space.
  ```bash
  # Change the limit from 3 to 2
  sudo sed -i 's/installonly_limit=3/installonly_limit=2/' /etc/dnf/dnf.conf
  ```
  You can list installed kernels with `rpm -q kernel-core` and remove an old one (that you aren't currently using) with `sudo dnf remove kernel-core-VERSION`.
</details>

<details>
  <summary>Set Hardware Clock to UTC (for Dual Booting)</summary>

  This simple command can fix time inconsistencies between Fedora and Windows on the same machine.
  ```bash
  sudo timedatectl set-local-rtc '0'
  ```
</details>

<details>
  <summary>Set Up Custom DNS Servers (Optional)</summary>

  Using a privacy-focused DNS provider like Cloudflare can enhance your privacy. This configures DNS-over-TLS.
  ```bash
  sudo tee /etc/systemd/resolved.conf.d/99-dns-over-tls.conf > /dev/null <<EOT
  [Resolve]
  DNS=1.1.1.2#security.cloudflare-dns.com 1.0.0.2#security.cloudflare-dns.com
  DNSOverTLS=yes
  EOT
  ```
</details>

## ⚡ Performance Optimizations

Squeeze a little more performance out of your system.

### Disable CPU Mitigations (Advanced)
> ⚠️ **Security Warning:** This can boost CPU performance by 5-30% on some systems but exposes you to risks like Spectre. **Do not do this** on a server or a machine handling critical data. The performance gain on modern Intel CPUs (10th gen+) is often negligible. Proceed with caution.
<details>
  <summary>Click to see the command</summary>

  ```bash
  sudo grubby --update-kernel=ALL --args="mitigations=off"
  ```
</details>

### Enable NVIDIA DRM/KMS (NVIDIA Laptops)
This can improve compatibility and is needed for some PRIME features on laptops with NVIDIA GPUs.
```bash
sudo grubby --update-kernel=ALL --args="nvidia-drm.modeset=1"
```

### Speed Up Boot Time
The `NetworkManager-wait-online` service can add significant time to your boot process. It's safe to disable on most desktop systems.
```bash
sudo systemctl disable NetworkManager-wait-online.service
```

## 🎨 GNOME Customization

If you're using the default Fedora Workstation, these tweaks can improve your desktop experience.

### Allow Volume Above 100%
For when you *really* need to crank it up.
```bash
gsettings set org.gnome.desktop.sound allow-volume-above-100-percent true
```

### Disable GNOME Software Autostart
Prevent GNOME Software from running in the background to save a surprising amount of RAM.
```bash
sudo rm -f /etc/xdg/autostart/org.gnome.Software.desktop
```

### Recommended GNOME Extensions
-   **Pop Shell:** A fantastic auto-tiling window manager.
    ```bash
    sudo dnf install -y gnome-shell-extension-pop-shell xprop
    ```
-   **GSConnect:** Seamlessly connect your Android phone to your desktop.
    ```bash
    sudo dnf install -y nautilus-python # For file browsing integration
    sudo firewall-cmd --permanent --zone=public --add-service=kdeconnect
    ```
    Then grab the extension from the [GNOME Extensions website](https://extensions.gnome.org/extension/1319/gsconnect/).

---

<p align="center">Made with ❤️ for the Fedora community.</p>
