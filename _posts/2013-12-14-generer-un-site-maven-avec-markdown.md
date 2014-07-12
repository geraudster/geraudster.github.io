---
layout: post
title: Générer un site Maven avec Markdown
tags: maven markdown pdf
comments: true
tracking: true
share: true
---

Maven permet d'embarquer de la documentation au sein d'un projet. Le plugin maven-site permet de créer des sites dans différents formats dont le format Markdown, assez répandu en ce moment).

Cet article décrit comment réaliser cette tâche.

## Pré-requis

Il vous faudra Maven en version 3.1.1 (il peut y avoir quelques soucis sur les versions antérieures).

## Initialisation du projet

Nous allons générer un squelette de base à l'aide du plugin archetype :

     $ mvn archetype:generate -B -DarchetypeGroupId=org.apache.maven.archetypes \
                               -DarchetypeArtifactId=maven-archetype-quickstart \
                               -DarchetypeVersion=1.0 \
                               -DgroupId=com.geraudster.demo \
                               -DartifactId=demo-site-markdown

Vous obtenez alors un beau projet Maven prêt à compiler :

    $ tree demo-site-markdown/
    ├── pom.xml
    └── src
        ├── main
        │   └── java
        │       └── com
        │           └── geraudster
        │               └── demo
        │                   └── App.java
        └── test
            └── java
                └── com
                    └── geraudster
                        └── demo
                            └── AppTest.java
    
    $ cd demo-site-markdown/
    $ mvn install
    [INFO] Scanning for projects...
    [INFO]                                                                         
    [INFO] ------------------------------------------------------------------------
    [INFO] Building demo-site-markdown 1.0-SNAPSHOT
    [INFO] ------------------------------------------------------------------------
    ...
    -------------------------------------------------------
     T E S T S
    -------------------------------------------------------
    Running com.geraudster.demo.AppTest
    Tests run: 1, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.006 sec
    
    Results :
    
    Tests run: 1, Failures: 0, Errors: 0, Skipped: 0
    
    ...
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 7.894s
    [INFO] Finished at: Fri Dec 13 12:14:52 CET 2013
    [INFO] Final Memory: 15M/170M
    [INFO] ------------------------------------------------------------------------

Jusque là tout va bien, nous pouvons passer à la suite.

## Configuration des dépendances

Au niveau du fichier pom.xml, il faut ajouter plusieurs dépendances afin d'utiliser
Markdown lors de la génération du site.

{% highlight xml %}
    <build>
        <plugins>
        ...
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-site-plugin</artifactId>
                <version>3.3</version>
                <dependencies>
                    <dependency>
                        <groupId>org.apache.maven.doxia</groupId>
                        <artifactId>doxia-module-markdown</artifactId>
                        <version>1.4</version>
                    </dependency>
                </dependencies>
            </plugin>
        ...
        </plugins>
    </build>
{% endhighlight %}

## Création du site

### Initialisation de l'arborescence

Maintenant, il reste à créer le site. Pour rappel, l'emplacement par défaut des fichiersconsituant le site doi se trouver dans src/site.

    $ mkdir -p src/site

Ce répertoire doit contenir un fichier site.xml décrivant la structure du site. Ci-dessous un exemple de contenu :

{% highlight xml %}
<project name="Exemple Site Markdown" xmlns="http://maven.apache.org/DECORATION/1.4.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://maven.apache.org/DECORATION/1.4.0 http://maven.apache.org/xsd/decoration-1.4.0.xsd">
  <!-- On utilise le skin Fluido qui permet d'utilise Bootstrap -->
  <skin>
    <groupId>org.apache.maven.skins</groupId>
    <artifactId>maven-fluido-skin</artifactId>
    <version>1.3.0</version>
  </skin>
  
   <body>
    <menu name="Exemple Markdown">
      <item href="page1.html" name="Première page" />
      <item href="page2.html" name="Seconde page" />
    </menu>

    <menu ref="reports" inherit="bottom" />
  </body>
  
</project>
{% endhighlight %}


### Ajouter du contenu

On crée ensuite le contenu, ici 2 fichiers Markdown (page1.md et page2.md) à placer dans le répertoire src/site/markdown.

Voici le contenu de page1.md :

{% highlight text %}
### Présentation

Ceci est un site écrit en Markdown et généré par Maven

* Premier Item
* Deuxième Item
* Troisième Item

### Une autre partie

On peut même faire des tableaux :

|Colonne 1|Colonne 2|Colonne 3|
| ------- | ------- | ------- |
|Lorem|Ipsum|Foo|
|Bar|1234|3.14|
{% endhighlight %}

Petite remarque sur le nommage des fichiers : au niveau du site.xml, il faut faire référence au fichier html (page1.html) et non au fichier markdown (page1.md).

### Générer le site

Il ne reste plus qu'à générer le site avec maven avec :

    $ mvn site

Le fichier index.html du site est généré dans le répertoire target/site. Cela devrait donner ça :

![Screenshot](/images/mvn-site/ExempleSiteMarkdown.png)

Il est également possible de lancer un Jetty avec Maven pour consulter en temps réel les modifications apportées aux pages du site :

    $ mvn site:run

Il suffit ensuite d'accéder au site à l'url [http://localhost:8080](http://localhost:8080)

Le code source de ce petit projet est disponible sur [Github](https://github.com/geraudster/demo-site-markdown)
