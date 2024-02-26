- В bios первести secure boot в режим setup
- Соеденинение с интернетом, Синхронизация времени 
Проверить что соеденение с интернетом есть
```
 ping google.com
```

Проверить что время задано верно
```
timedatectl
```

Задать часовой пояс
```
timedatectl set-timezone Asia/Yekaterinburg
```

- Разметка диска, Форматирование разделов, Монтирование разделов
1. Проверка доступных дисков, просмотр разметки
```
fdisk -l
```

Просмотреть разделы
```
lsblk
```

Разметить диск  (d - delete, n - new, w - write)
```
fdisk /dev/sd
```
efi -- size 1G, type 1 (efi)
swap -- size 20G, type 19 (swap)
root -- size full free space, type 20 (linux filesystem) 

2. Форматировать разделы:
efi (FAT32)
```
mkfs.fat -F 32 /dev/sd
```

root (btrfs)
```
mkfs.btrfs /dev/sd
```

swap
```
mkswap /dev/sd
```

3. Монтировать разделы:
root
```
mount /dev/sd /mnt
```

efi 
```
mount --mkdir /dev/sd /mnt/boot
```

swap
```
swapon /dev/sd
```

- Обновление зеркал
```
reflector --latest 20 --sort rate --protocol https --save /etc/pacman.d/mirrorlist
```

- Установка основных пакетов
```
pacstrap -K /mnt base linux-zen linux-firmware amd-ucode nano git reflector linux-zen-headers base-devel sudo networkmanager grub efibootmgr sbctl pavucontrol pulseaudio print-manager cups system-config-printer ntfs-3g xorg xf86-video-amdgpu xfce4 xfce4-goodies lightdm lightdm-gtk-greeter openssh noto-fonts-cjk usbutils
```

- fstab 
```
genfstab -U /mnt >> /mnt/etc/fstab
```

- chroot
```
arch-chroot /mnt
```

- Время (ln -sf /usr/share/zoneinfo/_Регион_/_Город_ /etc/localtime)
```
ln -sf /usr/share/zoneinfo/Asia/Yekaterinburg /etc/localtime
```
```
hwclock --systohc
```

- Обновление зеркал
```
reflector --latest 20 --sort rate --protocol https --save /etc/pacman.d/mirrorlist
```

- Локализация
```
nano /etc/locale.gen
```
Расскоментировать строчки:
```
en_US.UTF-8 UTF-8
```
```
ru_RU.UTF-8 UTF-8
```

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

networkmanager (pacstrap)
```
systemctl enable NetworkManager
```

- Задание пароля root
```
passwd
```

- Установка загрузчика, включение Secure Boot
```
sudo grub-install --target=x86_64-efi --efi-directory=/boot/ --bootloader-id=GRUB --modules="tpm" --disable-shim-lock
```

```
sudo grub-mkconfig -o /boot/grub/grub.cfg
```

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


!!! проблема нет раздела /efi/ sbctl в ошибку, в ручную подписать все файлы
example, so sign all files
```
sudo sbctl sign -s /efi/EFI/GRUB/grubx64.efi
```
```
sudo sbctl verify
```
```
sbctl status
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

add user sudoers (Файл настроек /etc/sudoers всегда следует редактировать с помощью команды visudo(8).)
```
EDITOR=nano visudo
```
```
71mal1 ALL=(ALL:ALL) ALL
```


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

- enable ssh
```
systemctl enable sshd && systemctl start sshh
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

- Sound (pacstrap pavucontrol pulseaudio) (почему нет его)
```
systemctl --user enable pulseaudio.service && systemctl --user start pulseaudio.service
```

- Графика amdgpu pro
```
mkdir temp1 && cd temp1 && git clone https://aur.archlinux.org/amdgpu-pro-installer.git && cd amdgpu-pro-installer && makepkg -si
```


- Экран приветствия/входа в систему
```
systemctl enable lightdm && systemctl start lightdm
```


!! добавить

- Add printer(pacstrap print-manager cups system-config-printer)
```
systemctl enable cups && systemctl start cups
```
```
yay -S hplip
```
```
sudo hp-setup -i
```

нужен qt5 запускать лучше в графическом режиме, устанавливается без танцев с бубном принтер раз через раз
