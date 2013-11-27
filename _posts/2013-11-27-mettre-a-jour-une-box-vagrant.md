---
layout: post
title: Mettre à jour une box Vagrant
tags: centos vm vagrant devops
comments: true
tracking: true
share: true
---

Suite de l'article sur la création d'une box Vagrant... Au bout de quelques jours d'utilisation de votre box, vous vous rendez compte qu'il manque quelquechose (de l'espace disque, un nouveau disque...).

Vous souhaitez donc mettre à jour la box par défaut avec ces nouveaux éléments. C'est tout à fait possible, il suffit de :
* démarrer une VM,
* modifier la configuration avec VirtualBox
* arrêter la VM
* recréer la box

Dans le détail :

    $ mkdir updated-box
    $ cd updated-box
    $ vagrant init centos64-64
    $ vagrant up
    $ vagrant halt

Accéder à la configuration de la vm via VirtualBox (la VM doit avoir un nom du genre centos64-64\_default\_1385542165).
Recréer ensuite la box:

    $ vagrant package --base centos64-64_default_1385542165 --output centos64-64.box
    $ vagrant box remove centos64-64
    $ vagrant box add centos64-64

Ensuite, dans une VM déjà créée :

    $ rm -rf .vagrant
    $ vagrant up

