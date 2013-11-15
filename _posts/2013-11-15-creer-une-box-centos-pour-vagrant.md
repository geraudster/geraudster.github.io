---
layout: post
title: Créer une box CentOS pour Vagrant
tags: centos vm vagrant devops
comments: true
tracking: true
share: true
---

[Vagrant](http://vagrantup.com) est un outil facilitant la gestion d'environnements virtualisés.
À partir de *boxes* (qui sont des images de VMs pré-configurées), Vagrant offre la possibilité 
de démarrer puis de personnaliser ses VMs.

# Principe

L'exemple de base pour récupérer une box Ubuntu :

	mkdir test_vm
	cd test_vm
	vagrant init precise32 http://files.vagrantup.com/precise32.box # crée un fichier de conf VagrantFile dans le répertoire courant
	vagrant up # démarre la VM
	vagrant ssh # connexion à la VM via... ssh
	
Par la suite, il sera possible de modifier le fichier VagrantFile pour personnaliser la VM (par le lancement de script shell par exemple).
Cela fera l'objet d'un prochain post :)

Pour la suite de cet article, nous allons voir comment créer notre propre box, basée sur [CentOS](http://www.centos.org) 6.4.

 
# Avant de commencer

1. Installer VirtualBox (version 4.3.2) et l'extension pack (nécessaire pour l'USB)
2. Installer Vagrant (version 1.5.3)

# Installation de CentOS 6.4 sur la VM

## Installation de l'OS

* Récupération de l'iso netinstall depuis [http://mirror.ovh.net/ftp.centos.org/6.4/isos/x86_64/CentOS-6.4-x86_64-netinstall.iso](http://mirror.ovh.net/ftp.centos.org/6.4/isos/x86_64/CentOS-6.4-x86_64-netinstall.iso)
* Créer une nouvelle VM depuis VirtualBox (Linux, Redhat 64 bit, 2Go RAM)
* Booter sur l'iso, et utiliser les paramètres suivants
	* Url du mirroir : http://mirror.centos.org/centos-6/6/os/x86_64/
	* hostname = vagrant-centos64
	* mot de passe root = vagrant
	* Sélectionner le type d'installation "Minimal Server"
* Redémarrer après l'installation
* Se connecter en root et mettre à jour le système :
{% highlight console %}
root# yum update -y
{% endhighlight %}

## Configuration post-installation

* Configurer la redirection des ports depuis VirtualBox :
![](/images/vagrant/redirection_rule_vbox.png)

* Il est alors possible de se connecter via Putty/SSH au port 2222 :
![](/images/vagrant/login_ssh.png)

* Création de l'utilisateur vagrant (mot de passe = vagrant) :
{% highlight console %}
[root@vagrant-centos64 ~]# adduser vagrant
[root@vagrant-centos64 ~]# ls /home
lost+found  vagrant
[root@vagrant-centos64 ~]# id vagrant
uid=500(vagrant) gid=500(vagrant) groups=500(vagrant)
[root@vagrant-centos64 ~]# passwd vagrant
Changing password for user vagrant.
New password:
BAD PASSWORD: it is based on a dictionary word
BAD PASSWORD: is too simple
Retype new password:
passwd: all authentication tokens updated successfully.
{% endhighlight %}

* Configuration des sudoers :
{% highlight console %}
# visudo -f /etc/sudoers.d/vagrantadmin
	
Defaults   env_keep += "SSH_AUTH_SOCK"

vagrant ALL=NOPASSWD: ALL
{% endhighlight %}

* Mettre en commentaire la ligne requiretty dans le fichier sudo :
{% highlight console %}
# visudo
{% endhighlight %}

