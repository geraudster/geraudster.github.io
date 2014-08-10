---
layout: post
title: Déboires avec Ubuntu 14.04 LTS
tags: ubuntu tips
comments: true
tracking: true
share: true
---

## Problèmes avec le wifi

Après une mise à jour de 13.10 à 14.04, le réseau Wifi habituel n'est plus reconnu. Il faut supprimer les drivers propriétaires:

	sudo apt-get purge bcmwl-kernel-source

Installer ensuite le bon paquet:

	sudo apt-get update # au cas où
	sudo apt-get install linux-firmware-nonfree

Ubuntu ne retrouve plus votre réseau: avec le dernier driver, Ubuntu ne semble pas reconnaître les réseaux sur les canaux au-delà de 11... Donc en changeant le canal de votre réseau, il devrait apparaître dans la nm-applet.

## Problème  "no talloc stackframe at ../source3/param/loadparm.c:4864, leaking memory" 

Il s'agit d'un problème avec le module pam pour samba. J'ai réglé le problème avec :

	sudo apt-get remove libpam-smbpass

## Problèmes dns

Si dans dmesg vous avez ce genre de message:

 	[  368.553363] systemd-hostnamed[3478]: Warning: nss-myhostname is not installed. Changing the local hostname might make it unresolveable. Please install nss-myhostname!

Installer nss-myhostname:

	sudo apt-get install libnss-myhostname

## Problèmes driver carte video

Dans dmesg:

	[  103.647803] WARNING: CPU: 1 PID: 1234 at /build/buildd/linux-3.13.0/drivers/gpu/drm/i915/intel_display.c:9237 intel_modeset_check_state+0x5dd/0x750 [i915]()
	[  103.647806] encoder's hw state doesn't match sw tracking (expected 0, found 1)


Voir: 

* [Bug kernel](https://bugs.freedesktop.org/show_bug.cgi?id=81537)
* [Bug Ubuntu](https://bugs.launchpad.net/ubuntu/+source/linux/+bug/1343543)

## Liens

* Choix du driver pour le wifi : http://ubuntuforums.org/showthread.php?t=2214110
* Problème PAM Samba : http://ubuntuforums.org/showthread.php?t=2214042

