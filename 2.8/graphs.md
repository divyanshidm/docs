---
layout: default
description: A Graph consists of vertices and edges
---
ArangoDB Graphs
---------------

### First Steps with Graphs

A Graph consists of *vertices* and *edges*. Edges are stored as documents in *edge collections*.
A vertex can be a document of a *document collection* or of an *edge collection* (so *edges* can be used as *vertices*).
Which collections are used within a named graph is defined via *edge definitions*.
A named graph can contain more than one *edge definition*, at least one is needed.
Graphs allow you to structure your models in line with your domain and group them logically in collections and giving you the power to query them in the same graph queries.

### Coming from a relational background - what is a graph?

In SQL you commonly have the construct of a relation table to store *n:m* relations between two data tables.
An *edge collection* is somewhat similar to these *relation tables*; *vertex collections* resemble the data tables with the objects to connect.
While simple graph queries with fixed number of hops via the relation table may be doable in SQL with several nested joins,
graph databases can handle an arbitrary number of these hops over edge collections - this is called *traversal*.
Also edges in one edge collection may point to several vertex collections.
Its common to have attributes attached to edges, i.e. a *label* naming this interconnection.
Edges have a direction, with their relations `_from` and `_to` pointing *from* one document *to* another document stored in vertex collections.
In queries you can define in which directions the edge relations may be followed (`OUTBOUND`: `_from` -> `_to`, `INBOUND`: `_to` -> `_from`, `ANY`: `_from` <-> `_to`).

