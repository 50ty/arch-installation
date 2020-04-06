# Identifikation of the block devices:
```
lsblk 
blkid
```

I'm installing arch to /dev/sda and create two partitions /dev/sda1 und /dev/sda2. '/dev/sda1' should be the boot partition with at least 512MB.
I don't care of existing data, ensure that the blcok device is empty.

```
sgdisk -n 1:0:+512MB -n 2:0:0 -t 1:ef00 /dev/sda
```

# Create efi boot file system
```
 mkfs.fat -F 32 -n EFIBOOT /dev/sda1
```

# Create filesystem for root 
```
 mkfs.ext4 -L p_arch /dev/sda2
```

# Mount partitions
```
 mount /dev/sda2 /mnt
 mkdir /mnt/boot
 mount /dev/sda1 /mnt/boot
```

# Install packages
```
 pacstrap /mnt base base-devel
```

# Generate fstab 
```
 genfstab -p /mnt > /mnt/etc/fstab
 # Kontrolle
 nano /mnt/etc/fstab
```

# Install additional stuff and do a base configuration
```
 arch-chroot /mnt
 echo myhost > /etc/hostname # Set Hostname
 echo KEYMAP=de-latin1 > /etc/vconsole.conf # Tastatur
 echo FONT=lat9w-16 >> /etc/vconsole.conf # Font
 ln -s /usr/share/zoneinfo/Europe/Berlin /etc/localtime # Zeitzone
 nano /etc/locale.gen
	#uncomment:
	#de_DE.UTF-8 UTF-8
	#de_DE ISO-8859-1
	#de_DE@euro ISO-8859-15
 locale-gen
 passwd # passwort setzten
 pacman -S efibootmgr dosfstools gptfdisk # Pakete für uefi
```

# Install efi bootmgr
```
 bootctl install 
```

# Create bootloader files

If using a Intel CPU, you have to add a line for ucode updates (see https://wiki.archlinux.org/index.php/Microcode#systemd-boot)

## nano /boot/loader/entries/arch-uefi.conf
```
title    Arch Linux
linux    /vmlinuz-linux
initrd   /initramfs-linux.img
options  root=LABEL=p_arch rw
```

## nano /boot/loader/entries/arch-uefi-fallback.conf
```
title    Arch Linux Fallback
linux    /vmlinuz-linux
initrd   /initramfs-linux-fallback.img
options  root=LABEL=p_arch rw
```

# reboot 
```
 exit
 umount -R /mnt
 reboot
```

# Create users
```
 useradd -m -G wheel -s /bin/bash <desired username>
 passwd <desired username>
 nano /etc/sudoers
```
# install graphics drivers 

See the archlinux wiki for your graphic card: https://www.archlinux.org/

# kde 
 http://www.techrapid.co.uk/2017/04/install-kde-plasma-5-on-arch-linux.html
