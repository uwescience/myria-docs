---
layout: default
title: Start Here 
group: "docs"
weight: 0
---

# Getting Started with Myria

This is the documentation for the Myria project. 

## Part 1: Learning about the Myria stack

For a short overall view of Myria as a cloud service and a big data management system, see our demo [paper](http://myria.cs.washington.edu/publications/Halperin_Myria_demo_SIGMOD_2014.pdf).


## Part 2:  Getting the source code 

Unless you are only interested in a specific component, the best place to 
start is with the [Myria Stack Repository](https://github.com/uwescience/myria-stack).

The Myria Stack repository is the umbrella repository that contains all the
components of the Myria stack as modules. To get all the source code, you
need to run the following commands:

git clone https://github.com/uwescience/myria-stack.git
cd myria-stack
git submodule init
git submodule update --recursive

Now you should see the Myria source code on your machine.


## Part 3: Run the Myria query execution engine, MyriaX


To get started with Myria, take a look at the [MyriaX](myriaX.html) engine. 


## Part 4: Execute queries

After you setup the engine, you can upload data and run queries through the [Python](myriapython.html) API. An alternative way to run queries is via the [Myria Web](myriaweb.html) interface. To learn more about the Myria query language, check out the [MyriaQL](myriaql.html) page. 


# Myria for developers

For those interested in developing with Myria, check out our [Myria Developer](developer.html) page. 

# FAQ

For any questions, see the [FAQ](faq.html) or contact us if you don't see your question listed. 
