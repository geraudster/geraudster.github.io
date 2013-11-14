---
layout: post
title: Créer une box CentOS pour Vagrant
tags: centos,vm,vagrant
comments: true
tracking: true
share: true
---

Inspiré de https://github.com/okfn/ckan/wiki/How-to-Create-a-CentOS-Vagrant-Base-Box

# Pré-requis

1. Installer VirtualBox
2. Installer Vagrant

# Installation de CentOS sur la VM

0. Récupération de l'iso netinstall
1. Configurer la VM
2. Booter sur l'iso,
	- Url du mirroir : http://mirror.centos.org/centos-6/6/os/x86_64/
	- hostname = vagrant-centos64
	- mot de passe root = vagrant
	- Sélectionner le type d'installation "Minimal Server"
3. Redémarrer après l'installation
4. Se connecter en root et mettre à jour le système
{% highlight console %}
root# yum update -y
{% endhighlight %}
5. Redirection des ports
![](/images/vagrant/redirection_rule_vbox.png)
6. Il est alors possible de se connecter via Putty/SSH au port 2222:
![](/images/vagrant/login_ssh.png)
7. Création de l'utilisateur vagrant (mot de passe = vagrant)
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
8. Configuration des sudoers
{% highlight console %}
# visudo -f /etc/sudoers.d/vagrantadmin
	
Defaults   env_keep += "SSH_AUTH_SOCK"

vagrant ALL=NOPASSWD: ALL
{% endhighlight %}
9. Mettre en commentaire la ligne requiretty dans le fichier sudo :
{% highlight console %}
# visudo
{% endhighlight %}
9. Test de sudo avec le user vagrant
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
8. Ajout de la clé vagrant
{% highlight console %}
[vagrant@vagrant-centos64 ~]$ mkdir ~/.ssh
[vagrant@vagrant-centos64 ~]$ curl -k https://raw.github.com/mitchellh/vagrant/master/keys/vagrant.pub > .ssh/authorized_keys
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
102   409  102   409    0     0     73      0  0:00:05  0:00:05 --:--:--  3556
[vagrant@vagrant-centos64 ~]$ chmod 0700 .ssh
[vagrant@vagrant-centos64 ~]$ chmod 0600 .ssh/authorized_keys
{% endhighlight %}
9. Nettoyage des paquets
{% highlight console %}
[vagrant@vagrant-centos64 ~]$ sudo yum clean all
{% endhighlight %}
10. Installer les extensions VirtualBox, depuis Périphériques -> Installer les Additions invités...
{% highlight console %}
[vagrant@vagrant-centos64 ~]$ sudo mkdir /mnt/cdrom
[vagrant@vagrant-centos64 ~]$ sudo mount /dev/cdrom /mnt/cdrom
mount: block device /dev/sr0 is write-protected, mounting read-only
[vagrant@vagrant-centos64 ~]$ sudo sh /mnt/cdrom/VBoxLinuxAdditions.run
{% endhighlight %}

Éteindre la VM.

## En cas de problèmes

Lors de l'installation par le réseau de Centos, s'il bloque sur l'installation d'un paquet
cela peut venir du fait que le mirroir n'est plus disponible (ou n'est pas assez rapide). Il faut alors
relancer l'installation avec une nouveau mirroir (à noter que le redémarrage est obligatoire...)

# Création de la box

{% highlight console %}
> vagrant package --output centos64-64.box --base "Centos 6.4 box"
[Centos 6.4 box] Clearing any previously set forwarded ports...
[Centos 6.4 box] Creating temporary directory for export...
[Centos 6.4 box] Exporting VM...
[Centos 6.4 box] Compressing package to: .../centos64-64.box
{% endhighlight %}


# Utilisation de la box

{% highlight console %}
> vagrant box add centos64-64 centos64-64.box
Downloading or copying the box...
Extracting box...ate: 29.4M/s, Estimated time remaining: --:--:--)
Successfully added box 'centos64-64' with provider 'virtualbox'!
{% endhighlight %}

Démarrage :

	mkdir test_vm
	cd test_vm
	vagrant init centos64-64
	vagrant up
	vagrant ssh
