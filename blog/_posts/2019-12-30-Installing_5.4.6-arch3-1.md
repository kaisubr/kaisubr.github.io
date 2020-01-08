---
layout: post
category: blog
title: Installing Arch
---

... because why not? 

## Preface

The steps below were tested on VirtualBox, but should be replicable for most systems. A lot of posts online can be outdated so this is a quick write-up for installation. 

## Install it!

### Retrieve the ISO

Download the .iso from https://archlinux.org/download. Use a mirror close to you.

### (VM only) Configure the virtual disk image

The minimum bare-bones installation takes under 5GB. Use about 10-20GB to ensure installation is successful and a little extra for the swap partition.

### Insert into optical drive, then boot

This is simple when using a VM, just hop into the settings, create a optical disk, and select the .iso you used.

Boot into Arch Linux (x86_64).

### Verify Internet connectivity

Ping a site to check that you're connected to the Internet: `ping -c 3 google.com`. There shouldn't be any issues since the VM takes care of that using your host connectivity.

### Partitioning the drive

View the current drives by `fdisk -l`. Typically we are partitioning the `a` drive.

Enter cfdisk by `cfdisk /dev/sda`. Select `dos` and create two partitions: 

* The first is a primary partition, with the `bootable` flag and is most of the drive (in this case 18G). Write the changes to the table.
* We let the remainder be the second primary partition, a swap partition that is twice the RAM (in this case 2x1G) with `type` 82 (Linux swap/Solaris).

Write changes to the partition table and quit.

### Create and mount the filesystem & swap

To create the filesystem let's format it as ext4: `mkfs.ext4 /dev/sda1`. 

Then, create the swap space: `mkswap /dev/sda2`; it is similar to virtual memory, but it is used when the RAM is full. This is especially useful when using VMs because RAM is often limited and is a common bottleneck for performance.

To mount the filesystem, run `mount /dev/sda1 /mnt`.

To activate the swap, run `swapon /dev/sda2`.

### Install base Arch 

Here's where a lot of articles differ in installing Arch. The most effective command includes `linux` and `linux-firmware` since it provides items, including the kernel, that are left out when simply running `base-devel`:

```
pacstrap /mnt base base-devel linux linux-firmware nano
```

I've found that leaving out `linux` and `linux-firmware` causes GRUB to launch into the `grub>` terminal, since it needs the linux/linuz or initrd images even though it reports no error after mkconfig.

### Generate fstab

Generate fstab on the mounted partition using `genfstab -U /mnt >> /mnt/etc/fstab`.

### chroot

Now that we've installed Arch through the ISO, we can change the root to the Arch system by `arch-chroot /mnt`.

Note that if you are chroot-ing into a previous installation, you should mount first (`mount /dev/sda1 /mnt`).

On Arch, you might want to install Vim to edit some files, in which case you can install it via `pacman -S vim`. 

### Locale configuration, hostname, and DHCP

Uncomment your preferred locale (such as en_US.UTF-8 UTF-8) on the locale.gen file: 

- Open the file: `vim /etc/locale.gen`
- Scroll to the line, enter `i`nsert mode and uncomment the line.
- `esc`ape and enter `:`, then type `wq` to write changes and quit. (Note that `:q!` will quit without saving changes.)

Then, run `locale-gen` to generate the locale file.

Next, create the locale configuration file with "LANG=en_US.UTF-8" (or whatever locale you chose) as its only line: `echo "LANG=en_US.UTF-8" > /etc/locale.conf`.

Select a hostname and add it to the hostname file, as `echo "HOSTNAME_HERE" > /etc/hostname`.

Enable DHCP. If `systemctl enable dhcpcd` does not work, try installing it via `pacman -S dhcpcd` and running the previous command once more.

### Root password

Set a new password by running `passwd`. When logging in,  you can log in as `root`. 

### GRUB bootloader

Install GRUB: `pacman -S grub os-prober`.

Then run `grub-install /dev/sda` to complete installation.

Configure GRUB: `grub-mkconfig -o /boot/grub/grub.cfg`. At this point you can see if GRUB was able to locate images.

Exit `arch-chroot` by running `exit`.

### Boot into Arch

Close out of the VM, and send the shutdown signal.

Head into settings and remove the ISO from the optical tray. Start the system once more and you should be greeted with the GNU GRUB bootloader. 

Select Arch Linux and log in.

Ensure that you can `ping` websites. If you can't (but could ping from the ISO): then, insert the ISO, chroot into Arch, and ensure that `dhcpcd` is installed and enabled. 

## Customize Arch

Great! Now, let's customize Arch. That's what you're here for, right?

### A user account

