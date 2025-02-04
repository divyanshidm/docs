---
layout: default
description: Welcome to the ArangoDB documentation!
page-toc:
  disable: true
---
ArangoDB {{ site.data.versions[page.version.name] }} Documentation
=====================================

Welcome to the ArangoDB documentation!

New and eager to try out ArangoDB? Start right away with our beginner's guide:
[Getting Started](getting-started.html)

The documentation is organized in four handbooks:

- This manual describes ArangoDB and its features in detail for you as a user,
  developer and administrator.
- The [AQL handbook](../aql/) explains ArangoDB's query language AQL.
- The [HTTP handbook](../http/) describes the internal API of ArangoDB
  that is used to communicate with clients. In general, the HTTP handbook will be
  of interest to driver developers. If you use any of the existing drivers for
  the language of your choice, you can skip this handbook.
- Our [cookbook](../cookbook/) with recipes for specific problems and
  solutions.

Features are illustrated with interactive usage examples; you can cut'n'paste them
into [arangosh](administration-arangosh.html) to try them out. The HTTP
[REST-API](../http/) for driver developers is demonstrated with cut'n'paste
recepies intended to be used with the [cURL](http://curl.haxx.se){:target="_blank"}. Drivers may provide
their own examples based on these .js based examples to improve understandeability
for their respective users, i.e. for the [java driver](https://github.com/arangodb/arangodb-java-driver#learn-more){:target="_blank"}
some of the samples are re-implemented.

### Overview

ArangoDB is a multi-model, open-source database with flexible data models for documents, graphs, and key-values. Build high performance applications using a convenient SQL-like query language or JavaScript extensions. Use ACID transactions if you require them. Scale horizontally and vertically with a few mouse clicks.

Key features include:

* installing ArangoDB on a [**cluster**](deployment.html) is as easy as installing an app on your mobile
* [**Flexible data modeling**](data-modeling.html): model your data as combination of key-value pairs, documents or graphs - perfect for social relations
* [**Powerful query language**](../aql/) (AQL) to retrieve and modify data 
* Use ArangoDB as an [**application server**](foxx.html) and fuse your application and database together for maximal throughput
* [**Transactions**](transactions.html): run queries on multiple documents or collections with optional transactional consistency and isolation
* [**Replication** and **Sharding**](administration.html): set up the database in a master-slave configuration or spread bigger datasets across multiple servers
* Configurable **durability**: let the application decide if it needs more durability or more performance
* No-nonsense storage: ArangoDB uses all of the power of **modern storage hardware**, like SSD and large caches
* JavaScript for all: **no language zoo**, you can use one language from your browser to your back-end
* It is **open source** (Apache License 2.0)

### Community

If you have questions regarding ArangoDB, Foxx, drivers, or this documentation don't hesitate to contact us on:

- [GitHub](https://github.com/arangodb/arangodb/issues){:target="_blank"} for issues and misbehavior or [pull requests](https://www.arangodb.com/community/){:target="_blank"}
- [Google Groups](https://groups.google.com/forum/?hl=de#!forum/arangodb){:target="_blank"} for discussions about ArangoDB in general or to announce your new Foxx App
- [StackOverflow](http://stackoverflow.com/questions/tagged/arangodb){:target="_blank"} for questions about AQL, usage scenarios etc.
- [Slack](http://slack.arangodb.com){:target="_blank"}, our community chat

When reporting issues, please describe:

- the environment you run ArangoDB in
- the ArangoDB version you use
- whether you're using Foxx
- the client you're using
- which parts of the documentation you're working with (link)
- what you expect to happen
- what is actually happening

We will respond as soon as possible.
