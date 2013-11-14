---
layout: post
title: Installer Jekyll sous Windows
tags: jekyll,windows
comments: true
tracking: true
share: true
---

Après l'install facile sous Linux, voici la version un peu plus compliquée pour Windows.
Ça peut dépanner dans certaines circonstances.

# Installer Python

Récupérer un exe sur http://www.python.org/download/releases/2.7.5/.
Et installer le package.

Ensuite:

	set PATH=D:\apps\Python-2.7;%PATH%
	
# Installer Ruby

Récupérer l'exe ici : http://rubyinstaller.org/downloads/ (prendre une version 1.9.3)
L'installer.

# Installer le DevKit

	ruby dk.rb init

Vérifier que le fichier config.yml contient bien le répertoire d'install de Ruby.

	ruby dk.rb install

Tester l'install :

	gem install json --platform=ruby
	ruby -rubygems -e "require 'json'; puts JSON.load('[42]').inspect"

Cela doit donner 42 comme résultat...
C'est l'option --platform=ruby qui permet d'utiliser le DevKit pour l'installation de gems.

# Installer Jekyll

	gem install jekyll --platform=ruby

Démarrer Jekyll :

	jekyll serve

# Résolution des problèmes

## Problème d'UTF-8

Si vous avez l'erreur suivante :

	error: incompatible encoding regexp match (UTF-8 regexp with CP850 string)
Il faut forcer ruby à utiliser l'UTF-8, pour cela, il faut modifier le fichier RUBY_HOME/bin/jekyll.bat, pour spécifier l'option -E :

	@ECHO OFF
	IF NOT "%~f0" == "~f0" GOTO :WinNT
	@"ruby.exe" "D:/apps/Ruby193/bin/jekyll" %1 %2 %3 %4 %5 %6 %7 %8 %9
	GOTO :EOF
	:WinNT
	@"ruby.exe" -E UTF-8 "%~dpn0" %*

## Problème de version de pygments

Si vous avez l'erreur suivante :

	Liquid Exception: No such file or directory - /bin/sh in _posts/2013-10...
	
C'est un problème de version de pygment, il faut désinstaller la version en cours pour installer la version 0.50:

	gem uninstall pygments.rb --version "=0.5.2"
	gem install pygments.rb --version "=0.5.0"

## Problème de python introuvable

Si vous avez l'erreur suivante :

	Liquid Exception: No such file or directory - python in _posts/2013-10...

Vérifiez que Python est bien défini dans la variable PATH.

 