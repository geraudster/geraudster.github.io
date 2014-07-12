---
layout: post
title: Efficient import in Neo4j with Java API
tags: data neo4j
comments: true
tracking: true
share: true
---

During a preparation of a training on Neo4j, I worked on data import in Neo4j.

The objective was to import datasets from [Movielens](http://grouplens.org/datasets/movielens/). Grouplens provides datasets of differents size (10k, 1M and 10M) of ratings. Datasets consists of relationships between users and movies.
This is a bipartite graph as there are no relations between users or between movies. So there are more relationships than nodes.

The first try was to import the 10k dataset, it was quite easy and no problem were encountered. I've just followed the [Neo4j doc](http://docs.neo4j.org/chunked/stable/).
At the moment, I was working on Neo4j 2.0.0, java 1.7 (with default settings). The tool I developed performs the import following the steps :

* Delete previous data
* Import users
* Import movies
* Import relations

Transaction is committed every 10'000 elements.
I also maintain a map of users and movies, this map is used during relationships creation.

## First try to import 1M

Here the transaction is committed every 100'000 elements.
First launch gives this:

    100000 ratings imported (elapsed: 8905 ms)
    200000 ratings imported (elapsed: 6617 ms)
    300000 ratings imported (elapsed: 8163 ms)
    400000 ratings imported (elapsed: 8785 ms)
    500000 ratings imported (elapsed: 28027 ms)

And nothing more... the JVM is stucked (but my CPU is at 100%). That's not what I've expected...
Let's tune the jvm options with -Xms2g -Xmx2g :

    100000 ratings imported (elapsed: 4610 ms)
    200000 ratings imported (elapsed: 3171 ms)
    300000 ratings imported (elapsed: 4120 ms)
    400000 ratings imported (elapsed: 3806 ms)
    500000 ratings imported (elapsed: 3627 ms)
    600000 ratings imported (elapsed: 22318 ms)
    700000 ratings imported (elapsed: 59417 ms)

It's going a little bit further, but not very satisfying.

## Second try

I know that Neo4j can do much better, and the solution was [BatchInserters](http://docs.neo4j.org/chunked/stable/batchinsert-db.html).
BatchInserters is a factory that provides GraphDatabaseService implementation, so you can use it without changing your code (or at least the
DatabaseService creation). So instead of:

{% highlight java %}
GraphDatabaseService graphDB = new GraphDatabaseFactory().newEmbeddedDatabaseBuilder(dbPath).newGraphDatabase();
{% endhighlight %}

You just have to do this:

{% highlight java %}
GraphDatabaseService graphDB = BatchInserters.batchDatabase(dbPath);
{% endhighlight %}

And indeed, it was much better:

    100000 ratings imported (elapsed: 2158 ms)
    200000 ratings imported (elapsed: 1190 ms)
    300000 ratings imported (elapsed: 1230 ms)
    400000 ratings imported (elapsed: 1216 ms)
    500000 ratings imported (elapsed: 1184 ms)
    600000 ratings imported (elapsed: 1173 ms)
    700000 ratings imported (elapsed: 1185 ms)
    800000 ratings imported (elapsed: 1166 ms)
    900000 ratings imported (elapsed: 1193 ms)
    1000000 ratings imported (elapsed: 1170 ms)
    Import done in 14980 ms

Ok, now it's far better :) Let's check in the server if all is here.
First query, we count the number of ratings :

![Query 1](/images/neo4j/query1.png)

Next, let's find similar ratings of user 2433 :

![Query 2](/images/neo4j/query2.png)

## Get even more data !!!

So we can now try to import the full dataset (10M ratings). Let's go :

    100000 ratings imported (elapsed: 2263 ms)
    200000 ratings imported (elapsed: 1323 ms)
    300000 ratings imported (elapsed: 1313 ms)
    400000 ratings imported (elapsed: 1294 ms)
    500000 ratings imported (elapsed: 1288 ms)
    600000 ratings imported (elapsed: 1259 ms)
    700000 ratings imported (elapsed: 1262 ms)
    800000 ratings imported (elapsed: 1252 ms)
    900000 ratings imported (elapsed: 1249 ms)
    1000000 ratings imported (elapsed: 1217 ms)
    1100000 ratings imported (elapsed: 1239 ms)
    1200000 ratings imported (elapsed: 1225 ms)
    1300000 ratings imported (elapsed: 1254 ms)
    ...
    9800000 ratings imported (elapsed: 1301 ms)
    9900000 ratings imported (elapsed: 1409 ms)
    10000000 ratings imported (elapsed: 4095 ms)
    Import done in 177970 ms

Nice !!

## To conclude

Using Java API to import data can be very useful when you cannot (or don't want) to generate your own Cypher script or use CSV import.
The Batch Graph Database is very good for large data import in Neo4j, but beware that during a batch import, no one else can
use the database (DB must be shut down and import tool must be single threaded).

For my use case, it did the trick, and give amazing results.
![Amazing!](/images/neo4j/spongebob.jpg)