---
layout: post
title: Nettoyer Ubuntu en ligne de commande
tags: ubuntu admin
comments: true
tracking: true
share: true
---

![Ubuntu](/images/clean-ubuntu/ubuntu-orange.gif)Un Ubuntu a tendance à s'encrasser et à conserver au fil des mises à jour 
un certain nombre de saletés (vieux kernels inutilisés, connexions réseau dans 
NetworkManager, vieux fichiers de config...). Ayant été confronté au problème (mon pc est sous Ubuntu depuis 2008 et a subi plusieurs dist-upgrade), j'ai cherché comment nettoyer tout ça en ligne de commande !

## Nettoyage d'apt

Suppression des paquets partiels:

	sudo apt-get autoclean

Suppression des .deb téléchargés:

	sudo apt-get clean

Suppression des dépendances inutilisées:

	sudo apt-get autoremove

Suppression des packages de debug (ils prennent de la place et ne sont pas forcément nécessaires):

	sudo apt-get remove .*-dbg

## Nettoyage des anciens noyaux

Les anciens noyaux Linux sont conservés et peuvent prendre de la place pour rien.
Voici comment faire pour nettoyer les anciens kernels (Merci à StackOverflow):

	sudo apt-get remove --purge $(dpkg -l 'linux-image-*' | sed '/^ii/!d;/'"$(uname -r | sed "s/\(.*\)-\([^0-9]\+\)/\1/")"'/d;s/^[^ ]* [^ ]* \([^ ]*\).*/\1/;/[0-9]/!d')

## Suppression des fichiers de configuration inutilisés

Après plusieurs dist-upgrade, certains fichiers de configuration obsolètes demeurent, pour les supprimer:

	sudo aptitude purge ?config-files

## Suppression des connexions réseaux

Au fil des années, la liste des connexions dans l'applet NetworkManager peut devenir importante. Il est possible de les supprimer grâce à l'outil [nmcli](http://manpages.ubuntu.com/manpages/maverick/man1/nmcli.1.html).

Suppression des connexions jamais utilisées:

	nmcli --fields UUID,TIMESTAMP-REAL c list | \
		grep never | cut -f1 -d ' ' | \
		xargs -n 1 nmcli c delete uuid

Suppression des connexions non utilisées depuis un certain temps (modifier la date 2014-01-01 selon vos besoins) :

	LANG=C nmcli --fields UUID,TIMESTAMP-REAL c list | \
		dateutils.dgrep --lt '2014-01-01' -i "%a %b %d %T %Y" | \
		cut -f1 -d ' ' | xargs -n 1 nmcli c delete uuid

Il vous faudra peut-être installer [*dateutils*](http://packages.ubuntu.com/trusty/dateutils) auparavant.

![Ubuntu is now clean!](/images/clean-ubuntu/clean.jpg)

## Liens

* [Debian Cleanup](http://raphaelhertzog.com/2011/01/31/debian-cleanup-tip-1-get-rid-of-useless-configuration-files/)
* [8 ways to maintain clean lean Ubuntu machine](http://www.maketecheasier.com/8-ways-to-maintain-a-clean-lean-ubuntu-machine/)
