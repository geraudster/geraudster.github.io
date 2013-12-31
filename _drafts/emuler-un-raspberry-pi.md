---
layout: post
title: Emuler un Raspberry Pi avec Qemu
tags: qemu arm raspberry
comments: true
tracking: true
share: true
---

# Liens

* http://xecdesign.com/qemu-emulating-raspberry-pi-the-easy-way/
* http://www.soslug.org/wiki/raspberry_pi_emulation

# Etapes

	sudo apt-get install qemu-system qemu-user qemu-kvm-extras

	wget http://archive.raspbian.org/installer/rpi_installer_08-19-12.zip
	wget http://downloads.raspberrypi.org/raspbian_latest


	mkdir -p vm/raspbian
	cd vm/raspbian
	unzip ~/rpi_installer_08-19-12.zip
	unzip ~/2013-12-20-wheezy-raspbian.zip

# Lancement de qemu

	sudo qemu-system-arm -kernel kernel.img -cpu arm1176 -M \
		versatilepb -no-reboot -append "root=/dev/sda2 panic=1" \
		-hda 2013-12-20-wheezy-raspbian.img