### Named Graphs
Named graphs are completely managed by arangodb, and thus also [visible in the webinterface](web-interface.html#graphs-tab).
They use the ful spectrum of ArangoDBs graph features. You may access them via several interfaces.

 - [AQL Graph Operations](aql-graphs.html) with several flavors:
   - [AQL Graph traversals](aql-graph-traversals.html) on both named and anonymous graphs
   - [AQL operations on named Graphs](aql-graph-operations.html)
   - [AQL operations on raw vertex & edge collections](aql-graph-functions.html)
 - [The javascript General Graph implementation, as you may use it in FOXX Services](general-graphs.html)
    * [Graph Management](general-graphs-management.html); creating & manipualating graph definitions; inserting, updating and deleting vertices and edges into graphs
    * [Graph Functions](general-graphs-functions.html) for working with edges and vertices, to analyze them and their relations
    * [Fluent Query Interface](general-graphs-fluent-aqlinterface.html); if you like to work with graphs via a [fluent interface](https://en.wikipedia.org/wiki/Fluent_interface){:target="_blank"}
 - [the RESTful General Graph interface](http-gharial.html) used to implement graph management in client drivers

#### Manipulating collections of named graphs with regular document functions

The underlying collections of the named graphs are still accessible using the standard methods for collections.
However the graph module adds an additional layer on top of these collections giving you the following guarantees:

- All modifications are executed transactional
- If you delete a vertex all edges referring to this vertex will be deleted too
- If you insert an edge it is checked if the edge matches the *edge definitions*

Your edge collections will only contain valid edges and you will never have loose ends.
These guarantees are lost if you access the collections in any other way than the graph module,
so if you delete documents from your vertex collections directly, the edges pointing to them will be remain in place.

Existing inconsistencies in your data will not be corrected when you create a named graph.
Therefore, make sure you start with sound data as otherwise there could be dangling edges after all.
The graph module guarantees to not introduce new inconsistencies only.

### Anonymous graphs
Sometimes you may not need all the powers of named graphs, but some of its bits may be valuable to you.
You [may use AQL Graph Functions](aql-graph-functions.html) that are a little deeper explained in the [traversals](traversals.html) and the [Working with Edges](edges.html) Chapter.
Anonymous graphs don't have *edge definitions* describing which *vertex collection* is connected by which *edge collection*. The graph model has to be maintained in the client side code.
This gives you more freedom than the strict *named graphs*.

#### When to choose anonymous or named graphs?
As noted above, named graphs ensure graph integrity, both when inserting or removing edges or vertices.
So you won't encounter dangling edges, even if you use the same vertex collection in several named graphs.
This involves more operations inside the database which don't come for free.
Therefore anonymous graphs may be faster in many operations.
So this question boils down to 'Can I afford the additional effort or do I need the warranty for integrity?'. 

### Backup and restore
For sure you want to have backups of your graph data, you can use [ArangoDump](http-bulk-imports-arangodump.html) to create the backup,
and [ArangoRestore](http-bulk-imports-arangorestore.html) to restore a backup into a new ArangoDB. You should however note that:
- you need the system collection `_graphs` if you backup named graphs.
- you need to backup the complete set of all edge and vertex collections your graph consists of. Partial dump/restore may not work.

### Blueprint Graphs
*Blueprint 2 graphs* are a subset of *named graphs*. It only supports one edge and one vertex collection per graph. Both vertices and edges have to contain a label attribute; the Blueprints API defines a special `LABEL` attribute for this.
We offer [blueprints support via an api compatible java driver](https://github.com/arangodb/blueprints-arangodb-graph){:target="_blank"}.

### Example Graphs
ArangoDB comes with a set of easily graspable graphs that are used to demonstrate the APIs.
You can use the `add samles` tab in the `create graph` window in the webinterface, or load the module `@arangodb/graph-examples/example-graph` in arangosh and use it to create instances of these graphs in your ArangoDB.
Once you've created them, you can [inspect them in the webinterface](web-interface.html#graphs-tab) - which was used to create the pictures below.

#### The Knows\_Graph
A set of persons knowing each other:
![Persons relation Example Graph](images/knows_graph.png)

The *knows* graph consists of one *vertex collection* `persons` connected via one *edge collection* `knows`.
It will contain five persons *Alice*, *Bob*, *Charlie*, *Dave* and *Eve*.
We will have the following directed relations:
  - *Alice* knows *Bob*
  - *Bob* knows *Charlie*
  - *Bob* knows *Dave*
  - *Eve* knows *Alice*
  - *Eve* knows *Bob*

This is how we create it, inspect its *vertices* and *edges*, and drop it again:
{% arangoshexample examplevar="examplevar" script="script" result="result" %}    @startDocuBlockInline graph_create_knows_sample
    @EXAMPLE_ARANGOSH_OUTPUT{graph_create_knows_sample}
    var examples = require("org/arangodb/graph-examples/example-graph.js");
    var g = examples.loadGraph("knows_graph");
    db.persons.toArray()
    db.knows.toArray();
    examples.dropGraph("knows_graph");
    @END_EXAMPLE_ARANGOSH_OUTPUT
    @endDocuBlock graph_create_knows_sample
{% endarangoshexample %}
{% include arangoshexample.html id=examplevar script=script result=result %}

#### The Social Graph
A set of persons and their relations:

![Social Example Graph](images/social_graph.png)

This example has female and male persons as *vertices* in two *vertex collections* - `female` and `male`. The *edges* are their connections in the `relation` *edge collection*.
This is how we create it, inspect its *vertices* and *edges*, and drop it again:
{% arangoshexample examplevar="examplevar" script="script" result="result" %}
    @startDocuBlockInline graph_create_social_sample
    @EXAMPLE_ARANGOSH_OUTPUT{graph_create_social_sample}
    var examples = require("org/arangodb/graph-examples/example-graph.js");
    var graph = examples.loadGraph("social");
    db.female.toArray()
    db.male.toArray()
    db.relation.toArray()
    examples.dropGraph("social");
    @END_EXAMPLE_ARANGOSH_OUTPUT
    @endDocuBlock graph_create_social_sample
{% endarangoshexample %}
{% include arangoshexample.html id=examplevar script=script result=result %}

#### The City Graph
A set of european cities, and their fictional traveling distances as connections:

![Cities Example Graph](images/cities_graph.png)

The example has the cities as *vertices* in several *vertex collections* - `germanCity` and `frenchCity`. The *edges* are their interconnections in several *edge collections* `french / german / international Highway`. This is how we create it, inspect its *edges* and *vertices*, and drop it again:
{% arangoshexample examplevar="examplevar" script="script" result="result" %}
    @startDocuBlockInline graph_create_cities_sample
    @EXAMPLE_ARANGOSH_OUTPUT{graph_create_cities_sample}
    var examples = require("org/arangodb/graph-examples/example-graph.js");
    var g = examples.loadGraph("routeplanner");
    db.frenchCity.toArray();
    db.germanCity.toArray();
    db.germanHighway.toArray();
    db.frenchHighway.toArray();
    db.internationalHighway.toArray();
    examples.dropGraph("routeplanner");
    @END_EXAMPLE_ARANGOSH_RUN
    @endDocuBlock graph_create_cities_sample
{% endarangoshexample %}
{% include arangoshexample.html id=examplevar script=script result=result %}
#### The Traversal Graph
This graph was designed to demonstrate filters in traversals. It has some labels to filter on it.

![traversal graph](images/traversal_graph.png)

The example has all its vertices in the *circles* collection, and an *edges* edge collection to connect them.
Circles have uniq numeric labels. Edges have two boolean attributes (*theFalse* always being false, *theTruth* always being true) and a label sorting *B* - *D* to the left side, *G* - *K* to the right side. Left and right side split into Paths - at *B* and *G* which are each direct neighbours of the root-node *A*. Starting from *A* the graph has a depth of 3 on all its paths.
{% arangoshexample examplevar="examplevar" script="script" result="result" %}
    @startDocuBlockInline graph_create_traversal_sample
    @EXAMPLE_ARANGOSH_OUTPUT{graph_create_traversal_sample}
    var examples = require("org/arangodb/graph-examples/example-graph.js");
    var g = examples.loadGraph("traversalGraph");
    db.circles.toArray();
    db.edges.toArray();
    examples.dropGraph("traversalGraph");
    @END_EXAMPLE_ARANGOSH_RUN
    @endDocuBlock graph_create_traversal_sample
{% endarangoshexample %}
{% include arangoshexample.html id=examplevar script=script result=result %}
#### The World Graph
The world contry graph structures its nodes like that: world -> continet -> country -> capital. In some cases edge directions aren't forward (therefore it will be displayed disjunct in the graph viewer). It has two ways of creating it. One using the named graph utilities (*worldCountry*), one without (*worldCountryUnManaged*). 
It is used to demonstrate raw traversal operations.
{% arangoshexample examplevar="examplevar" script="script" result="result" %}
    @startDocuBlockInline graph_create_world_sample
    @EXAMPLE_ARANGOSH_OUTPUT{graph_create_world_sample}
    var examples = require("org/arangodb/graph-examples/example-graph.js");
    var g = examples.loadGraph("worldCountry");
    db.worldVertices.toArray();
    db.worldEdges.toArray();
    examples.dropGraph("worldCountry");
    var g = examples.loadGraph("worldCountryUnManaged");
    examples.dropGraph("worldCountryUnManaged");
    @END_EXAMPLE_ARANGOSH_RUN
    @endDocuBlock graph_create_world_sample
{% endarangoshexample %}
{% include arangoshexample.html id=examplevar script=script result=result %}

### Cookbook examples
The above referenced chapters describe the various APIs of ArangoDBs graph engine with small examples. Our cookbook has some more real life examples:

 - [Traversing a graph in full depth](https://docs.arangodb.com/cookbook/Graph/FulldepthTraversal.html){:target="_blank"}
 - [Search for vertices of special type connecting a given subgraph](https://docs.arangodb.com/cookbook/Graph/FindingConnectedVerticesForSubgraphs.html){:target="_blank"}
 - [Using an example vertex with the java driver](https://docs.arangodb.com/cookbook/Graph/JavaDriverGraphExampleVertex.html){:target="_blank"}
 - [Retrieving documents from ArangoDB without knowing the structure](https://docs.arangodb.com/cookbook/Graph/JavaDriverBaseDocument.html){:target="_blank"}
 - [Using a custom visitor from node.js](https://docs.arangodb.com/cookbook/Graph/CustomVisitorFromNodeJs.html){:target="_blank"}
 - [AQL Example Queries on an Actors and Movies Database](https://docs.arangodb.com/cookbook/Graph/ExampleActorsAndMovies.html){:target="_blank"}

### Higher volume graph examples
All of the above examples are rather small so they are easy cromprehensible and can demonstrate the way the functionality works. There are however several datasets freely available on the web that are a lot bigger. [We collected some of them with import scripts](https://github.com/triAGENS/ArangoDB-Data/){:target="_blank"} so you may play around with them. Another huge graph is the [Pokec social network](https://snap.stanford.edu/data/soc-pokec.html){:target="_blank"} from Slovakia that we [used for performance testing on several databases](https://www.arangodb.com/2015/06/multi-model-benchmark/){:target="_blank"}; You will find importing scripts etc. in this blogpost.
