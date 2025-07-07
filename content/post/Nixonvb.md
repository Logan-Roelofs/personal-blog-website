---
title: "NixOS on Virtual Box"
date: 2025-06-17
---

First off have the latest version of virtual box installed from https://www.virtualbox.org/wiki/Downloads

Second off download the Graphical ISO image 64-bit Intel/AMD installer from https://nixos.org/download/#nixos-iso

## Create the VM 

Create a new VM using The new botten on the top of the VB bar. In the window we will create a vm with the following setting 

- Name: NixOS
- ISO image: Select the downloaded ISO
- Type: Linux
- Subtype: Other Linux
- Version: Other Linux (64-bit)

For hardware allocate
- Memory: at least 4096 MB, recommended 8192 MB
- Processors: at least 2, recommended 8

For our storage we will just use a Virtual Hard Disk
- Disk Size: at least 30 GB, recommended 100 GB


## Start install

Now that we have the VM created highlight the NixOS VM we created and click on the start button on the top. To get our of our VM while we are in it use the right ctrl key on your keyboard.

We will choose the first boot option. This will boot us into the NixOS live CD where the NixOS installer will open up. Now that we have the NixOS installer open we will go through the pages 

- American English 
- Your time zone
- English default 
- Create your username and password
- Create an admin passowrd as well
- For our window manager we can just use Gnome
- Allow unfree software
- Erase disk, No swap
- Install (if your dispaly is small you might have to use right ctrl and scroll down to click this) 
- The install can take a while and you will likly get stuck on 46%, patience is a virtue
- DO NOT CLICK ON RESTART NOW, if you do this it will boot you back into this live CD. What we need to do is power off the VM from the Virtual Box menu.
- Now that the VM is powered off we need to go to the VM settings and change the boot order, under system move Hard Disk all the way up.
- While in the settings go to storage and remove teh NixOS ISO image 

## Boot into NixOS and have fun!