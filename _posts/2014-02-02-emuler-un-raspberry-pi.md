---
layout: post
title: Emuler un Raspberry Pi avec Qemu
tags: qemu arm raspberry
comments: true
tracking: true
share: true
---

Emuler un processeur ARM sur une archi x86 sous Linux, c'est possible ! Voici comment faire.

## Les étapes

Tout d'abord, il faut installer qemu en version 1.5 minimum (sinon risque de bugs, voir à la fin de ce post).


Ensuite, on récupère une image de raspbian, et un kernel compilé pour les architectures ARM:

	wget http://downloads.raspberrypi.org/raspbian/images/raspbian-2013-12-24/2013-12-20-wheezy-raspbian.zip
	wget http://xecdesign.com/downloads/linux-qemu/kernel-qemu

Ensuite, on prépare l'environnement

	mkdir -p vm/raspbian
	cd vm/raspbian
	unzip 2013-12-20-wheezy-raspbian.zip

## Premier lancement de qemu

Il y a un peu de configuration afin de faire fonctionner l'image. On démarre donc d'abord qemu sur l'image raspbian en exécutant un shell (option init=/bin/bash):

	qemu-system-arm -kernel kernel-qemu -cpu arm1176 -m 256 -M versatilepb -no-reboot -serial stdio -append "root=/dev/sda2 panic=1 rootfstype=ext4 rw init=/bin/bash" -hda 2013-12-20-wheezy-raspbian.img 

Cette commande devrait donc démarrer un bash, il faut ensuite éditer le fichier /etc/ld.so.preload afin de mettre en commentaire la ligne suivante :

	#/usr/lib/arm-linux-gnueabihf/libcofi_rpi.so

Sauvegarder puis arrêter qemu:

	halt

ou Ctrl-D

## Véritable démarrage de Raspbian

On peut ensuite relancer qemu sans l'option init :

	qemu-system-arm -kernel kernel-qemu -cpu arm1176 -m 256 -M versatilepb -no-reboot -serial stdio -append "root=/dev/sda2 panic=1 rootfstype=ext4 rw" -hda 2013-12-20-wheezy-raspbian.img 

Attendre ensuite patiemment le démarrage... L'écran de config de raspbian devrait s'afficher. Après configuration basique, on obtient un shell ressemblant à ça :

![](/images/qemu-raspi/resultat.png)


## Problèmes rencontrés

Sous Ubuntu 12.04, avec qemu 1.0 installé par :
	sudo apt-get install qemu-system qemu-user qemu-kvm-extras

Le lancement de qemu donne des erreurs sur l'accès SCSI :

![](/images/qemu-raspi/erreur-scsi.png)

L'installation d'une version plus récente de qemu (1.5) a permis de résoudre le problème.

## Liens

* http://xecdesign.com/qemu-emulating-raspberry-pi-the-easy-way/
* http://www.soslug.org/wiki/raspberry_pi_emulation

## Installation d'un environnement de cross-compilation

L'étape d'après consistera à voir comment compiler une application pour architecture ARM à partir d'un Linux.

	git clone git://github.com/raspberrypi/tools.git
	
La suite au prochain numéro !

