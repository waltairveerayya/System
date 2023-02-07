# System-Build-Guide

**0. System Specifications**
* Motherboard: ASUS B550 Prime B550M-A WiFi II AM4 Micro ATX
* PSU: MSI MAG A550BN
* Case: MSI MAG Forge 100M Mid Tower
* CPU: AMD Ryzen™ 5 5600G Desktop Processor 
* Thermal Compound: Noctua NT-H1 3.5g
* RAM: Kingston FURY Beast 16GB (2x8GB) DDR4
* Storage: 1TB SK hynix Gold P31 PCIe NVMe Gen3 M.2 
* Integrated GPU: Radeon Vega 
* Display: LG - 27Qn880-B (2560x1440)
* Keyboard: Razer BlackWidow Tournament Edition Chroma V2 Mechanical 
* Mouse: Razer DeathAdder Essential
* Headset: JBL Quantum One
* UPS: APC Back-UPS BX1100C-IN 1100VA / 660W, 230V
* OS: Arch Linux


**1. Download Latest Arch x86_64.iso**
https://archlinux.org/download/


**2. Create a bootable usb**
* In windows make use of https://rufus.ie/en/ 
* In linux use a dd command
```
dd if=archlinux-version-x86_64.iso of=/dev/sdb bs=4M
```

**3. Connect to Internet**
```
iwctl
```
```
device list
```
```
station wlan0 connect Zenos_5g
```

**4. Ping some site on the Internet to verify connection**
```
ping -c 3 archlinux.org
```

**5. Update system clock**
```
timedatectl set-ntp true
```
<details><summary>Check status</summary>
```
timedatectl status
```
</details>

**6. Update your mirrorlist**
```
reflector --verbose --latest 200 --sort rate --save /etc/pacman.d/mirrorlist
```

**7. Install archlinux-keyring**
```
pacman -Syy archlinux-keyring
```

**8. Create the filesystems**
* boot EFI Drive Setup
```
gdisk /dev/nvme0n1
```
- Type 'o' to create a partition table
* boot Drive setup
   - Type 'n' for a new partition
   - Enter
   - Enter
   - +1G
   - EF00
* swap Drive setup
   - Type 'n' to create a new partition
   - Enter
   - Enter
   - +40G
   - 8200
* root Drive setup
   - Type 'n' to create a new partition
   - Enter
   - Enter
   - Enter
   - 8300

* Check if everything looks right by pressing ‘p’. It should look like this:
```
   Number    Start (sector)  End (sector)   Size          Code    Name
   1         2048            2099199        1024.0 MiB    EF00    EFI system partition
   2         2099200         85985279       40.0 GiB      8200    Linux swap
   3         85985280        2000408575     912.9 GiB     8300    Linux filesystem
```

* Looks good?
Press 'w' to write changes to disk.

* Create filesystems
```
mkfs.vfat /dev/nvme0n1p1
mkswap /dev/nvme0n1p2
mkfs.ext4 /dev/nvme0n1p3
```

* Mount partitions
```
mount /dev/nvme0n1p3 /mnt
```
```
mkdir /mnt/boot
```
```
mount /dev/nvme0n1p1 /mnt/boot
```
```
swapon /dev/nvme0n1p2
```

**9) Install Arch linux base and vim packages**
```
pacstrap -i /mnt base vim linux linux-firmware linux-headers base-devel \
efibootmgr mtools dosfstools openssh iwd ntfs-3g amd-ucode xf86-video-amdgpu \
git ark unrar sshfs networkmanager gnome gdm firefox virtualgl vlc gimp kdenlive \
qbittorrent obs-studio yt-dlp gparted pacman-contrib
```

**10) Copy systemd network configuration files**
```
cp /etc/systemd/network/* /mnt/etc/systemd/network
```

**11) Change root to new system**
```
arch-chroot /mnt
```

**12) Set the timezone**
```
ln -sf /usr/share/zoneinfo/Asia/Kolkata /etc/localtime
```

