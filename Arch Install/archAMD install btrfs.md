- В bios первести secure boot в режим setup
- Соеденинение с интернетом, Синхронизация времени 
```bash
 ping google.com
```
```bash
timedatectl
```
```bash
timedatectl set-timezone Asia/Yekaterinburg
```

- Разметка диска, Форматирование разделов, Монтирование разделов
```bash
fdisk -l
```
```bash
lsblk
```
```bash
fdisk /dev/sda
```
efi -- type 1 (efi)
swap -- type 19 (swap)
root -- type 20 (linux filesystem) 

---
Partitioning, BTRFS layout

| Mount point | Partition | Partition type       | FS Type | Bootable flag | Size      |
| ----------- | --------- | -------------------- | ------- | ------------- | --------- |
| /boot/efi   | /dev/sda1 | EFI System Partition | FAT32   | Yes           | 1 GiB     |
| [SWAP]      | /dev/sda2 | Linux swap           | SWAP    | No            | 20 GiB    |
| /           | /dev/sda3 | Linux                | BTRFS   | No            | 211,9 GiB |
```text

sda3 (Volume)
|
|
- _active (Subvolume)
|    |
|    - rootvol (Subvolume - It will be the current /)
|    - homevol (Subvolume - it will be the current /home)
|
- _snapshots (Subvolume -  It will contain all the snapshots which are subvolumes too)
```
---
```bash
mkfs.fat -F 32 /dev/sda1
```
```bash
mkswap /dev/sda2
```
```bash
swapon /dev/sd
```
```bash
mkfs.btrfs -L arch /dev/sda3
```
```bash
mount /dev/sda3 /mnt
```
```bash
cd /mnt
```
```bash
btrfs subvolume create _active
```
```bash
btrfs subvolume create _active/rootvol
```
```bash
btrfs subvolume create _active/homevol
```
```bash
btrfs subvolume create _snapshots
```
```bash
cd ..
```
```bash
umount /mnt
```
```bash
mount -o subvol=_active/rootvol /dev/sda3 /mnt
```
```bash
mkdir /mnt/{home,boot}
```
```bash
mkdir /mnt/boot/efi
```
```bash
mkdir /mnt/mnt
```
```bash
mkdir /mnt/mnt/defvol
```
```bash
mount /dev/sda1 /mnt/boot/efi
```
```bash
mount -o subvol=_active/homevol /dev/sda3 /mnt/home
```
```bash
mount -o subvol=/ /dev/sda3 /mnt/mnt/defvol
```
--- 
```
reflector --latest 20 --sort rate --protocol https --save /etc/pacman.d/mirrorlist
```

```
pacstrap -K /mnt base linux-zen linux-firmware amd-ucode nano git reflector linux-zen-headers base-devel sudo networkmanager grub efibootmgr sbctl xf86-video-amdgpu gnome gnome-extra gdm openssh noto-fonts-cjk usbutils btrfs-progs gnome-firmware print-manager cups system-config-printer 
```

```
genfstab -U /mnt >> /mnt/etc/fstab
```

```
arch-chroot /mnt
```
---
- edit fstab EXAMPLE
```
# /dev/sda3 LABEL=arch
UUID=3b5a7b5b-8b29-4b02-a41c-ec00e210d4a8       /               btrfs           rw,relatime,compress=lzo,ssd,discard,autodefrag,space_cache,subvolid=257,subvol=/_active/rootvol        0 0

# /dev/sda1
UUID=ABE8-6059          /boot/efi       vfat            rw,relatime,fmask=0022,dmask=0022,codepage=437,iocharset=iso8859-1,shortname=mixed,utf8,errors=remount-ro       0 2

# /dev/sda3 LABEL=arch
UUID=3b5a7b5b-8b29-4b02-a41c-ec00e210d4a8       /home           btrfs           rw,relatime,compress=lzo,ssd,discard,autodefrag,space_cache,subvolid=258,subvol=/_active/homevol        0 0

# /dev/sda2
UUID=6de2287c-9dbb-46b5-91e0-05d5c34c625c       none            swap            defaults        0 0

UUID=3b5a7b5b-8b29-4b02-a41c-ec00e210d4a8       /mnt/defvol     btrfs           rw,relatime,compress=lzo,ssd,discard,autodefrag,space_cache,subvolid=5,subvol=/ 0 0
```

