---
layout: default
title: Start Here 
group: "docs"
weight: 0
---

# Getting Started with Myria

This is the documentation for the Myria project. 

## Learning about the Myria stack

For a short overview of Myria as a cloud service and a big data management system, see our demo [paper](http://myria.cs.washington.edu/publications/Halperin_Myria_demo_SIGMOD_2014.pdf).


## Getting the source code 

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


## Using the Myria Service
Once you download the myria-stack, you can upload data to the Myria Service in order to start running queries. The Myria Service is currently hosted by the UWDB group's private cluster. 

### Part 1: Upload/Download Data
To upload data, this can be done through the [Python](myriapython.html) API. Look under the "Using Python with the Myria Service" section.

### Part 2: Running Queries on the Service
To start building queries once the data is uploaded, you can either write your queries directly through our [Myria Web Frontend](https://demo.myria.cs.washington.edu/editor), [Python](myriapython.html), or [IPython Notebook](https://github.com/uwescience/myria-python/blob/master/ipnb%20examples/myria%20examples.ipynb). To learn more about the Myria query language, check out the [MyriaQL](myriaql.html) page.


## Running the MyriaX execution engine

### Part 1: Setting up the Myria service
MyriaX is designed to run in a shared-nothing cluster. It consists of
a coordinator process and a set of worker processes. The coordinator receives query
plans in JSON through a REST api and gets the workers to
execute these query plans.

There are three ways to run MyriaX:

- Run MyriaX locally on a laptop or desktop. This is the easiest
way to get to experiment with MyriaX. This setup is not designed
to deliver high-performance. It should be thought of as an experimental
or debug mode. 

- Ryn MyriaX in an existing cluster.

The instructions to run MyriaX either locally or in an existing cluster are here:  [Running the MyriaX engine](myriaX.html). 

- Run MyriaX in a public cloud.

The instructions to deploy MyriaX on Amazon EC2 are here: [Running Myria on Amazon EC2](myria-ec2.html).

### Part 2: Running queries on the service
After you setup the engine, you can upload data and run queries through the [Python](myriapython.html) API under the "Using Python with your own Myria Deployment" section. An alternative way to run queries is via the [Myria Web](myriaweb.html) interface.

## Myria for developers

For those interested in developing with Myria, check out our [Myria Developer](developer.html) page. 

## FAQ

For any questions, see the [FAQ](faq.html). 
