---
layout: post
title: Utiliser Hadoop2 depuis Windows
tags: hadoop yarn mapreduce bigdata windows
comments: true
tracking: true
share: true
---

## Pré-requis:

 * Microsoft SDK
 * Protobuf 2.5, dans c:/protob
 * Maven, dans c:/mvn
 * JDK 1.7: dans c:/jdk
 * Cygwin ou Git-bash
 * Pour les clients: Microsoft Visual C++ Redistributable


## Récupération et compilation des sources

Lancer un prompt Microsoft SDK, puis:

{% highlight console %}
set JAVA_HOME=c:\jdk
set MAVEN_HOME=c:\mvn
set PROTOB_HOME=c:\protob
set SHELL_TOOLS=c:\git-bash
set Path=%Path%;%JAVA_HOME%\bin;%MAVEN_HOME%\bin;%PROTOB_HOME%\bin
set Platform=x64 // or set Platform=Win32 for 32 bits

mvn package  -Pdist,native-win -DskipTests -Dtar
{% endhighlight %}