Since `root` can be dangerous, create a [user account](https://wiki.archlinux.org/index.php/Users_and_groups#User_management) for yourself. Replace USERNAME_HERE with your choice. 

```
# useradd -m USERNAME_HERE
# passwd USERNAME_HERE
New password: (...)
```

You can verify the list of users in `vim /etc/passwd`.

Install sudo to configure adminstrator rights. Run `pacman -S sudo`.

(Now, there are a lot of ways to add the newly created user to an admin group that is allowed to execute any command. Arch does not provide a `sudo` group out of the box (can be [verified](https://unix.stackexchange.com/questions/359986/unable-to-add-a-new-user-to-the-sudo-group) by `cat /etc/group`), but does provide a way for users in the sudo group to execute any command. Alternatively, it also allows users in the `wheel` group to execute any command.)

For good measure, enter `EDITOR=nano visudo` in etc/sudoers.tmp and uncomment the lines `%wheel ALL=(ALL) ALL` and `%sudo ALL=(ALL) ALL`. Exit (`^X`) and save (`y` at the prompt).

Next, you can either ~> add the user to the `wheel` group, or ~> create a `sudo` group and add the user to the `sudo` group.

* (Recommended) To add the user to `wheel` run `usermod -a -G wheel USERNAME_HERE`. You can verify the list of users in `vim /etc/passwd`.

Now, users in the `wheel` group can execute `sudo` commands.

Enter `logout` and try to login with the user you created.

### Sound

Install `alsa-utils`: `sudo pacman -S alsa-utils`. Run `alsamixer`, and unmute channels (hit M), using arrow keys to navigate, and raise volume until dB gain reaches zero. `Esc`ape to exit alsamixer, and test speakers by `speaker-test -c 2` (where 2 is the number of speakers I had). 

Running the speaker test with more speakers than you have will output `unused`, so don't fret about it. Press CTRL+C (`^C`) to interrupt the test. Make sure your host system is not muted!

### Graphics

Install the X Window System by running:

``` 
sudo pacman -S xorg-server xorg-xinit xorg-apps
```

Note that `xorg-server-utils` was removed, but `xorg-apps` should provide the packages anyway. To remove packages you install accidentally, you can run `pacman -Rns PACKAGE_NAME_HERE`. 

Next, identify the card by `lspci | grep -e VGA -e 3D`. Using a virtual machine, it displayed VMWare SVGA II Adapter. Search for corresponding drivers: `pacman -Ss vmware`, and install them `sudo pacman -S xf86-video-vmware`, `xf86-input-vmmouse`, and `open-vm-tools`. The more the merrier.

Install the default X environment: `sudo pacman -S xorg-twm xorg-xclock xterm` and start it via `startx`.

If you have issues with input mouse/touch drivers, esp. on a laptop, try `sudo pacman -S xf86-input-synaptics`. Note that on VMs, `xf86-input-vmmouse` from earlier seemed to work fine. Then, `exit` and `startx` again.

When everything looks good, type `exit` in the xterm to leave the X environment. 

Now that we've verified that graphics works, we can proceed to install a desktop environment. GNOME is popular, and is used by distros such as Debian. Other desktop environments include KDE, Xfce, MATE, and Cinnamon.

```
sudo pacman -S gnome
```

Note that installing GNOME requires nearly 2G.

Provide graphical login with 

```
sudo systemctl enable gdm.service
```

Then, reboot: `sudo reboot`. On the GNOME login, you can log in with a user or with `root`.



## Installing Linux as a dual-boot system

Alternatively, you might want to dual-boot Windows and Linux. The recommended method is to first install Windows 10 and use its partitioning (Disk Management) tool before installing Linux. I like vanilla GNOME so I went with Fedora. Some other neat distros include Lubuntu if you like a lightweight distro, K/X/ubuntu for KDE or Xfce, Debian, and so on.

### Partition existing OS

Free up/shrink at least 20G from your primary partition (typically with `C:` drive). In Windows, use the Disk Management tool as administrator. In Linux, you can use the steps outlined above for the Arch installation.

Then, turn off [fast startup](https://www.tenforums.com/tutorials/4189-turn-off-fast-startup-windows-10-a.html).

### BIOS boot sequence

At this point you can insert the USB, run the Fedora Media Creation Tool, and create a live bootable USB. Upon restarting, if you do not boot into the USB, then you can try advanced startup or try through the BIOS. This will depend on manufacturer settings; in general, while powering up, hold F1, F2, F8, or F10. 

* For Dell, hold F2 at the Dell logo screen, enter BIOS Boot Sequence, (Boot mode should be UEFI) and go to General > Boot Sequence > move USB to the top > Apply. Upon Restart, you should boot into the USB.
* For VirtualBox, press F12 at the brief VBox screen. Insert the ISO into the CD-ROM (Devices > Optical > ... > Choose disk) and press `c`. For VMWare Workstation, press F2 at the brief launch screen.

### Within the live USB

You can begin installation of Fedora-Workstation-Live-xx immediately. Click "install to hard drive", configure as necessary.

Click on "Installation Destination" and select the 50G drive (so that it is selected with a black checkmark - this is ambiguous on the installation dialogue), which should display 20G free. Under Storage Configuration, select Automatic. Leave everything else to their default settings. Then press "Done" in the top left and wait a few seconds.

Finally, click the button to begin installation. 

