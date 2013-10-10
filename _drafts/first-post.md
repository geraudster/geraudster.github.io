---
layout: post
title: Utiliser Jekyll pour blogguer sur Github
---

Github.io permet d'héberger des pages gratuitement à partir d'un repository github.
En plus de pouvoir héberger de simple page HTML, Github utilise Jekyll pour générer des pages à partir
de fichiers formatés dans divers formats tels que [Markdown](http://daringfireball.net/projects/markdown) ou [Textile](http://textile.thresholdstate.com/) (et peut-être d'autres encore...)

Deux types de sites sont proposés :

* les sites pour les projets => les pages sont stockées sur une branche particulière du projet, elle doit être nommée gh-pages
* les sites perso ou d'organisation => les pages sont stockées dans leur propre repo, nommé selon le format username.github.io ou organization.github.io

Nous allons nous intéresser au 2nd type.

# Configuration du projet sous Github

1. Créer un repository portant le nom *votre username*.github.io
2. Accéder aux préférences du repository :

    ![](/images/jekyll/creation_projet_01.png)

3. Activer le "Page Generator" :

    ![](/images/jekyll/creation_projet_02.png)

4. Saisir le contenu de la page :

    ![](/images/jekyll/creation_projet_03.png)

5. Valider puis sélectionner le thème du site :

    ![](/images/jekyll/creation_projet_04.png)

Le site est désormais initialisé, son contenu peut-être récupéré par un :

    git clone https://*votre username*@github.com/*votre username*/*votre username*.github.io.git

