# Identifikation der Richtigen Platte 
```
lsblk 
blkid
```

Ich installiere hier auf /dev/sda 
Insgesamt werden zwei Partitionen angelegt, efi auf Partition 1 und arch auf Partition 2. 
ACHTUNG: DIE GANZE PLATTE WIRD DABEI PLATT GEMACHT. 
in der Anleitung muss dementsprechend X durch 1 und Y durch 2 ersetzt werden. 

# Formatieren der Platte
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
 mkfs.fat -F 32 -n EFIBOOT /dev/sdaX
```

# root 
```
 mkfs.ext4 -L p_arch /dev/sdaY
```

# Partitionen einhängen
```
 mount /dev/sdaY /mnt
 mkdir /mnt/boot
 mount /dev/sdaX /mnt/boot
```

# Pakete installieren
```
 pacstrap /mnt base base-devel wpa_supplicant
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
 mkinitcpio -p linux # linuxkernel erzeugen
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
 useradd -m -G wheel -s /bin/bash stefan
 passwd stefan
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