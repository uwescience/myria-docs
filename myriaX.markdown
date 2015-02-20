---
layout: default
title: MyriaX
group: "docs"
weight: 1
---

# MyriaX Engine

## Local Installation

### 0. Preparation

#### Myria needs Java 7
Make sure `java -version` shows `7` on your machine.

If not (e.g., UW CSE support-managed machines have Java 6), you can put Java 7 in your directory, and let your `PATH` include it BEFORE the default `PATH`:

    export PATH=(path to java7 bin folder):$PATH

#### Passwordless SSH

You need to be able to do `ssh localhost` without typing in password.

- Start SSH Server on your machine to enable remote login. Instructions
for how to do this can be found here: http://osxdaily.com/2011/09/30/remote-login-ssh-server-mac-os-x/

- Setting up keys. If you have not setup keys before, the easiest way to do it is as follows:

    `ssh-keygen`

    `ssh-copy-id username@localhost`

Use default settings. You may need to install `ssh-copy-id` if you don't have it on your machine.
Instructions for setting up keys without installing ssh-copy-id can be found here: http://osxdaily.com/2012/05/25/how-to-set-up-a-password-less-ssh-login/

To test, run `ssh localhost`.

#### Download Myria
We suggest you to use `git` because you may want to switch between branches later, although GitHub also offers a ZIP file of the master branch.
To do that, install `git` and run `git clone https://github.com/uwescience/myria`,
which creates a directory `myria` with the master branch inside.

#### Build the Myria jar
Get into the `myria` directory, run `./gradlew jar`.

Note: if it is not a fresh installation (e.g. you just switched from another branch),
you may need to run `./gradlew clean` before `./gradlew jar`. This is for cleaning different versions of Java libraries.

If succeeded, you should be able to see jars in `build/libs` including `myria-0.1.jar`.

### 1. Setting up a local deployment
Go to `myriadeploy`.

#### Deployment configuration file (and Postgres setting up)
There are some example configure files,
we use `deployment.cfg.local` as a starting point for local installation.

Make a copy:

    cp deployment.cfg.local deployment.cfg

And make the changes you want to `deployment.cfg`.

`deployment.cfg.local` uses SQLite as the storage backend. If you want to use [PostgreSQL](www.postgresql.org),
`deployment.cfg.postgres` show how to set up the configurations.
In addition, take these steps:

- Install postgres

- Myria connects to postgres through port 5401 rather than the default. Change the port on which Postgres listens in the `postgresql.conf` file, and make sure Postgres is restarted.

- Create a `uwdb` role which Myria will use to manage the tables stored in Postgres.

    `create role uwdb with superuser;`

    `alter role uwdb with login;`

- Create Postgres databases. Be careful that if you have multiple workers on the same machine, they need to use different Postgres databases.
For example, the configuration in `deployment.cfg.postgres` needs the Postgres databases `myria1` and `myria2` on both worker machines:

    `createdb myria1; createdb myria2;`

#### Setup the working directories and catalogs and copy to nodes

Use this script if you want to initialize (or re-initialize) a new Myria cluster configuration.

    ./setup_cluster.py <deployment.cfg>

This will: Create a directory called `<description>` with all the catalog files, then dispatch them to corresponding working directories.

Notice this **overwrites** the cluster: no ingested relations in previous Myria instances will be inherited by this new one.

### 2. Running the cluster

#### Launch the cluster

    ./launch_cluster.sh <deployment.cfg>

#### Check the cluster status

A. Query which workers the master knows about. They better match what's in `deployment.cfg`!

    curl -i localhost:8753/workers

B. Query which workers are alive. 

    curl -i localhost:8753/workers/alive

#### Start using the cluster

Here we use eamples in the directory `jsonQueries/getting_started`.
There are more in `jsonQueries`, check them.

A. Ingest some data.

    curl -i -XPOST localhost:8753/dataset -H "Content-type: application/json"  -d @./ingest_smallTable.json

You may need to change the path to your source data file.

B. Run a query.

    curl -i -XPOST localhost:8753/query -H "Content-type: application/json"  -d @./global_join.json

This query writes result back to the backend storage. You should be able to find the result tables in your databases. The table name is specified in the `DbInsert` operator, change it if you want.

#### Shutdown the cluster

A. Shutdown the whole cluster via the REST API:

    curl -i localhost:8753/server/shutdown

This will shutdown everything, including the master and all the workers.

B. If there was an error in any query, ingesting data, etc., then the cluster might freeze and most commands that involve workers (e.g., queries, ingestion, and shutdown) would not work. You can force-quit all machines:

    ./stop_all_by_force <deployment.cfg>

This will go to all the nodes, find the master/worker processes under your username, and kill them.

## Cluster Installation

Specify your cluster configurations, including machine names, port numbers, working directories, database names, etc, in your `deployment.cfg` file. 

Similar to local installation, make sure you have: Java 7, passwordless SSH from the master machine(s) to all the worker machine(s), Postgres users and databases created on your worker machine(s) on your cluster. 

Now you should be able to go!

