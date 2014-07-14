---
layout: post
title: Copier un DVD sur une tablette VTech Storio2
tags: dvd video ffmpeg ubuntu vtech storio2
comments: true
tracking: true
share: true
---

Ce post vise à décrire comment convertir un DVD pour le transférer sur la tablette
Storio 2 (ref[]).

## 1ère étape

Ripper le DVD, ça y est, le gros mot est lâché. On utilisera OGMRip sous Ubuntu pour
ripper le DVD :
*TODO*

## 2nde étape

Convertir la vidéo.

Plusieurs variantes avec ffmpeg:

	avconv -i THE_JUNGLE_BOOK_DIAMOND_EDITION.avi -qmax 8 -qmin 2 -r:v 15 -b:v 2400k -s 480x272 -c:v mjpeg -c:a libmp3lame -ac 1 -ar 24000 test.avi
	=> saccadé + décalage du son

	avconv -i THE_JUNGLE_BOOK_DIAMOND_EDITION.avi -qmax 8 -qmin 2 -r:v 15 -b:a 96k -s 480x272 -c:v mjpeg -c:a libmp3lame -ac 1 -ar 24000 test.avi

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

## Liens

* Doc ffmpeg pour Ubuntu : http://doc.ubuntu-fr.org/ffmpeg
* Explication ffmpeg : http://www.jcartier.net/spip.php?article57
* Spec Storio : http://www.familledegeeks.fr/comment-resoudre-les-petits-soucis-dutilisation-de-la-storio-de-vtech/
* Autres tentatives : http://geekmps.fr/tutoriaux/464-convertir-une-video-pour-storio-2-vtech

