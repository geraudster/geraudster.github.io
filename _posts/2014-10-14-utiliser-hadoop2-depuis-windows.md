---
layout: post
title: Utiliser Hadoop2 depuis Windows
tags: hadoop yarn mapreduce bigdata windows
comments: true
tracking: true
share: true
---

Il est possible d'utiliser Hadoop sous Windows à partir de la version 2.x, cependant, les binaires Windows n'étant
pas fournis, il est nécessaire de recompiler les sources d'Hadoop pour les obtenir. Ce post décrit comment faire.

## Problème

![Hadoop + Windows](/images/hadoop/hadoop-windows.png)

Si vous tentez d'exécuter des jobs mapreduce en local sur votre poste Windows, vous allez sans doute tomber sur ce genre 
d'erreur:

{% highlight java %}
[ERROR]: org.apache.hadoop.util.Shell - Failed to locate the winutils binary in the hadoop binary path
java.io.IOException: Could not locate executable null\bin\winutils.exe in the Hadoop binaries.
at org.apache.hadoop.util.Shell.getQualifiedBinPath(Shell.java:318)
at org.apache.hadoop.util.Shell.getWinUtilsPath(Shell.java:333)
at org.apache.hadoop.util.Shell.<clinit>(Shell.java:326)
at org.apache.hadoop.util.StringUtils.<clinit>(StringUtils.java:76)
at org.apache.hadoop.security.Groups.parseStaticMapping(Groups.java:93)
at org.apache.hadoop.security.Groups.<init>(Groups.java:77)
at org.apache.hadoop.security.Groups.getUserToGroupsMappingService(Groups.java:240)
at org.apache.hadoop.security.UserGroupInformation.initialize(UserGroupInformation.java:256)
at org.apache.hadoop.security.UserGroupInformation.ensureInitialized(UserGroupInformation.java:233)
at org.apache.hadoop.security.UserGroupInformation.isAuthenticationMethodEnabled(UserGroupInformation.java:310)
at org.apache.hadoop.security.UserGroupInformation.isSecurityEnabled(UserGroupInformation.java:304)
at org.apache.hadoop.ipc.RPC.getProtocolProxy(RPC.java:534)
at org.apache.hadoop.hdfs.NameNodeProxies.createNNProxyWithClientProtocol(NameNodeProxies.java:348)
at org.apache.hadoop.hdfs.NameNodeProxies.createNonHAProxy(NameNodeProxies.java:244)
at org.apache.hadoop.hdfs.NameNodeProxies.createProxy(NameNodeProxies.java:144)
at org.apache.hadoop.hdfs.DFSClient.<init>(DFSClient.java:579)
at org.apache.hadoop.hdfs.DFSClient.<init>(DFSClient.java:524)
at org.apache.hadoop.hdfs.DistributedFileSystem.initialize(DistributedFileSystem.java:146)
at org.apache.hadoop.fs.FileSystem.createFileSystem(FileSystem.java:2397)
at org.apache.hadoop.fs.FileSystem.access$200(FileSystem.java:89)
at org.apache.hadoop.fs.FileSystem$Cache.getInternal(FileSystem.java:2431)
at org.apache.hadoop.fs.FileSystem$Cache.get(FileSystem.java:2413)
at org.apache.hadoop.fs.FileSystem.get(FileSystem.java:368)
at org.apache.hadoop.fs.FileSystem$1.run(FileSystem.java:157)
at org.apache.hadoop.fs.FileSystem$1.run(FileSystem.java:154)
at java.security.AccessController.doPrivileged(Native Method)
at javax.security.auth.Subject.doAs(Unknown Source)
at org.apache.hadoop.security.UserGroupInformation.doAs(UserGroupInformation.java:1557)
at org.apache.hadoop.fs.FileSystem.get(FileSystem.java:154)
{% endhighlight %}

Cette exception indique que l'API Hadoop ne parvient pas à trouver le programme winutils.exe. Pour obtenir ce programme,
il va falloir recompiler...

## Pré-requis:

 * [Microsoft SDK](http://www.microsoft.com/en-us/download/details.aspx?id=8279)
 * [Protobuf 2.5](https://protobuf.googlecode.com/svn/rc/protoc-2.5.0-win32.zip), installé dans `c:\protob`
 * [Maven](http://maven.apache.org/download.cgi), installé dans `c:\mvn`
 * [JDK 1.7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html): installé dans `c:\jdk` (il ne doit pas y avoir d'espace dans le répertoire d'installation)
 * [Git-bash](http://git-scm.com/download/win), installé dans `c:\git-bash`
 * Pour les clients: [Microsoft Visual C++ Redistributable](http://www.microsoft.com/en-us/download/details.aspx?id=30679)


## Récupération et compilation des sources

Récupérer les sources depuis les sites [Apache](http://www.apache.org/dyn/closer.cgi/hadoop/common/) et les 
décompresser dans un répertoires dont le nom est court (pour éviter les problèmes de builds dus à des noms de
fichiers trop longs), idéalement un répertoire du genre `c:\hdp` fera l'affaire.

Lancer un prompt Microsoft SDK depuis le menu Démarrer, puis:

{% highlight bat %}
set JAVA_HOME=c:\jdk
set MAVEN_HOME=c:\mvn
set PROTOB_HOME=c:\protob
set SHELL_TOOLS=c:\git-bash
set Path=%Path%;%JAVA_HOME%\bin;%MAVEN_HOME%\bin;%PROTOB_HOME%\bin
set Platform=x64 // or set Platform=Win32 for 32 bits

mvn package  -Pdist,native-win -DskipTests -Dtar
{% endhighlight %}

Après quelques minutes, le build devrait se terminer. Vous pouvez alors récupérer
l'archive `hadoop-2.5.1.tar.gz` dans le répertoire `c:\hdp\hadoop-dist\target`. Cette archive
est semblable à celle fournie sur le site hadoop.apache.org, avec les binaires Windows
en plus.

## Installation du package

On suit la démarche habituelle:

* Décompression de l'archive
* Création de la variable d'environnement HADOOP_HOME pointant vers ce répertoire d'install


## Quelques liens

* Ticket Jira: [https://issues.apache.org/jira/browse/HADOOP-10051](https://issues.apache.org/jira/browse/HADOOP-10051)
* Doc d'install sous Windows: [https://wiki.apache.org/hadoop/Hadoop2OnWindows](https://wiki.apache.org/hadoop/Hadoop2OnWindows)


