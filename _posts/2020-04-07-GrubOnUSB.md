---
layout: post
title: "Configuration d'une clé USB MultiBoot avec Windows 10 Installer, Debian live et GParted live"
categories: sys ops
output:
  html_document:
    highlight: pygments
published: true
comments: false
tags: [grub boot]
excerpt: cookbook pour une clé USB multiboot
---
<div class="social-media-list">
<a href="https://twitter.com/share?ref_src=twsrc%5Etfw" class="twitter-share-button" data-show-count="false">Tweet</a>
<script type="IN/Share" data-url="{{ site.url }}{{ page.url }}"></script>
<div class="fb-share-button" data-href="{{ site.url }}{{ page.url }}" data-layout="button" data-size="small"><a target="_blank" href="https://www.facebook.com/sharer/sharer.php?u={{ site.url }}{{ page.url }}" class="fb-xfbml-parse-ignore">Partager</a></div>
</div>


# Récupération des ISO pour les systèmes

Pour un ISO de Windows 10 il faut executer l outil sur un WIndows et demander à creer un fichier ISO (taille 4.5Go environ)

https://www.microsoft.com/fr-fr/software-download/windows10


Pour DEBIAN LIVE , aller sur https://www.debian.org/CD/live/

POur GParted (outil de partition disques) aller sur https://gparted.org/download.php


# Préparation de la clé USB

Le plus fiable, sur Linux, utiliser GParted.

Retirer l ensemble des partitions
Créer une partition pour GRUB (200-300Mo) en ext4 , flag à boot

Créer une partition pour WIndows 10 Installer en NTFS (4.5Go)  avec le label WIN10INSTALL

Créer une partition avec le label DEBIAN pour le cd live de Debian (FAT32) car plus rapide qu'un boot sur fichier iso

Créer une partition avec le label INSTALL pour stocker les fichiers ISO (FAT32) sur laquelle on peut booter grace à loopback

suivre la méthode pour Windows 10 Installer :
https://willhaley.com/blog/windows-installer-usb-linux/

https://doc.ubuntu-fr.org/tutoriel/grub2_lancer_des_images_iso


Pour lister les partitions
```
sudo blkid
```

Montage de l iso et copie sur la partition NTFS
```
mount -t udf /media/user/disk/win10.iso /media/user/win10iso

sudo mount /dev/sdf2 /media/user/win10installer

sudo rsync -vr /media/user/win10iso/ /media/user/win10installer

sudo sync
```



# Installation de GRUB2
```
sudo mount -o remount,rw /dev/sdf1 /media/user/grub/

sudo grub-install \
    --no-floppy \
    --target=i386-pc \
    --recheck \
    --locales="en@quot" \
    --root-directory=/media/user/grub \
    --boot-directory=/media/user/grub/grub-boot \
    --install-modules="ls loopback linux ext2 ntldr normal search ntfs echo boot reboot" \
    /dev/sdf
```
Si certains modules manquenet, installer grub2 
et  aller copier depuis /usr/lib/grub/ sur la partition de boot de la clé usb
```
sudo apt-get update
sudo apt-get install grub2
sudo cp /usr/lib/grub/i386-pc/* /media/user/grub/grub-boot/grub/i386-pc
```

Pour configurer le menu grub:
créer un nouveau fichier /media/user/grub/grub-boot/grub/grub.cfg

avec la recherche des partitions systemes par le label

```
set timeout=10 set default=0

insmod play
play 960 440 1 0 4 440 1

menuentry "Windows 10 Installer" {
    insmod ntfs
    search --set=root --file /bootmgr
    ntldr /bootmgr
    boot
}
# ou plus efficace
menuentry "Windows 10 Installer" {
    insmod ntfs
    search --set=root --label WIN10INSTALL
    ntldr /bootmgr
    boot
}

menuentry "GParted(partitions)" {

    set isofile="/iso/gparted-live-1.0.0-3-i686.iso"
	search --set root --label INSTALL
	loopback loop $isofile
	echo	'Lancement de l iso en cours, veuillez patienter..'
    linux (loop)/live/vmlinuz findiso=${isofile} boot=live union=overlay username=user config components noswap toram=filesystem.squashfs ip= net.ifnames=0 nosplash locales=fr_FR.UTF-8 keyboard-layouts=fr
    initrd (loop)/live/initrd.img
}

menuentry "DEBIAN Live ISO" { 
	set isofile="/iso/debian-live-10.3.0-i386-kde.iso"
	search --set root --label INSTALL
	loopback loop $isofile
	echo 'Lancement de l iso en cours, veuillez patienter..'
    linux (loop)/live/vmlinuz-4.19.0-8-686 boot=live components locales=fr_FR.UTF-8   keyboard-layouts=fr quiet splash "${loopback}"
    initrd (loop)/live/initrd.img-4.19.0-8-686
}

menuentry "DEBIAN Live" { 
	search --set root --label DEBIAN
	echo 'Lancement du systeme en cours, veuillez patienter..'
    linux  /live/vmlinuz-4.19.0-8-686 boot=live components locales=fr_FR.UTF-8  keyboard-layouts=fr quiet splash
    initrd /live/initrd.img-4.19.0-8-686
}
```