## Nettoyage d'apt

Suppression des paquets partiels:

	sudo apt-get autoclean

Suppression des .deb téléchargés:

	sudo apt-get clean

Suppression des dépendances inutilisées:

	sudo apt-get autoremove

Suppression des packages de debug:

	sudo apt-get remove .*-dbg
## Nettoyage des anciens noyaux

Utiliser Ubuntu Tweak

## Suppression des fichiers de configuration inutilisés

	sudo aptitude purge ?config-files

## Liens

* Debian Cleanup : http://raphaelhertzog.com/2011/01/31/debian-cleanup-tip-1-get-rid-of-useless-configuration-files/
* 8 ways to maintain clean lean Ubuntu machine : http://www.maketecheasier.com/8-ways-to-maintain-a-clean-lean-ubuntu-machine/
