---
layout: post
title: Copier des fichiers via Telnet avec Netcat
tags: shell network netcat
comments: true
tracking: true
share: true
---

Telnet est un protocole généraliste utilisé pour établir des connexions distantes.
La principale utilisation est de fournir des sessions Telnet auxquelles on s'identifie via login/mot de passe afin d'accéder à un shell sur l'hôte distant.
C'est notamment utilisé dans le monde de l'embarqué pour se connecter au matériel et avoir accès à des commandes, des logs...
Le problème est qu'il arrive un moment où on a besoin de transférer des fichiers (pour récupérer un fichier de logs par exemple) mais les clients Telnet classiques ne le permettent pas. C'est là qu'arrive [netcat](http://nc110.sourceforge.net/) (nc), qui va permettre de scripter sur des connexions Telnet (notamment)

# Qu'est-ce donc que Netcat ?

Netcat est un petit utilitaire sous Unix qui permet de lire et d'envoyer des données sur le réseau.
En gros, il va permettre un fonctionnement soit en mode serveur (il existe des exemples d'implémentation de serveur HTTP ou de chat avec nc) soit en mode client (là encore les usages sont variés comme l'envoi de mails, l'utilisation comme client HTTP...) et d'envoyer ou recevoir des données. Dans le cas de la copie via le protocole Telnet, nous allons nous intéresser au fonctionnement en mode client.

# Scripter Telnet

À l'inverse du protocole SSH, Telnet ne permet pas de copier nativement des fichiers (du  moins à ma connaissance). Telnet vise à fournir une console interactive sur un serveur distant.
Mais si on souhaite automatiser certaines tâches, telle que la récupération d'un fichier, il faut passer par du scripting.

Traditionnellement, si je veux consulter le fichier `/tmp/toto.txt`, je me connecte via telnet :
{% highlight console %}
$ telnet my-remote-host
Trying xxx.yyy.zzz.123...
Connected to xxx.yyy.zzz.123.

QNX Neutrino (mmx) (ttyp0)
login: geraud

 $ cat /tmp/toto.txt
 Hello toto
 $ exit
$
{% endhighlight %}


L'idéal serait plutôt d'avoir une commande permettant de récupérer le fichier en local, un peu de cette manière :

{% highlight console %}
$ telnetcp geraud@my-remote-host:/tmp/toto.txt .
{% endhighlight %}

Pour celà, l'idée serait de scripter Telnet pour récupérer la sortie de la session telnet dans un fichier, genre :
{% highlight console %}
$ (echo geraud; echo "cat /tmp/toto.txt"; exit) | telnet my-remote-host
{% endhighlight %}

Sauf que le client telnet est prévu pour des sessions interactives et qu'il faudrait placer des commandes sleep entre chaque commande pour avoir une chance qu'elle soit prise en compte...
Typiquement, si on exécute cette commande on obtient le résultat suivant :
{% highlight console %}
Trying xxx.yyy.zzz.123...
Connected to xxx.yyy.zzz.123.

QNX Neutrino (mmx) (ttyp0) 
geraud
Connection closed by foreign host.
{% endhighlight %}

Telnet envoie le login avant d'avoir obtenu la mire de login...
Heureusement Netcat est là.

# Scriptons avec Netcat

![Netcat](http://cdn.memegenerator.net/instances/500x/48996187.jpg)

Netcat va permettre de gérer les interactions de la session telnet avec l'option `-t`. On peut également paramétrer des délais d'attente entre chaque commande, ou bien lui dire d'attendre la fin de la connexion du serveur pour se terminer.

Donc il devient possible de lancer la commande suivante :
{% highlight console %}
$ (echo geraud; echo "cat /tmp/toto.txt"; exit) | nc -q -1 -t my-remote-host 23 
 Trying xxx.yyy.zzz.123...
 Connected to xxx.yyy.zzz.123.

 QNX Neutrino (mmx) (ttyp0)
 login: geraud

 $ cat /tmp/toto.txt
  Hello toto
 $ exit
$
{% endhighlight %}

Quelques explications:

* L'option `-q` avec une valeur négative indique que Netcat doit attendre jusqu'à la fermeture de la connexion avant de se terminer.
* L'option `-t` permet de dialoguer dans une session telnet.
* D'autre part, Netcat étant un outil généraliste, il est nécessaire de lui préciser le port 23.

Nous avons bien le contenu de notre fichier toto.txt. On peut améliorer les choses avec quelques commandes tail/head et redirections :

{% highlight console %}
$ (echo geraud; echo "cat /tmp/toto.txt"; exit) | nc -q -1 -t my-remote-host 23 | tail -n +6 | head -n -1 > toto.txt
$ cat toto.txt
Hello toto
$
{% endhighlight %}

# Création du script

Vous trouverez dans ce [gist](https://gist.github.com/geraudster/11290152#file-telcp), le script *telcp* qui regroupe toutes ces commandes afin de proposer une interface similaire à scp:
{% highlight console %}
$ telcp geraud@my-remote-host:/tmp/toto.txt .
$ cat toto.txt
Hello toto
$
{% endhighlight %}

