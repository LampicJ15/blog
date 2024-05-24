---
layout: post
title: "Cypher vs Datalog"
date: 2021-11-21
---
Lately I have been getting into Clojure a bit and what better way to learn a new language than by using it. So I thought, what project should I take up that would alsoinclude my interest in graph technologies and query languages. And then Iremembered that I had actually already been briefly acquainted with Clojure. At work,we came across an interesting database called
XTDB a general purpose bitemporaldatabase written entirely in Clojure that uses Datalog as it’s main query language. Sothe idea for the post came to be – why don’t I just do a comparison of two languagescapable of eff iciently querying graphs & do a bit of programming in Clojure at the sametime.
We will compare two query languages:

- _Cypher_ a declerative graph query language that allows for expressive and eff icientdata querying in a property graph. With Cypher, use ASCII art to decribe the “shape”of the subgraph we are looking for in the data.
- _Datalog_ a declarative database query language with roots in logic programming.The idea for Datalog is to query data by providing a partial specification of rules orfacts, and then let the database find all instances of the specification implied bythese rules.

If you want to learn more about Cypher or Datalog, make sure to check out somesources provided at the end of the post.
We will run queries against the infamous Northwind dataset. It depicts a product salesystem – storing and tracking customers, products, customer orders, warehouse stock,shipping, suppliers, and even employees. Although the Northwind data set is oft enused to demonstrate SQL and relational databases, we can also structure the data as a graph. For our purposes, we will use a subset of the dataset (as shown in the figurebelow).


# Import to Neo4j

Neo4j stores data in a property graph, so we have to transform tables into nodes andrelationships. For this example it is pretty straight forward, we turn table rows intonodes and joins to relationships. We get the following graph data model.
If you want to learn more about how to import relational data into Neo4j I recommendto check out their off icial
tutorial.

# Import to XTDB

Importing data to XTDB is even easier since it is a schemaless document database.
We import every table row as a separate document (a document is a map fromkeywords to values). The database then automatically indexes top-level fields ofdocuments to enable eff icient joins and retrievals. 
Example of a document (in EDNformat) that represents one product:

In XTDB each document must contain the `:xt/id` key. For the sake of simplicity, we used random uuids.
Ok, so now that we have prepared our dataset, we can finally query it. Let’s start with an easy query, just to get the feel of both languages. We will explain it step-by-step (if you wish to run queries on your own, the source code is available
here).

# Queries
