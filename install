#!/bin/bash

# 設置鍵盤布局
loadkeys <keymap>

# 檢查網絡連接
ping -c 3 www.archlinux.org
if [ $? -ne 0 ]; then
  echo "網絡連接失敗，請檢查網絡設置。"
  exit 1
fi

# 更新系統時間
timedatectl set-ntp true

# 創建分區（使用 GPT 分區表和UEFI分區）
parted /dev/sda mklabel gpt
parted /dev/sda mkpart primary fat32 1MiB 200MiB
parted /dev/sda set 1 boot on
parted /dev/sda mkpart primary ext4 200MiB 100%

# 格式化分區
mkfs.fat -F32 /dev/sda1
mkfs.ext4 /dev/sda2

# 掛載分區
mount /dev/sda2 /mnt
mkdir /mnt/boot
mount /dev/sda1 /mnt/boot

# 選擇預設的軟體源
cp /etc/pacman.d/mirrorlist /etc/pacman.d/mirrorlist.backup
rankmirrors -n 6 /etc/pacman.d/mirrorlist.backup > /etc/pacman.d/mirrorlist

# 安裝基礎系統
pacstrap /mnt base

# 生成文件系統表
genfstab -U /mnt >> /mnt/etc/fstab

# 切換到新安裝的系統
arch-chroot /mnt

# 配置時區
ln -sf /usr/share/zoneinfo/Asia/Hong_Kong /etc/localtime
hwclock --systohc

# 配置語言環境（假設使用英文）
echo "en_US.UTF-8 UTF-8" > /etc/locale.gen
locale-gen
echo "LANG=en_US.UTF-8" > /etc/locale.conf

# 配置主機名
echo "archlinux" > /etc/hostname

# 配置網絡
# 請根據自己的需求修改網絡配置
echo "127.0.0.1   localhost
::1         localhost
127.0.1.1   archlinux.localdomain  archlinux" > /etc/hosts

# 設置 root 密碼
passwd

# 安裝引導程序（假設使用 GRUB）
pacman -S grub efibootmgr
grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=ArchLinux
grub-mkconfig -o /boot/grub/grub.cfg

# 完成安裝
exit
