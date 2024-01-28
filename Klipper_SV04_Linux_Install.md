# Introduction 

There are many different paths to take for installing Klipper on the SOVOL-SV04.  The following is a detailed step-by-step guide on how to install Klipper with the Display option using Kiauh (Klipper Installation and Update Helper) and the [Bully85 Github](https://github.com/Bully85/Sovol-SV04-Klipper) software on a clean install of any rpm based Linux such as Alma Linux, Rocky Linux, RHEL or similar distros.

##### Throughout this document the system will be referred to as _klinux_ and the user name that is running commands will be _klipper_.

### IMPORTANT NOTE:  Different configs are required for depending on which chipset was used to build the mainboard on your SV04 printer.  The chipset is either STM or GD.  Figure out how you will proceed before moving forward with this install.

There are 2 ways to verify which chipset is on your mainboard.  
1) Remove the bottom cover of the printer mainboard and look if the chipset says STM32F* or GD32F*.  
2) You can install Klippper and see if there is an error message with the z-Tilt.

This guide will help you set up your SV04 with Klipper, including the COPY and MIRROR modes.

#### [Bully85 Github Release Notes](https://github.com/Bully85/Sovol-SV04-Klipper/releases)


# Sovol-SV04-Klipper
This repository contains all the necessary configuration files for the SV04 to work with Klipper's Mirror and Copy modes, including images and descriptions.  The single extruder modes work with the standard configuration files.

![KlipperSV04](docs/img/sv04klipper.png)


## Requirements

- Hardware
  -- Most any 64 bit micro computer that can run a modern rpm based linux distro.  Intel Nuc for instance.
- Operating System
  -- rpm based Linux (Alma Linux, Rocky Linux or any other RHEL style distro)
- Network connectivity, wifi or ethernet
- sshd enabled
- Base package requirements:
  -- git
  -- python3
  -- wget
  -- unzip
  -- fwupd
  -- curl
- Root and/or sudo permission
  
