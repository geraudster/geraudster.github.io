---
layout: post
title: Réparer un DVD sous Ubuntu avec ddrescue
tags: dvd ubuntu ddrescue
comments: true
tracking: true
share: true
---

Lors de la copie d'un [DVD vers la tablette Storio]({% post_url 2014-07-16-copier-un-DVD-sur-storio2 %}), j'ai rencontré plusieurs
problèmes à la sauvegarde du DVD: lenteur (plusieurs heures pour une simple
copie, séries d'erreurs dans les logs systèmes...). Cet article décrit comment
contourner ce problème sous Ubuntu.

## Le problème

Le problème s'est manifesté par une lenteur inhabituelle de la copie du DVD
par OGMRip.

En jetant un coup d'oeil dans les logs du système (avec `dmesg`), on voit
une série d'erreurs d'I/O. Voici un extrait:

{% highlight console %}
[50097.605036] sr 0:0:0:0: [sr0] Unhandled sense code
[50097.605046] sr 0:0:0:0: [sr0]  
[50097.605051] Result: hostbyte=DID_OK driverbyte=DRIVER_SENSE
[50097.605057] sr 0:0:0:0: [sr0]  
[50097.605061] Sense Key : Medium Error [current] 
[50097.605070] sr 0:0:0:0: [sr0]  
[50097.605076] Add. Sense: L-EC uncorrectable error
[50097.605083] sr 0:0:0:0: [sr0] CDB: 
[50097.605086] Read(10): 28 00 00 02 e3 60 00 00 02 00
[50097.605107] end_request: I/O error, dev sr0, sector 757120
[50097.605116] Buffer I/O error on device sr0, logical block 189280
[50097.605123] Buffer I/O error on device sr0, logical block 189281
{% endhighlight %}

Il s'agit apparemment d'une protection sur le DVD (cf. cet article sur
[LinuxFR](http://linuxfr.org/forums/linux-general/posts/probl%C3%A8me-lors-de-la-copie-de-dvd)). Nous allons voir comment régler le problème avec ddrescue.

## Utilisation de ddrescue

La soluce consiste à utiliser [GNU ddrescue](http://www.gnu.org/software/ddrescue/) afin
de créer une iso à partir du DVD. `ddrescue` propose plusieurs options
pour gérer les erreurs de lecture :

{% highlight console %}
ddrescue --direct --block-size=2048 --no-split /dev/sr0 Disk.iso disc.log
{% endhighlight %}

Ce qui donne la sortie suivante :
{% highlight console %}

GNU ddrescue 1.17
Press Ctrl-C to interrupt
rescued:     5450 MB,  errsize:   1362 MB,  current rate:    20480 B/s
   ipos:     5451 MB,   errors:       3,    average rate:    4756 kB/s
   opos:     5451 MB,    time since last successful read:       0 s
Finished                   

{% endhighlight %}

Lorsque la commande est terminée, il est possible de monter l'image iso
pour consulter son contenu. OGMRip permet aussi de ripper le contenu
à partir de cette image iso.

