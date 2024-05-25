---
layout: post
title: "Neo4j and Kevin Bacon"
date: 2021-02-04
---

# Neo4j and Kevin Bacon

Hooray, finally my first blog post. As most of my blogs will be about graphs and graph related technology (especially about Neo4j), 
this one is no exception. In this post we will take a brief look at:

* What is a Graph Database?
* What is Neo4j?
* What is Cypher and how can it make our life easier?
* What does Kevin Bacon have to do with graphs?

So lets jump into it. I will try not to overcomplicate things as we will get more familiar with Neo4j and graph databases in the upcoming blogs.

## What is a Graph Database?

>Very simply, a graph database is a database designed to treat the relationships between data as equally important to the data itself. 
> It is intended to hold data without constricting it to a pre-defined model. Instead, the data is stored like we first draw it out – showing how each individual entity connects with or is related to others.

Source: https://neo4j.com/developer/graph-database/#why-graphdb

## What is a Property Graph Model?

> As with most technologies, there are few different approaches to what makes up the key components of a graph database. 
> One such approach is the property graph model,where data is organized as nodes, relationships, and properties 
> (data stored on the nodes or relationships).

Source: https://neo4j.com/developer/graph-database/#property-graph

**_Nodes_** are the entities in the graph. They can hold any number of attributes (key-value
pairs) called _properties_. Nodes can be tagged with _labels_ , representing their different roles in a domain.

**_Relationships_** provide directed, named, semantically-relevant connections between
two node entities (e.g. Employee _WORKS_FOR_ Company). A relationship always has a
direction, a type, a start node, and an end node. Like nodes, relationships can also
have properties.

Below is an example of a property graph model. Cool right, looks just like something
you could draw up on a whiteboard.

![property_graph_model_example.png](..%2Fdocs%2Fassets%2Fimages%2Fproperty_graph_model_example.png)

Let's go through the above Property Graph Model together. The model has 2 nodes
tagged with label _Person. Person_ nodes also have properties _name_ and _age._ There is
one node labeled _Book_ with properties _title_ and _authors_. The model has 3 relationships
connecting these nodes. Two relationships are of type _HAS_READ_ and on of type
_IS_FRIENDS_WITH_. Relationships also contain properties.

Now, a domain explanation of the model. We have two persons, John and Sally, who
have been friends since 01/09/2013. They both read the book Graph Databases by Jim
Webber and Ian Robinson, but on different dates. Pretty cool right, the model itself is
very readable and self-explanatory. Now try to imagine modeling this using tables, not
as pretty right.

## What is Neo4j?

Neo4j is an open-source, NoSQL, native graph database that provides an ACID-compliant transactional back end for your applications. Neo4j is referred to as a native
graph database because it efficiently implements the property graph model down to
the storage level. What this means is that the Graph Property Model is stored in the
Database as it was designed on the white board.

One of the main reasons Neo4j is so popular among developers is **Cypher**. Cypher is
Neo4jʼ s graph query language that allows users to store and retrieve data from the
graph database. Cypher is based on ASCII art, so the syntax is easily understood and
makes queries more self-explanatory.
We will get to know Cypher later in the article through various examples.


OK enough with the theory lets get our hands dirty and try out Neo4j for real. To follow
along you can simply clone the [GitHub repository](https://github.com/LampicJ15/neo4j-movies), perform
the steps under _Environment Setup_ and _Data Import_ and then follow along with the post.

## Movies Data set

To see Neo4j in action, we first have to populate it with data. For this example we will
use data about movies and people that helped create them. There is an awesome data
set available on Keagle with information on more than 350 000 movies from The Movie
Database. The data set contains 350 000 movies up from end of 19th century to august
2017 and for each movie information about the casting & crew.

We will import two CSV tables:

AllMoviesDetailsCleaned.csv [127.7MB] containing information about movies from _The Movie Database_:
- movie title
- language
- movies budget
- genre
- popularity
- production company
- the country where it was produced

AllMoviesCastingRaw.csv [33.78MB] contains data about movies main casting crew (5 first billed actors) & crew members (producer, writer, editor and director).


However, since this data is in CSV format, it does not benefit us too much :).
With a little magic, we import the data into the database and transform it into the
following Graph Property Model.

![movies_schema.png](..%2Fdocs%2Fassets%2Fimages%2Fmovies_schema.png)

As seen from the graph everything is centered around movies.
We have _Movie_ nodes with 7 different types of outgoing relationships _– RATED,
OF_GENRE, HAD, SPOKEN_IN, LASTS, RELEASED_ON, PRODUCED_IN_ providing information on:

* movie's rating,
* movie genre,
* what was the budget of the movie,
* language of the movie,
* what is the runtime of the movie,
* when was it released
* and in which country it was produced.

A _Movie_ node also has incoming relationships originating from the _Company_ and
_Person_ nodes. From the _Company_ node there is the _PRODUCED_ relationship indicating
which company produced the given movie. _Person_ nodes are however connected to
the _Movie_ node with different types of relationships depending on their role in the
making of the movie. A person can be an actor, director, producer, screenplay or editor
therefore different types of relationships belong to them ( _ACTED_IN, PRODUCED,
DIRECTED, WROTE, EDITED_ ).

You might be asking yourself why do we need so many nodes and relationships when
modeling such data. I will try to answer this with the following examples, where we will
get to know the basics of Cypher query language.

Cypher language is purpose built for working with graph data which uses patterns to
describe graph data. The main idea of the Cypher language is to describe queries with
patterns that illustrate the graph, i.e. nodes and relationships. You can imagine Cypher
as if you wanted to draw a Property Graph Model using ASCII characters.

For instance, nodes are represented with parentheses around the attributes and
information regarding the entity.

