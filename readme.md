# Formatieren der Platte
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

# Efi Boot
 mkfs.fat -F 32 -n EFIBOOT /dev/sdaX

# root 
 mkfs.ext4 -L p_arch /dev/sdaY

# Partitionen einhängen
 mount -L /dev/sdaY /mnt
 mkdir /mnt/boot
 mount -L /dev/sdaX /mnt/boot

# Pakete installieren
 pacstrap /mnt base base-devel wpa_supplicant

# Fstab erzeugen 
 genfstab -p /mnt > /mnt/etc/fstab
 # Kontrolle
 nano /mnt/etc/fstab

# Starten & Konfig (> und >> beachten!)
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

# Bootmanager
 bootctl install 

# nano /boot/loader/entries/arch-uefi.conf
title    Arch Linux
linux    /vmlinuz-linux
initrd   /initramfs-linux.img
options  root=LABEL=p_arch rw resume=p_swap

# nano /boot/loader/entries/arch-uefi-fallback.conf
title    Arch Linux Fallback
linux    /vmlinuz-linux
initrd   /initramfs-linux-fallback.img
options  root=LABEL=p_arch rw resume=p_swap

# reboot 
 exit
 umount -R /mnt
 reboot

# Nutzer anlegen
 useradd -m -G wheel -s /bin/bashs stefan
 passwd stefan
 nano /etc/sudoers

# login to stefan
 logout

# start dhcp???
 sudo systemctl start dhcpcd



# kde 
 https://www.techrapid.co.uk/linux/arch-linux/install-kde-plasma-on-arch-linux