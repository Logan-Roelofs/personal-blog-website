---
title: "NixOS on VirtualBox"
date: 2025-06-17
---

First, make sure you have the latest version of VirtualBox installed from https://www.virtualbox.org/wiki/Downloads.

Second, download the **Graphical ISO image** (64-bit Intel/AMD installer) from https://nixos.org/download/#nixos-iso.

## Create the VM

Create a new VM using the **New** button at the top of the VirtualBox interface. In the setup window, configure the VM with the following settings:

- **Name**: NixOS  
- **ISO Image**: Select the downloaded ISO  
- **Type**: Linux  
- **Subtype**: Other Linux  
- **Version**: Other Linux (64-bit)

For hardware allocation:

- **Memory**: At least 4096 MB, recommended 8192 MB  
- **Processors**: At least 2, recommended 8

For storage, use a virtual hard disk:

- **Disk Size**: At least 30 GB, recommended 100 GB

## Start Installation

Now that the VM is created, highlight the NixOS VM and click the **Start** button. To exit the VM input capture while it's running, press the **Right Ctrl** key on your keyboard.

Choose the first boot option. This will launch the NixOS Live CD and open the installer. Go through the following setup steps:

- American English  
- Select your time zone  
- English default keyboard layout  
- Create your username and password  
- Create an admin password as well  
- Choose **GNOME** as your window manager  
- Allow unfree software  
- Erase disk, no swap  
- Click **Install** (If your display is too small, you may need to press Right Ctrl and scroll down to see the button)

**Note**: The installation may take some time and often appears stuck at 46%. Be patient—this is normal.

⚠️ **Important**: Do *not* click "Restart Now" after installation. If you do, it will boot back into the Live CD. Instead, shut down the VM from the VirtualBox menu.

Once the VM is powered off:

1. Go to the VM settings.  
2. Under **System → Boot Order**, move **Hard Disk** to the top.  
3. Under **Storage**, remove the NixOS ISO image.

## Boot into NixOS and have fun!