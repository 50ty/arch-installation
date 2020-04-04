# Identifikation der Richtigen Platte 
```
lsblk 
blkid
```

Ich installiere hier auf /dev/sda und nehme an, dass nur zwei Partitionen (/dev/sda1 und /dev/sda2) vorhanden sind, wobei /dev/sda1 die boot Partition mit 512MB ist.

# Formatieren der Platte
```
sgdisk -n 1:0:+512MB -n 2:0:0 -t 1:ef00 /dev/sda
```
Alternative:
```
gdisk /dev/sda
 n
 enter
 enter
 +512M
 EF00
 n
 enter
 enter
 enter
 enter
 p
 w
 Y
```

# Efi Boot
```
 mkfs.fat -F 32 -n EFIBOOT /dev/sda1
```

# root 
```
 mkfs.ext4 -L p_arch /dev/sda2
```

# Partitionen einhängen
```
 mount /dev/sda2 /mnt
 mkdir /mnt/boot
 mount /dev/sda1 /mnt/boot
```

# Pakete installieren
```
 pacstrap /mnt base base-devel
```

# Fstab erzeugen 
```
 genfstab -p /mnt > /mnt/etc/fstab
 # Kontrolle
 nano /mnt/etc/fstab
```

# Starten & Konfig (> und >> beachten!)
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

# Bootmanager
```
 bootctl install 
```

# nano /boot/loader/entries/arch-uefi.conf
```
title    Arch Linux
linux    /vmlinuz-linux
initrd   /initramfs-linux.img
options  root=LABEL=p_arch rw
```

# nano /boot/loader/entries/arch-uefi-fallback.conf
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

# Nutzer anlegen
```
 useradd -m -G wheel -s /bin/bash <desired username>
 passwd <desired username>
 nano /etc/sudoers
```

# login to stefan
```
 logout
```

# start dhcp???
```
 sudo systemctl start dhcpcd
```

# Graka installieren

# kde 
 http://www.techrapid.co.uk/2017/04/install-kde-plasma-5-on-arch-linux.html