```cypher
// node
(variable:Label {propertyKey: 'propertyValue'})
// example
(movie:Movie {title: 'Forrest Gump'})
```
Relationships are depicted with an arrow (either directed or undirected) with the
relationship type in brackets.


relationship type in brackets.

```cypher
// relationship
-[variable:RELATIONSHIP_TYPE]->
// example
-[acted:ACTED_IN]->
```
Combing nodes and relationship we get a Cypher pattern.

```cypher
// Cypher pattern
(node1:LabelA)-[rel1:RELATIONSHIP_TYPE]->(node2:LabelB)
// example
(actor:Person)-[acted:ACTED_IN]->(movie:Movie)
```
By the looks of it not so hard right. It is basically as if were drawing the Graph with
ASCII art and doing so defining which parts of the graph we want to traverse.

Now to our very first Cypher query. Let's find the node representing the movie _Forrest
Gump_.

```
MATCH (movie:Movie)
WHERE movie.title = 'Forrest Gump'
RETURN movie
```
```
MATCH (movie:Movie)
a clause to specify that we want to find a node with label Movie which will assign
matches to the variable movie
WHERE movie.title = 'Forrest Gump'
a clause to filter the Movie nodes to select only those with the title property equal to
value “Forrest Gump”
RETURN
a clause used to specify the results of the query
```
If you are familiar with SQL this should be pretty straight forward.
If we now run the query in Neo4j Browser, it will return the node representing the
desired movie.


Results of the query for finding the _Movie_ node with title ʻForrest Gumpʼ displayed in
Neo4j browser.

Now that we have seen the basic example of a Cypher query letʼs step it up a bit by
trying to find the answers to the following questions:

1. _In which movies did Christian Bale act in?_
2. _Which are the top 3 longest movies?_
3. _Which are the top 10 rated comedies?_
4. _Which directors most oen worked with Leonardo DiCaprio_?

To answer the first question we have to find all of the movies that Christian Bale acted
in. We do this with the following query that returns 46 movies.

```
MATCH (:Person {name:'Christian Bale'})-[:ACTED_IN]->(movie:Movie)
RETURN movie.title
```
For the second one, let's find the 3 longest movies, where I would probably fall asleep
watching them.

```
MATCH (movie:Movie)-[:LASTS]->(runtime:Runtime)
RETURN movie.title, runtime.value
ORDER BY runtime.value DESC
LIMIT 3
```

By performing the query we get that the movie Logistics made by Erika Magnusson and Daniel Andersson is the longest. With 51 420 minutes (
hours or 35 days and 17 hours) this is the longest movie ever made. Now this is a movie
I would not try to bother watching even during quarantine. Following it is the movie
Ambiancé with a runtime of 43 200 minutes and in third place with 14 400 minutes the
movie Modern Times Forever.

If you like comedies this will be interesting to you. Letʼs find the 10 top rated comedies
according to their ratings. Let's find the movie titles and their ratings.

```cypher
MATCH (movie:Movie)-[:OF_GENRE]->(:Genre {name:'Comedy'}),
(movie)-[:RATED]->(rating:Rating)
RETURN movie.title AS title, rating.value AS ratings
ORDER BY rating.value DESC
LIMIT 10
```
For the final question, let's find out which directors collaborated with Leonardo DiCaprio the most.

```
MATCH (:Person {name:"Leonardo DiCaprio"})-[:ACTED_IN]->
(movie:Movie)<-[:DIRECTED]-(director:Person)
RETURN director.name, count(director) AS collabs,
collect(movie.title) AS movies
ORDER BY collabs DESC
```
From the results we see that Leonardo DiCaprio has collaborated with Martin Scorsese the most times (8 times).

## Kevin Bacon and Graphs

We are now almost at the end and the only thing that we have to address is “ _What does
Kevin Bacon have to do with graphs?_“.

In graph theory there is a classic problem (known as _Six Degrees of Kevin Bacon_ ) where
the objective is to find the shortest path between a given actor and Kevin Bacon where
an intermediary connection can only be made between actors who have appeared
together in a movie. For example, Stana Katic was in _Stiletto_ with Kelly Hu, who was
in _The Air I Breathe_ with Kevin Bacon. While there is only one person with Bacon
number 0 (Kevin Bacon himself), most individuals involved in the Hollywood film
industry have a Bacon number of 6 or less.

Neo4j support functionality to find the shortest path between two nodes out of the box.

Let's find the Bacon path to Jim Carrey. When we execute the following query in Neo4j browser

```
MATCH (KevinB:Person { name: 'Kevin Bacon' }), (otherActor:Person
{ name: 'Jim Carrey' }),
p = shortestPath((KevinB)-[:ACTED_IN*]-(otherActor))
RETURN p
```
we get the whole path from Kevin Bacon to Jim Carrey.


We see that they have never acted in the same movie together but are connected
through Gary Oldman. Gary Oldman acted in the movie _Murder in the First_ with Kevin
Bacon and in the movie _A Christmas Carol_ with Jim Carrey. Therefore, Jim Carey has a
Bacon number of 2.

Since I am from Slovenia I wanted to find out the Bacon path to some known Slovenian
actors like Jurij Zrnec.

```
MATCH (KevinB:Person { name: 'Kevin Bacon' }), (otherActor:Person
{ name: 'Jurij Zrnec' }),
p = shortestPath((KevinB)-[:ACTED_IN*]-(otherActor))
RETURN p
```
From the returned graph we can see that Jurij Zrnec is not as “closely” connected to
Kevin Bacon and has a Bacon number of 4.
I challenge you to find an actor that has a Bacon number greater than 4 (it is not easy).

Finally, we've come to the end of the first blog. Maybe it was a bit long, so I'll try to be
shorter in the next ones. I hope you found the content interesting and of course if there
are any questions please let me know.