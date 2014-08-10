---
layout: post
title: Installer Cyanogenmod sur HTC Wildfire
tags: unlock htc cyanogenmod wildfire clockworkmod htcdev android
comments: true
tracking: true
share: true
---

Dans l'idée de recycler mon vieux [HTC Wildfire](http://fr.wikipedia.org/wiki/HTC_Wildfire)  pour en faire un lecteur multimédia, je me suis lancé dans la mise à jour du portable (à la base sous Android 2.2). C'était un peu plus compliqué que prévu car il a d'abord fallu débloquer le téléphone avec les outils fournis par [HTCDev](http://htcdev.com), puis installer [ClockWork](http://wiki.cyanogenmod.org/w/ClockWorkMod_Instructions) à la main, avant de faire l'upgrade vers [Cyanogenmod](http://www.cyanogenmod.org/).

Ce post décrit les différentes étapes qui ont permis de réaliser l'installation de Cyanogenmod 7 (pas de version plus récente disponible sur HTC Wildfire...):

* Activation du mode root
* Installation de Clockworkmod Recovery
* Installation de Cyanogenmod

## Activer root sur le Wildfire

Sous windows:

Pré-requis:

* Installer http://developer.android.com/sdk/installing/index.html?pkg=tools
* Installer HTC sync (http://www.htc.com/fr/support/content.aspx?id=6228)

Démarche:

* Se connecter à HTCdev (http://www.htcdev.com/bootloader/ruu-downloads), choisir "Unlock root..."

![Step 1](/images/cyanogenmod-wildfire/shot_00.png)

* Choisir la version du logiciel (Vodafone FR pour SFR)

![Step 1](/images/cyanogenmod-wildfire/shot_01.png)

* L'installer et le lancer

![Step 1](/images/cyanogenmod-wildfire/shot_02.png)

* Brancher le téléphone avec mode debug USB

![Step 1](/images/cyanogenmod-wildfire/shot_03.png)

* Dérouler ensuite la séquence:

![Step 1](/images/cyanogenmod-wildfire/shot_04.png)


![Step 1](/images/cyanogenmod-wildfire/shot_05.png)


![Step 1](/images/cyanogenmod-wildfire/shot_06.png)

* Si vous êtes vraiment sûr de ce que vous faites, vous pouvez cliquer sur Next. La barre de progression s'affiche alors:

![Step 1](/images/cyanogenmod-wildfire/shot_07.png)


![Step 1](/images/cyanogenmod-wildfire/shot_08.png)

Super, vous venez d'installer le mode fastboot sur votre Wildfire! Il ne reste plus qu'à le débloquer. Pour cela, nous continuons sur le site htcdev. L'idée est de récupérer un jeton de déblocage à partir de l'identifiant du téléphone.

* Retour sur le site, clic sur "Proceed to Step 1"
* Télécharger le fastboot pour votre OS. Il s'agit d'un outil en ligne de commande permettant de modifier des données sur le portables.
* Redémarrer le téléphone en appuyant sur volume bas et power.
* Ouvrir un terminal puis:

{% highlight console %}
	fastboot oem get_identifier_token
{% endhighlight %}

* Un token est alors affiché dans la console, le copier dans le formulaire du site. Vous allez alors recevoir un mail avec un fichier Unlock_code.bin en pièce jointe.
* Récupérer le code de déblocage puis:

{% highlight console %}
    fastboot flash unlocktoken Unlock_code.bin
{% endhighlight %}

Et voilà! Votre Wildfire est débloqué! Ouf! Voyons voir comment installer le ClockworkMod qui va nous permettre ensuite d'installer de nouveaux OS (dont Cyanogenmod évidemment).

## Installer Clockworkmod

* Récupérer le package http://uploaded.net/file/lr4airzm
* Le décompresser en local, puis avec le téléphone démarré en mode Fastboot USB, lancer la commande:

{% highlight console %}
    fastboot flash recovery recovery-clockwork-5.0.2.6.111007-j_r0dd_mod-buzz.img
{% endhighlight %}

* Débrancher le téléphone et redémarrer en mode recovery
* Faire un wipe des data
* Faire un recovery => le téléphone doit booter sur Clockwork
* Refaire un wipe (utiliser les boutons volumes et le joystick pour valider les choix)
 
## Installation de Cyanogenmod
 
Finalement, il s'agit de la partie la plus facile...

* Depuis le téléphone, télécharger une image de cyanogenmod sur la sd (http://download.cyanogenmod.org/?device=buzz)
* Si vous souhaitez installer les outils Google, vous pouvez aussi télécharger les gapps [ici](http://goo-inside.me/gapps/gapps-gb-20110828-signed.zip)
* Redémarrer le téléphone en mode recovery, sous ClockworkMod, suivez la procédure habituelle d'update (backup, wipe, install from zip pour cyanogenmod, install from zip pour Gapps, reboot)

Et voilà, le tour est joué!


