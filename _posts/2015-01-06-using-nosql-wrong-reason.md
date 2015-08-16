---
layout: post
title: "Are you looking at NoSQL databases for the wrong reason?"
date: 2015-01-06T17:46
comments: true
sharing: true
categories:
 - technology
 - nosql
---
I was recently reading a report explaining that, in some circumstances, [PostgreSQL outperforms MongoDB](http://blogs.enterprisedb.com/2014/09/24/postgres-outperforms-mongodb-and-ushers-in-new-developer-reality/) in many key areas, and this made me think a bit about the rationales behind different choices among data storage options, especially related to - unfortunately frequent - naive comparisons between SQL and NoSQL solutions.

{% img center http://www.enterprisedb.com/sites/default/files/images/MongoDB_26_v_Postgres_94_Performance.png 460 358 'MongoDB vs PostgreSQL' %}

Besides the obvious fact that the above benchmark was created by EnterpriseDB, which is the commercial entity developing PostgreSQL (so it could be a little biased...), I'm already aware that PostgreSQL is an amazing product, to the point that I'd recommend it as one of the best solutions for most data storage problems one has to solve. Well known Enterprises with non-trivial performance needs are already [investing heavily on PostgreSQL](http://gotocon.com/berlin-2013/presentation/Why%20Zalando%20trusts%20in%20PostgreSQL).

My point of view, however, is a bit different: I'm asking to myself if most companies are looking at "NoSQL" solutions for the right reasons, and whether performance requirements are on top of their list. For example, let's have a look at the MongoDB entry in Wikipedia, as it's one of the databases I'm using more frequently these days:

> MongoDB is a cross-platform document-oriented database. Classified as a NoSQL database, MongoDB eschews the traditional table-based relational database structure in favor of JSON-like documents with dynamic schema (MongoDB calls the format BSON), making the integration of data in certain types of applications easier and faster.

I want to put some intentional emphasis on the sentence _in certain types of applications_ because this is exactly the point: you shouldn't adopt a document-oriented database, instead of a relational one, just because of performances, as this alone would be a silly motivation: a tuned SQL database can handle more than [14000 transactions per second](http://pgeoghegan.blogspot.it/2012/06/towards-14000-write-transactions-on-my.html), so if you beat this needs you are already playing in the "Big League" of companies with superior scaling requirements: congratulations!

Instead,

> a document database could be a better solution than a relational one when entities are mostly associated under a tree-like structure and a relational model would force to continuously create joins or to heavily denormalize relationships, beyond what would be rational. 

In case the data model respects the above constraints, then a document-oriented structure is capable of creating much less mismatch with an object-oriented design than a relational model. As we know, all non-trivial relational database schema create an amount of attrition with object models, the infamous [Object-relational impedance mismatch](http://en.wikipedia.org/wiki/Object-relational_impedance_mismatch) problem. Object-oriented systems are usually tree-like structures, which fit a document database much better than other models except for [graph databases](http://en.wikipedia.org/wiki/Graph_database), which obviously can implement the most general representation of a graph.

When moving out of the SQL domain, I'd always suggest to not underestimate that you and your team are loosing most of the battle-tested tools and expertise you have used almost automatically daily. I have seen too many struggling to extract even the most basic information from a NoSQL repository, something that could be done easily with a relational database, mostly because people is doing this since ages. Then, NoSQL databases often manage transactions very differently than "regular" relational database: do you know what it means to design applications with [Eventual Consistency](http://en.wikipedia.org/wiki/Eventual_consistency) and [Idempotent Services](http://soapatterns.org/design_patterns/idempotent_capability)?

I'm not saying you should not adopt new technologies, as I and my company [are working with many of them already](http://restheart.org), but my final suggestion is:

> adopt the data storage solution which best fits your domain model, and forget premature performance pseudo-optimizations: [you are probably trying to solve the wrong problem](http://www.azarask.in/blog/post/the-wrong-problem/).