* Test de sudo avec le user vagrant :
{% highlight console %}
[vagrant@vagrant-centos64 ~]$ sudo -l
Matching Defaults entries for vagrant on this host:
    requiretty, !visiblepw, always_set_home, env_reset, env_keep="COLORS
    DISPLAY HOSTNAME HISTSIZE INPUTRC KDEDIR LS_COLORS", env_keep+="MAIL PS1
    PS2 QTDIR USERNAME LANG LC_ADDRESS LC_CTYPE", env_keep+="LC_COLLATE
    LC_IDENTIFICATION LC_MEASUREMENT LC_MESSAGES", env_keep+="LC_MONETARY
    LC_NAME LC_NUMERIC LC_PAPER LC_TELEPHONE", env_keep+="LC_TIME LC_ALL
    LANGUAGE LINGUAS _XKB_CHARSET XAUTHORITY",
    secure_path=/sbin\:/bin\:/usr/sbin\:/usr/bin, env_keep+=SSH_AUTH_SOCK

User vagrant may run the following commands on this host:
    (root) NOPASSWD: ALL
[vagrant@vagrant-centos64 ~]$ sudo ls

{% endhighlight %}

* Ajout de la clé vagrant (pour la connexion avec la command vagrant ssh) :
{% highlight console %}
[vagrant@vagrant-centos64 ~]$ mkdir ~/.ssh
[vagrant@vagrant-centos64 ~]$ curl -k https://raw.github.com/mitchellh/vagrant/master/keys/vagrant.pub > .ssh/authorized_keys
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
102   409  102   409    0     0     73      0  0:00:05  0:00:05 --:--:--  3556
[vagrant@vagrant-centos64 ~]$ chmod 0700 .ssh
[vagrant@vagrant-centos64 ~]$ chmod 0600 .ssh/authorized_keys
{% endhighlight %}

* Nettoyage des paquets :
{% highlight console %}
[vagrant@vagrant-centos64 ~]$ sudo yum clean all
{% endhighlight %}

* Installer les extensions VirtualBox, depuis Périphériques -> Installer les Additions invités... :
{% highlight console %}
[vagrant@vagrant-centos64 ~]$ sudo mkdir /mnt/cdrom
[vagrant@vagrant-centos64 ~]$ sudo mount /dev/cdrom /mnt/cdrom
mount: block device /dev/sr0 is write-protected, mounting read-only
[vagrant@vagrant-centos64 ~]$ sudo sh /mnt/cdrom/VBoxLinuxAdditions.run
{% endhighlight %}

* Éteindre la VM.

## En cas de problèmes

Lors de l'installation par le réseau de Centos, s'il bloque sur l'installation d'un paquet
cela peut venir du fait que le mirroir n'est plus disponible (ou n'est pas assez rapide). Il faut alors
relancer l'installation avec une nouveau mirroir (à noter que le redémarrage est obligatoire...)

# Création de la box

Une box est en fait une archive contenant :
* un export de VM au format VirtualBox
* un fichier metadata.json indiquant le *provider* utilisé (ici VirtualBox)

On utilise la commande *vagrant package*, qui permet de créer l'archive au bon format : 

{% highlight console %}
> vagrant package --output centos64-64.box --base "Centos 6.4 box"
[Centos 6.4 box] Clearing any previously set forwarded ports...
[Centos 6.4 box] Creating temporary directory for export...
[Centos 6.4 box] Exporting VM...
[Centos 6.4 box] Compressing package to: .../centos64-64.box
{% endhighlight %}


# Utilisation de la box

Avant d'utilier la box, il faut l'enregistrer dans la liste des box disponible :

{% highlight console %}
> vagrant box add centos64-64 centos64-64.box
Downloading or copying the box...
Extracting box...ate: 29.4M/s, Estimated time remaining: --:--:--)
Successfully added box 'centos64-64' with provider 'virtualbox'!
{% endhighlight %}

Ensuite, il ne reste plus qu'à démarrer :

	mkdir test_vm
	cd test_vm
	vagrant init centos64-64
	vagrant up
	vagrant ssh

# Quelques liens

* Inspiré de [cet article](https://github.com/okfn/ckan/wiki/How-to-Create-a-CentOS-Vagrant-Base-Box)
* [www.vagrantup.com](http://www.vagrantup.com)

