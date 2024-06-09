---
layout: post
title: "Intro to Kùzu"
date: 2024-06-09  
---

# Introduction to Kùzu DB

I am very interested in the world of knowledge graphs and graph-related technology and have been working with Neo4j for
the last couple of years.
Recently, I finally got some time to start researching a new graph database that seems very intriguing. From the looks
of it, Kùzu DB has all the features of a competent graph database management system (GDBMS).

(If you are curious about what constitutes a competent graph database, I highly recommend checking out
this presentation [_The (sorry) State of Graph Database Systems_](https://homepages.cwi.nl/~boncz/edbt2022.pdf) by Peter
Boncz)

## What is Kùzu?

In the words from their website:
> "Kùzu is an embedded graph database built for query speed and scalability. It is optimized for handling complex
> join-heavy analytical workloads on very large graphs"

Source: https://blog.kuzudb.com/post/meet-kuzu/

The team developing Kùzu is inspired by how DuckDB revolutionized tabular data science and aims to achieve the same in
graph data science.

What excites me the most is their new join algorithms, particularly the Worst-case Optimal Join Algorithms. To
understand more about these, check out their [post](https://blog.kuzudb.com/post/wcoj/) on the topic; it is very
informative.

## Quick Start Guide: Setting Up Neo4j and Kùzu

This post is intended to provide quick instructions for setting up Kùzu DB, so you can try it out easily and compare it
with Neo4j.

For the dataset, I chose the [Crime Investigation Graph dataset](https://github.com/neo4j-graph-examples/pole), which is
available as a sandbox example in Neo4j.

![dataset schema](/blog/docs/assets/images/crime_investigation_schema.png)

To set up a local Neo4j instance and Kùzu DB, you can clone my
repository [graph-playground](https://github.com/LampicJ15/graph-playground), where the entire code and data are already
prepared.

### Setting Up Neo4j

To set up Neo4j with the dataset, first run `make run-docker` from the repository root to start the Neo4j Docker
container. When Neo4j is running, you can log in with the credentials provided in the repository's README.

After logging into Neo4j's browser, copy the content of the `playground/crime-investigation/neo4j_import.cypher` file
and execute the Cypher script to load the dataset.

### Setting Up Kùzu

To set up Kùzu, run the following Python script: `playground/crime-investigation/kuzu_db.py`. This script will create
Kùzu's database folder and import the dataset.

You can then explore the dataset with Kùzu's database explorer by running:

```bash
docker run -p 8000:8000 \
           -v ./playground/crime-investigation/db:/database \
           --rm kuzudb/explorer:0.4.2
```

You can then access the query interface on localhost port `8000` and perform queries against the dataset.
A set of example queries is provided in the `playground/crime-investigation/queries.cypher` file.
