---
layout: post
title: Utiliser Jekyll pour blogguer sur Github
tags: jekyll
comments: true
tracking: true
share: true
---

Github.io permet d'héberger des pages gratuitement à partir d'un repository github.
En plus de pouvoir héberger de simples pages HTML, Github utilise Jekyll pour générer des pages à partir
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

# Installer Jekyll en local

Pour quoi faire ? Cela va nous permettre de prévisualier le rendu du site avant de 'pusher'
le tout.

Pré-requis : ruby

Ensuite :

    export GEM_HOME=~/rubygems/gems
    export PATH=$GEM_HOME/bin:$PATH
    gem install jekyll

Voilà, c'est tout :)

# Initialiser le site avec Jekyll

Une fois Jekyll installé, initialiser l'arborescence du site:

    cd *username*.github.io
    jekyll new .

Lancer Jekyll :

    jekyll serve

Ouvrir un navigateur à l'url http://localhost:4000/

# Arborescence d'un site

Une arborescence reconnue par Jekyll ressemble à :

{% highlight console %}
.
├── _drafts
│   ├── first-post.md
│   └── todo.md
├── Gemfile
├── images
│   ├── body-bg.png
│   ├── favicon.ico
│   ├── highlight-bg.jpg
│   ├── hr.png
│   ├── jekyll
│   ├── octocat-icon.png
│   ├── tar-gz-icon.png
│   └── zip-icon.png
├── index.html
├── javascripts
│   └── main.js
├── _layouts
│   ├── default.html
│   └── post.html
├── params.json
├── _posts
│   └── 2013-10-08-first-post.md
├── _site
│   ├── 2013
│   ├── Gemfile
│   ├── images
│   ├── index.html
│   ├── javascripts
│   ├── params.json
│   └── stylesheets
└── stylesheets
    ├── print.css
    ├── pygment_trac.css
    └── stylesheet.css

{% endhighlight %}

Dans le détail :

* ``_site`` : contient les fichiers html, md, textile... transformés par Jekyll
* ``_drafts`` : contient les brouillons, visibles uniquement lorsque l'option --drafts est passée
au démarrage de Jekyll
* ``_posts`` : contient l'ensemble des posts. Le nom du fichier d'un post doit respecter le format
AAAA-MM-JJ-\[nom de l'article\].\[format\]
* ``_layouts`` : contient les modèles de mise en page
* tout le reste (javascripts, stylesheets, images...) est copié tel quel dans le répertoire
``_site``

# Que mettre dans son .gitignore ?

En gros, tout ce qui est généré par Jekyll, donc le répertoire ``_site``.