- Original 7" touch screen: Intall new DWIN after Klipper setup. [directions included below](#Installing-touchscreen-software)
- Terminal Window for command line ```SSH``` commands. Choose One:
    - Linux: gnome-terminal, konsole, terminator 
    - Windows: Powershell, [Git Bash](https://gitforwindows.org/), or [Putty](https://putty.org/) for Windows Terminal for SSH 
    - Mac: [iterm2](https://iterm2.com/) app, or terminal on Mac OS
- For secure file transfer SFTP or SCP. Choose One:
    - Linux: [FileZilla](https://filezilla-project.org/)
    - Linux: ```/usr/bin/scp``` or ```/usr/bin/sftp```
    - Windows: [FileZilla](https://filezilla-project.org/)
    - Windows: [WinScp](https://winscp.net)
    - Mac: Use ```scp``` command in mac terminal (or iterm2)
- [The configuration files provided in Bully85 repository](https://github.com/Bully85/Sovol-SV04-Klipper/tree/main/config)
- SD Card to flash the SV04 (max 8GB, formatted in Fat32 4096)
- Optional: 
  - USB Web Camera
  - [Creating SSH keys: ssh-keygen MAC or linux](https://www.digitalocean.com/community/tutorials/how-to-create-ssh-keys-with-openssh-on-macos-or-linux)
  - [Creating SSH keys: ssh-keygen on Windows](https://docs.digitalocean.com/products/droplets/how-to/add-ssh-keys/create-with-putty/)

## Features

- Copy Mode (supports different temperatures but does not support first layer settings for the right Extruder)
- Mirror Mode (supports different temperatures but does not support first layer settings for the right Extruder)
- Dual Mode
- Single Mode
- Bed Mesh Levelling
- Input Shaping
- Display Functionality
- And much more

# Installation on a new OS 

From your Mac or PC you will need to remotely connect to klinux.
If you do not know how to find the ip of your newly connected device, you may need to do some researching to find the IP.  

# Using SSH

After you determine the ip address of klinux, you will connect to it remotely.
On Mac or PC: 
- Option 1: Using a terminal window (Git Bash, Powershell, iTerm2).  
The Terminal is usually quicker and easier.
  - remember the username and hostname are the ones you set up when you installed your Linux system.
  - connect by typing the following:

     ```ssh klipper@klinux`` 
     
  - and enter your password when prompted

## Install Kiauh

Read the [Kiauh instructions](https://github.com/th33xitus/kiauh), then follow the install instructions.  After KIAUH is installed on klinux, perform the following actions for the Bully85 installation.

You are already logged into klipper@klinux via ssh.  Using your preferred editot (nano, emacs, vi) edit kiauh/klipper_repos.txt:
```sh 
sudo vim kiauh/klipper_repos.txt.example
```

- Add the following line at the end (see image below):
```sh 
https://github.com/Bully85/klipper
```
![KiauhSV04](docs/img/klipper_repos.txt.PNG)

- Save the file. 
- Rename the file to remove ".example" from the file name and press Enter, then Y. 
  - example: ```cp kiauh/klipper_repos.txt.example kiauh/klipper_repos.txt```
- Run Kiauh with the command 
```sh 
./kiauh/kiauh.sh
```
### Kiauh Settings Selections:

Kiauh program has started and you will need to choose the following selections by typing in a number:
```
- [6] Select Settings
- [1] To set the custom Klipper repository

- [4] Choose "Bully85/klipper"
- [Y] Confirm everything (This will remove the existing Klipper. This is what we want)
```

You can now go back (B) and then finally quit (Q).

# Transferring Files 

Now, the files and folders from the github repo: https://github.com/Bully85/Sovol-SV04-Klipper/tree/main/config directory must be copied to your printer's directory on the Raspberry Pi. 

The default directory on your raspberrypi is "printer_data".

To get the data over to the raspberry pi you can direcly clone it or clone it on your mac or PC.
I did the following on my mac and then used scp to copy the files onto the raspberry pi:
```
$ mkdir ~/version-control
$ cd ~/version-control
$ git clone https://github.com/Bully85/Sovol-SV04-Klipper.git
$ cd Sovol-SV04-Klipper.git
$ scp -r ~/version-control/frk/Sovol-SV04-Klipper/config/SV04-with-display <user name>@<raspberry pi IP address>:~/printer_data/config/.

For an add reason my mac is having trouble with regex "*" so I had to do an extra step on the raspberry pi
$ ssh <username>@<raspberry pi address>
$ cd ~/printer_data/config/SV04-with-display
$ cp -r * ../.
$ cd ..
$ rm -rf SV04-with-display
```

You can also easily migrate the files using SFTP or SCP Program, such as FileZilla or WinScp [mentioned above](#requirements)

# Install Firmware on Printer

On your Mac or PC.  The firmware.bin file from the cloned repo directory:

 ```~/version-control/frk/Sovol-SV04-Klipper/Firmware.bin``` 
 
 needs to be copied to a Full Size SD card and flashed onto the printer with the original display unplugged (the original display is not needed at this stage as it will not function). The 8GB SD card should be formatted with 4096 segments.

```
$ diskutil list
Located your SD card (the example is disk4, your set-up may be different)
$ sudo newfs_msdos -F 32 -c 8 -v TM3D /dev/<disk4>
Unplug the SD card and plug it back in to Mac or PC for the SD card to mount
$ cp ~/version-control/frk/Sovol-SV04-Klipper/Firmware\ bin/firmware.bin /Volumes/TM3D/.

Eject SD Card
Take SD card and insert into SV04 printer while it is off
Turn the printer on with the card in the printer.  Wait a few minutes.
Turn the printer off.

Connect the mini-usb cable to the printer and restart the Raspberry pi from the UI.

The browser will have the same url as the one you used for ssh.

```
### IMPORTANT NOTE: I had issues flashing the firmware.... or at least I thought I did.  I believe the mini-usb cable was not the proper type to transfer data, only power, or the cables was just bad.  So, the mainsail never appeared to register that there was a printer connected.  Here is a [link](https://superuser.com/questions/1269449/identifying-data-transfer-micro-usb-cables-vs-charge-only-micro-usb-cables#:~:text=%2Done%20is%20a%20charging%20cable,a%20Charge%20Cable%2C%20That's%20It.).  

The files from the "config" directory should now be transferred to the Raspberry Pi as described [below](#transferring-files), or directly in the Mainsail interface, which can be accessed by using the IP address or Hostname.


# Installing touchscreen software
- Requirements 
  - Allen wrenches - 2 sizes
  - MicroSD card
Prepare the SD card - Goto https://github.com/Bully85/DGUS-SV04/tree/master/project/DWIN_SET/DWIN_SET.rar and download the DWIN_SET.rar folder to the SD card.  Unzip it and discard the original ".rar" file.

To install the touchscreen software on the original SV04 touchscreen you will need to remove the 2 screws that attach the screen to the printer.  Disconnect the cable.  Then remove the 4 allen screws that attach the back plate to the screen.  Place the screen on a cloth while removing the screws.  Be careful not to lose the screws and handle the screen with care.  
Insert the MicroSD care into the bottom of the touchscreen microprocessing board.  
With the printer off, re-insert the cable to the touchscreen board.
Turn the printer on.  The touchscreen should show up with a blue screen and then there will be numbers.  They should not be all zeros (all zeros is likely a failure)
Once the flash is complete remove the SD card.  Turn the printer on with the raspberry pi connected and on and all should be working.


# Fixing the Invalid Message in Mainsail

To fix the invalid message in Mainsail, SSH into Klipper and execute the following command:

```sh 
sudo nano ~/printer_data/systemd/moonraker.env
```
![moonraker.env1](docs/img/moonraker.env1.JPG)

Append "-g"  to the end of the "MOONRAKER_ARGS" Line: 
![moonraker.env2](docs/img/moonraker.env2.JPG)

Quit and save, then reboot. 

In Mainsail, click on 'invalid' and perform a soft repair. Your setup should now be ready!

# update
Change the Startcode in the Slicer [Cura](Cura Profile/Startcode.md) [Prusa_Slicer](Prusaslicer Profile/Startcode.md)

# Spenden/Donations

[![Donate with PayPal](https://raw.githubusercontent.com/stefan-niedermann/paypal-donate-button/master/paypal-donate-button.png)](https://www.paypal.com/donate/?hosted_button_id=L85ULXXQKALP6)