- add HOOKS=(...  btrfs) 
```
nano /etc/mkinitcpio.conf
```

```
mkinitcpio -p linux-zen
```

```
sudo grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=GRUB --modules="tpm" --disable-shim-lock
```

- delete timeout, timeout = 0
```
nano /etc/default/grub
```

```
sudo grub-mkconfig -o /boot/grub/grub.cfg
```
---
- secure boot
```
sbctl status
```

```
sbctl create-keys
```

```
sbctl enroll-keys -m
```

```
sbctl status
```

```
sbctl sign -s /boot/efi/EFI/GRUB/grubx64.efi
```
```
sbctl sign -s /boot/efi/EFI/GRUB/grubx64.efi
```

```
sudo sbctl verify
```
```
sbctl status
```
---
- Локализация
```bash
nano /etc/locale.gen
```
	--- uncomment:
	en_US.UTF-8 UTF-8
	ru_RU.UTF-8 UTF-8
```
locale-gen
```
create  [locale.conf](https://man.archlinux.org/man/locale.conf.5)
```
nano /etc/locale.conf
```
```
LANG=en_US.UTF-8
```
- [Настройка сети](https://wiki.archlinux.org/title/Network_configuration_(Русский)#Управление_сетевым_подключением)
create [hostname](https://wiki.archlinux.org/title/Network_configuration_(%D0%A0%D1%83%D1%81%D1%81%D0%BA%D0%B8%D0%B9)#%D0%98%D0%BC%D1%8F_%D1%85%D0%BE%D1%81%D1%82%D0%B0 "Network configuration (Русский)")
```
nano /etc/hostname
```
```
archAMD
```

```
nano /etc/hosts
```
create  [hosts(5)](https://man.archlinux.org/man/hosts.5)
```
# Static table lookup for hostnames.
# See hosts(5) for details.
127.0.0.1       localhost
::1             localhost
127.0.1.1       archAMD.localdomain     archAMD
```

- enable networkmanager (pacstrap)
```bash
systemctl enable NetworkManager
```

- printer
```bash
systemctl enable cups && systemctl start cups
```
--- 
- Задание пароля root
```
passwd
```
- Add user
```
useradd -m 71mal1
```
```
passwd 71mal1
```
```
usermod -aG wheel,audio,video,render,storage,scanner 71mal1
```
- add user sudoers (Файл настроек /etc/sudoers всегда следует редактировать с помощью команды visudo(8).)
```
EDITOR=nano visudo
```
```
71mal1 ALL=(ALL:ALL) ALL
```
---
- Выйти из chroot
```
exit
```

- убедиться что не осталось активных процессов в системе 
```
 umount -R /mnt 
```

- reboot
```
reboot 
```
---
- enable ssh
```
systemctl enable sshd && systemctl start sshd
```

- enable multilib
```
sudo nano /etc/pacman.conf
```
uncomment
```
[multilib]
```
	Include = /etc/pacman.d/mirrorlist

- install yay 
```
mkdir temp && cd temp && git clone https://aur.archlinux.org/yay.git && cd yay && makepkg -si
```

- unlimit history bash
```
echo "export HISTSIZE=1000000" >> ~/.bashrc && echo "export HISTFILESIZE=1000000" >> ~/.bashrc
```

- Графика amdgpu pro
```
mkdir temp1 && cd temp1 && git clone https://aur.archlinux.org/amdgpu-pro-installer.git && cd amdgpu-pro-installer && makepkg -si
```

- Экран приветствия/входа в систему
```
systemctl enable gdm && systemctl start gdm
```