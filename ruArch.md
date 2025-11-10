# Краткий и понятный гайд на установку ARCH LINUX

### 1. Подключение к сети + настройка pacman
1. `iwctl` - заходим в утилиту подключения к сети
2. `station <wifi adapter> connect <wifi ssid>` - подключаемся к сети
3. `exit` - выходим из утилиты подключения к сети
4. `ping archlinux.org` - проверяем подключение
5. `nano /etc/pacman.d/mirrorlist` -  коментируем всё кроме нужных зеркал
6. `nano /etc/pacman.conf` - ставим *parallel downloads* на 15

### 2. Разметка диска
1. `lsblk` - смотрим название нужного диска
2. `cfdisk /dev/<disk>` - открываем утилиту разметки диска
3. Если есть, стераем разделы
4. Создаём раздел **256M** под `EFI System`
5. Создаём раздел **оставшееся место** под `Linux Filesystem`
6. Нажимаем `Write` и вводим **yes**
7. Нажимаем `Quit`

### 3. Форматирование диска
1. `lsblk` - смотрим названия разделов диска
2. `mkfs.vfat /dev/<раздел диска на 256M>` - создаём загрузочный раздел
3. `mkfs.ext4 /dev/<раздел диска на Linux Filesystem>` - создаём диск под файловую систему

### 4. Монтирование разделов
1. `mount /dev/<раздел диска на Linux Filesystem> /mnt` - монтируем основной раздел
2. `mkdir -p /mnt/boot/efi` - создаём папку под **EFI**
3. `mount /dev/<раздел диска на 256M> /mnt/boot/efi` - монтируем загрузочный раздел
### 5. Устанавливаем базовые пакеты
`pacstrap /mnt base base-devel linux linux-firmware linux-headers nano neovim bash-completion grub efibootmgr networkmanager xorg ttf-ubuntu-font-family ttf-hack ttf-dejavu git curl ttf-opensans mpv imv`

### 6. Устанавливаем окружение рабочего стола
- **Hyprland** - `pacstrap /mnt sddm hyprland hyprlang hyprpaper hyprcursor aquamarine hyprutils hyprgraphics kitty waybar`
- **KDE** - `pacstrap /mnt sddm plasma-meta kde-applications`
- **GNOME** - `pacstrap /mnt gdm gnome`
- **BSPWM** - `pacstrap /mnt BSPWM sxhkd dmenu polybar picom kitty`
### 7. Генерируем fstab
1. `genfstab /mnt >> /mnt/etc/fstab`

### 8. Настраиваем систему
1. `arch-chroot /mnt` - меняем корень системы
2. `systemctl enable NetworkManager` - включаем сервис NetworkManager
3. `systemctl enable sddm или gdm` - включаем сервис dm (в зависимости от установленного)
4. `useradd -m <Имя пользователя>` - создаём пользователя
5. `passwd <Имя пользователя>` - указываем пароль для пользователя
6. `passwd root` - указываем пароль для root (рекомендуется поставить пароль отличный от пользователя)
7. `nano /etc/sudoers` - редактируем файл *добавляем внизу строчку* `<Имя пользователя>  ALL=(ALL:ALL) ALL` 
8. `nano /etc/locale.gen`  - редактируем файл *расскоментируем* `en_US.UTF-8 UTF-8` и `ru_RU.UTF-8 UTF-8`
9. `nano /etc/locale.conf` - создаём файл *пишем* `LANG="en_US.UTF-8"`
10. `locale-gen` - генерируем язык системы
11. `grub-install /dev/<название диска>` - устанавливаем загрузщик
12. `nano /etc/default/grub` - Если нужно редактируем файл загрузщика *можно убрать quiet*
13. `grub-mkconfig -o /boot/grub/grub.cfg` - создаём конфигурацию загрузщика
14. `exit` - выходим из системы
15. `umount -R /mnt` - размонтируем диск
16. `reboot` - перезагрузка **ОБЯЗАТЕЛЬНО ВЫТАЩИТЬ ФЛЕШКУ**
