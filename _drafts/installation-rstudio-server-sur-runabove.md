---
layout: post
title: Installer RStudio Server sur RunAbove
tags: R devops runabove vm
comments: true
tracking: true
share: true
---

## Création de l'instance

## Sur la VM

{% highlight bash %}
sudo sh -c "echo 'deb http://ftp.igh.cnrs.fr/pub/CRAN/bin/linux/ubuntu trusty/' >> /etc/apt/sources.list"
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys E084DAB9
sudo apt-get update
sudo apt-get -y upgrade
sudo apt-get -y install vim git r-base r-base-dev
{% endhighlight %}

{% highlight bash %}
sudo apt-get install gdebi-core
sudo apt-get install libapparmor1
wget http://download2.rstudio.org/rstudio-server-0.98.1087-amd64.deb
sudo gdebi rstudio-server-0.98.1087-amd64.deb

{% endhighlight %}


Configuration du port:

/etc/rstudio/rserver.conf
www-port=80
sudo service rstudio-server restart

