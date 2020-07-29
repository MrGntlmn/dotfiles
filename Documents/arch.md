loadkeys us

dhcpcd

ping -c 3 www.archlinux.org

ip link

timedatectl set-ntp true
timedatectl set-timezone Europe/Bucharest
hwclock --systohc --utc
timedatectl status

pacman -Syyy

pacman -S reflector

reflector --verbose --latest 100 --sort rate --save /etc/pacman.d/mirrorlist

fdisk -l

cfdisk /dev/sda [boot/root/home/swap]

mkfs.ext4 /dev/sda1 [boot]
mkfs.ext4 /dev/sda2 [root]
mkfs.ext4 /dev/sda3 [home]
mkswap /dev/sda4 [swap]

mount /dev/sda2 /mnt
mkdir /mnt/boot
mount /dev/sda1 /mnt/boot
mkdir /mnt/home
mount /dev/sda3 /mnt/home
swapon /dev/sda4

pacstrap -i /mnt base base-devel linux linux-firmware linux-headers amd-ucode git curl wget rsync reflector

pacman-key --init
pacman-key --populate archlinux
pacman-key --refresh-keys

genfstab -U -p /mnt >> /mnt/etc/fstab

arch-chroot /mnt

pacman -S vim nano

vim /etc/locale.gen [remove '#' in front of 'en_US.UTF-8']

locale-gen

echo LANG=en_US.UTF-8 > /etc/locale.conf
export LANG=en_US.UTF-8

ln -sf /usr/share/zoneinfo/Europe/Bucharest /etc/localtime

hwclock --systohc --utc

echo archlinux > /etc/hostname

vim /etc/hosts {
127.0.0.1	localhost
::1		    localhost
127.0.1.1	archlinux
}

passwd [root password]

pacman -S grub sudo dhcpcd netctl wpa_supplicant iw dialog broadcom-wl wireless_tools networkmanager network-manager-applet bash-completion efibootmgr dosfstools os-prober mtools

grub-install /dev/sda
or
grub-install --target=x86_64-efi  --bootloader-id=grub_uefi --recheck
or
grub-install --target=x86_64-efi --bootloader-id=GRUB --efi-directory=/boot/efi


grub-mkconfig -o /boot/grub/grub.cfg

systemctl enable dhcpcd

exit [chroot]

umount -R /mnt/boot
umount -R /mnt/home
umount -R /mnt

reboot

useradd -m -g users -G wheel -s /bin/bash gntlmn
passwd gntlmn

vim /etc/sudoers {
[remove '#' in front of 'wheel']
}

exit [root] and login as user [gntlmn]

sudo pacman -Syyyu reflector

sudo reflector --verbose --latest 100 --sort rate --save /etc/pacman.d/mirrorlist

sudo pacman -Syu pulseaudio pulseaudio-alsa alsa-utils [if no sound, execute 'alsactl init']

sudo pacman -Syu xorg-server xorg xf86-video-ati xf86-video-amdgpu

sudo pacman -Syu i3-gaps dmenu i3stats xfce4-terminal firefox polkit-gnome

sudo pacman -Syu lightdm lightdm-gtk-greeter lightdm-gtk-greeter-settings

sudo systemctl enable lightdm
