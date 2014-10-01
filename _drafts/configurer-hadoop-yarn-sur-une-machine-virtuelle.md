---
layout: post
title: Configurer Hadoop YARN sur une machine virtuelle
tags: hadoop yarn mapreduce bigdata vagrant vm
comments: true
tracking: true
share: true
---

Cet article décrit l'installation et la configuration d'Hadoop sur 
une machine virtuelle. Le principe est de pouvoir utiliser un VM dans
le cadre de démo ou de formations.

## Configuration de la VM

On utilise vagrant avec une box de type centos 6.4 en 64 bits.
Les caractéristiques de la VM sont les suivantes:

* 2 CPU
* 3 Go RAM
* 1 data volume de 5Go (min)
* Réseau privé sur l'IP 192.168.1.30
* Image Virtual Box

## Installation d'Hadoop

L'installation de base d'Hadoop est très facile: elle consiste à récupérer une archive d'Hadoop
et de la décompresser.
Nous allons installer tout ce qui concerne Hadoop dans le répertoire /usr/local/hadoop.
Le user vagrant sera chargé de l'éxécution des services Hadoop, il faudra donc lui donner les droits
nécessaires.

{% highlight console %}
vagrant@vagrant-centos64:~$ sudo -i
root@vagrant-centos64:/home/vagrant# wget http://mirror.bbln.org/apache/hadoop/common/hadoop-2.5.1/hadoop-2.5.1.tar.gz
root@vagrant-centos64:/home/vagrant# tar xvzf hadoop-2.5.1.tar.gz
root@vagrant-centos64:/home/vagrant# mv hadoop-2.5.1 /usr/local/hadoop
root@vagrant-centos64:/home/vagrant# mkdir -p /usr/local/hadoop/tmp
root@vagrant-centos64:/home/vagrant# mkdir -p /usr/local/hadoop/logs
root@vagrant-centos64:/home/vagrant# chown -R vagrant /usr/local/hadoop/{tmp,logs}
root@vagrant-centos64:/home/vagrant# Ctrl+D
{% endhighlight %}

Quelques variables d'environnements sont à renseigner:

{% highlight bash %}
export HADOOP_PREFIX=/usr/local/hadoop
export PATH=$HADOOP_PREFIX/bin:$HADOOP_PREFIX/sbin:$PATH
{% endhighlight %}

Ici on a ajouté les répertoires bin et sbin (pour le démarrage des services).

On peut désormais tenter de démarrer HDFS:

{% highlight console %}
vagrant@vagrant-centos64:~$ start-dfs.sh
vagrant@vagrant-centos64:~$ jps
vagrant@vagrant-centos64:~$ hdfs dfs -ls /
{% endhighlight %}

Nous allons voir par la suite comment tuner les paramètres hadoop pour pouvoir lancer
des jobs MapReduce sur notre VM limitée en mémoire.

## Configuration et démarrage de HDFS

## Configuration et démarrage de YARN

## Pour conclure...