**13) Set locale**
```
sed -i 's/#en_US.UTF-8/en_US.UTF-8/g' /etc/locale.gen (uncomment en_US.UTF-8)
```
```
locale-gen
```

**14) Create locale.conf**
```
vim /etc/locale.conf
```
Add the below line with your locale info
```
LANG=en_US.UTF-8
```

**15) Set your hostname**
```
vim /etc/hostname
```
Add something like below line
```
waltair
```

**16) Set your hosts**
```
vim /etc/hosts
```
Add the below lines by making required changes
```
127.0.0.1   localhost
::1         localhost
127.0.1.1   waltair.localdomain   waltair
```

**17) Configure mkinitcpio**
```
vim /etc/mkinitcpio.conf
```
Update "HOOKS=(base udev autodetect modconf block filesystems keyboard fsck)" to
```
HOOKS=(base udev autodetect keyboard keymap consolefont modconf block filesystems resume fsck)
```

**18) Generate initramfs & install bootctl**
```
mkinitcpio -p linux
```
```
bootctl install
```

**19) Create linux boot entry**
```
vim /boot/loader/entries/arch.conf
```
Add the below lines
```
title arch
linux /vmlinuz-linux
initrd /amd-ucode.img
initrd /initramfs-linux.img
options resume=/dev/nvme0n1p2 root=/dev/nvme0n1p3 rw pci=noaer quiet splash
```


**20) configure bootloader, Set root password and create a user****
```
vim /boot/loader/loader.conf
```
Add below mentioned lines, save & close file
```
timeout 10
default arch 
```

```
passwd
```
```
useradd -m -g wheel veerayya
```
```
passwd veerayya
```
```
EDITOR=vim visudo
```
uncomment below line
```
%wheel ALL=(ALL) ALL
```

**21) Enable required services**
```
systemctl enable systemd-networkd systemd-resolved systemd-timesyncd \
NetworkManager bluetooth gdm 
```
**22) Finish installation**
```
exit
umount -a
reboot
```

**23) set-ntp & update the Hardware clock**
```
sudo timedatectl set-ntp true
sudo hwclock --systohc
```

**24) Setup yay (AUR Helper)**
```
cd /opt
```
```
sudo git clone https://aur.archlinux.org/yay-git.git
```
```
sudo chown -R $USER ./yay-git
```
```
cd yay-git
```
```
makepkg -si
```

**25) Install required fonts**
```
yay -S ttf-comic-mono-git freetype2 ttf-ms-fonts
```
```
sudo pacman -S freetype2 fontconfig cairo ttf-ubuntu-font-family \
noto-fonts noto-fonts-cjk ttf-dejavu \
ttf-liberation ttf-opensans ttf-cascadia-code
```
```
sudo ln -s /etc/fonts/conf.avail/70-no-bitmaps.conf /etc/fonts/conf.d  
```
```
sudo vim /etc/profile.d/freetype2.sh
```
Add/uncomment the following line to/in it, save and close 
```
export FREETYPE_PROPERTIES="truetype:interpreter-version=40"
```
```
mkdir -p ~/.config/fontconfig/conf.d/
```
```
vim ~/.config/fontconfig/conf.d/20-no-embedded.conf
```
Add the following lines to it, save and close 
```
<?xml version="1.0"?>
<!DOCTYPE fontconfig SYSTEM "fonts.dtd">
<fontconfig>
<match target="font">
<edit name="embeddedbitmap" mode="assign">
<bool>false</bool>
</edit>
</match>
</fontconfig>
```
```
reboot

```

**26) Installing required packages**
```
yay -S visual-studio-code-bin android-studio intellij-idea-community-edition pycharm-community-edition \
onlyoffice-bin libvirt virtualbox virtualbox-ext-vnc virtualbox-guest-iso virtualbox-guest-utils \
virtualbox-host-dkms virtualbox-ext-oracle teamviewer google-chrome brave-bin etcher-bin
```
