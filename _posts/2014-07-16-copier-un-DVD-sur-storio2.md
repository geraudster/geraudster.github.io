---
layout: post
title: Copier un DVD sur une tablette VTech Storio2
tags: dvd video ffmpeg ubuntu vtech storio2
comments: true
tracking: true
share: true
---

Ce post vise à décrire comment convertir un DVD pour le transférer sur la tablette
[Storio 2 ](http://www.vtech-jouets.com/vtech-et-vous/manuels/storio2.html).
Ayant eu des difficultés pour réaliser cette tâche en une étape, cela va finalement
se passer en 2 étapes:

* "Rippage" du DVD
* Conversion de la vidéo récupérée

## 1ère étape

![OGMRip](/images/storio/ogmrip.png)
Ripper le DVD, ça y est, le gros mot est lâché. On utilisera OGMRip sous Ubuntu pour
ripper le DVD. Il a l'avantage d'être simple à utilisere et de proposer des profils par défaut.

1. Lancer OGMRip
2. Charger le DVD : 
![OGMRip - choix DVD](/images/storio/ogmrip_01.png)

3. Lancer l'extraction et sélectionner le profil :  
![OGMRip - choix profil](/images/storio/ogmrip_02.png)

Il n'y a plus qu'à patienter, cela va prendre un peu de temps.

## 2nde étape

Nous allons maintenant convertir la vidéo au format attendu par la Storio.
Suite aux différentes mises à jour de la tablette, il s'avère que le format
AVI avec encodage video h264 n'est plus supporté. Il faut donc utiliser le
format MJPEG.

Ici, nous allons passer en ligne de commande et utiliser ffmpeg. Vous trouverez une explication du fonctionnement de ffmpeg sur ce [blog](http://www.jcartier.net/spip.php?article57)/

Ci-dessous, la commande utilisée pour convertir en format MJPEG:

{% highlight console %}
$ avconv -i THE_JUNGLE_BOOK_DIAMOND_EDITION.avi \
         -qmax 8 -qmin 2 \
	 -r:v 15 -b:a 96k -s 480x272 \
	 -c:v mjpeg -c:a libmp3lame -ac 1 -ar 24000 test.avi
{% endhighlight %}

Quelques explications:

* ``-qmax`` et ``-qmin``: pour la configuration du variable bitrate... (ça reste un peu obscur pour moi...)
* ``-r:v 15``: nombre d'images par seconde pour la vidéo (d'où le ``v``)
* ``-b:a 96k``: le bitrate du canal audio (cf. le ``a``)
* ``-s 480x272``: le format de la video en sortie
* ``-c:v mjpeg``: le type de l'encodeur video
* ``-c:a libmp3lame``: le type de l'encodeur audio
* ``-ac 1``: nombre de canaux audio (ici 1 = mono)
* ``-ar 24000``: fréquence d'échantillonement audio


Ci-dessous un exemple de la sortie console:
{% highlight console %}
avconv version 0.8.12-6:0.8.12-0ubuntu0.13.10.1, Copyright (c) 2000-2014 the Libav developers
  built on Jun 10 2014 15:38:48 with gcc 4.8.1
Input #0, avi, from 'THE_JUNGLE_BOOK_DIAMOND_EDITION.avi':
  Metadata:
    encoder         : ogmrip-1.0.0
  Duration: 01:15:15.16, start: 0.000000, bitrate: 1287 kb/s
    Stream #0.0: Video: mpeg4 (Advanced Simple Profile), yuv420p, 640x368 [PAR 187:183 DAR 7480:4209], 25 tbr, 25 tbn, 25 tbc
    Stream #0.1: Audio: mp3, 48000 Hz, stereo, s16, 128 kb/s
Incompatible pixel format 'yuv420p' for codec 'mjpeg', auto-selecting format 'yuvj420p'
[buffer @ 0x8a74420] w:640 h:368 pixfmt:yuv420p
[scale @ 0x8a6f560] w:640 h:368 fmt:yuv420p -> w:480 h:272 fmt:yuvj420p flags:0x4
Output #0, avi, to 'test.avi':
  Metadata:
    ISFT            : Lavf53.21.1
    Stream #0.0: Video: mjpeg, yuvj420p, 480x272 [PAR 12716:12627 DAR 7480:4209], q=2-8, 200 kb/s, 15 tbn, 15 tbc
    Stream #0.1: Audio: libmp3lame, 24000 Hz, 1 channels, s16, 96 kb/s
Stream mapping:
  Stream #0:0 -> #0:0 (mpeg4 -> mjpeg)
  Stream #0:1 -> #0:1 (mp3 -> libmp3lame)
Press ctrl-c to stop encoding
frame=67728 fps=109 q=24.8 Lsize=  834838kB time=4515.20 bitrate=1514.7kbits/s dup=0 drop=45151    
video:775886kB audio:52913kB global headers:0kB muxing overhead 0.728729%

{% endhighlight %}

Une fois terminé, il ne reste plus qu'à copier le fichier avi en sortie sur 
la tablette Storio 2. Le fichier fait environ 800-900Mo pour 1h15 de film, 
il faut donc avoir installé une carte SD dans la tablette.

Pour le transfert, soit par l'Explora P@rk (peut être lent), soit directement
sur la carte SD (plus rapide) dans le répertoire ``/LLN/MOVIES``

## Liens

* [Doc ffmpeg pour Ubuntu](http://doc.ubuntu-fr.org/ffmpeg)
* [Explication ffmpeg](http://www.jcartier.net/spip.php?article57)
* [Spec Storio](http://www.familledegeeks.fr/comment-resoudre-les-petits-soucis-dutilisation-de-la-storio-de-vtech/)
* [Autres tentatives](http://geekmps.fr/tutoriaux/464-convertir-une-video-pour-storio-2-vtech)

