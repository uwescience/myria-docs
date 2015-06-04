---
layout: default
title: MyriaX
group: "docs"
weight: 1
---

# MyriaX Engine

## Local Installation

### 1. Preparation

#### Myria needs Java 7
Make sure `java -version` shows `7` on your machine.

If not (e.g., UW CSE support-managed machines have Java 6), you can put Java 7 in your directory, and let your `PATH` include it BEFORE the default `PATH`:

    export PATH=(path to java7 bin folder):$PATH

#### Passwordless SSH

You need to be able to do `ssh localhost` without typing your password.

- Start SSH Server on your machine to enable remote login. Instructions
for how to do this can be found [here](http://osxdaily.com/2011/09/30/remote-login-ssh-server-mac-os-x/).

- Setting up keys. If you have not setup keys before, the easiest way to do it is as follows:

    `ssh-keygen`

    `ssh-copy-id username@localhost`

Use default settings. You may need to install `ssh-copy-id` if you don't have it on your machine.
Instructions for setting up keys without installing ssh-copy-id can be found [here](http://osxdaily.com/2012/05/25/how-to-set-up-a-password-less-ssh-login/).

To test, run `ssh localhost`.

#### Download Myria
We suggest that you use `git` because you may want to switch between branches later, although GitHub also offers a ZIP file of the master branch.
To do that, install `git` and run `git clone https://github.com/uwescience/myria`,
which creates a directory `myria` with the master branch inside.

#### Build the Myria jar

From within the `myria` directory run `./gradlew jar`.

Note: if it is not a fresh installation (e.g. you just switched from another branch),
you may need to run `./gradlew clean` before `./gradlew jar`. This is for cleaning different versions of Java libraries.

If the build succeeded, you should be able to see jars in `build/libs` including `myria-0.1.jar`.

### 2. Setting up a local MyriaX deployment

The next step is to setup a local deployment of the MyriaX query execution
engine. The local deployment will have a coordinator process and some worker
processes. All processes will run locally.

For data storage, MyriaX uses existing single-node relational
database management systems.  The preferred system is
[PostgreSQL](www.postgresql.org), but you can also use
[SQLite](http://www.sqlite.org/), which is already pre-installed on
many systems. Later, we will show how to execute queries that read
data from HDFS or the local file system.
XXX ADD LINK TO EXAMPLE THAT USES HDFS XXX

To start a local MyriaX deployment, take the following steps:

Go to `myriadeploy`.

#### Deployment configuration file (and Postgres setup)

The deployment configuration file specifies the details of the
deployment such as the number of worker processes to start.

The `myriadeploy` directory contains some example configuration files.

For a local deployment, use `deployment.cfg.local` as a starting point.

Make a copy:

    cp deployment.cfg.local deployment.cfg

Make any desired changes to `deployment.cfg`.

`deployment.cfg.local` uses SQLite as the storage backend. If you want to use [PostgreSQL](www.postgresql.org),
`deployment.cfg.postgres` shows how to set up the configuration file.

To use Postgres instead of SQLite, you need to take these additional steps:

- Install Postgres (e.g., `apt-get install postgresql-9.4`)
- Create a `uwdb` role which Myria will use to manage the tables stored in Postgres.

    ```sql
    create role uwdb with superuser;
    alter role uwdb with login;
    ```
- Create Postgres databases. Important: If you have multiple workers
on the same machine, they need to use different Postgres databases
(but they can share the same Postgres server instance). For example,
the configuration in `deployment.cfg.postgres` needs the Postgres
databases `myria1` and `myria2` on both worker machines:

    ```sql
    createdb myria1;
    createdb myria2;
    ```

- (Optional) Should you wish Myria to connect via an alternate port, add the following key/value pair to the `[deployment]` section of your Myria deployment file:

    ```ini
    database_port = [custom port number]
    ```


#### Setup the working directories and catalogs locally and remotely

Before we can start the cluster and run queries, we need to setup the system catalogs (locally and remotely) and
the working directories (remotely). These initialization steps are automated and executed by the `setup_cluster.py` script.

To initialize (or re-initialize) a new Myria cluster configuration, execute the following command:

    ./setup_cluster.py <deployment.cfg>

This will: Create a directory locally. The name of that directory will be the name of the cluster as specified
in the configuration file (`deployment.cfg`).  Look inside the directory.  You should see the catalog file for the master
and one catalog file for each worker.  In the case of a deployment in a shared-nothing cluster, the worker catalogs
get copied to the remote machines.

Notice that this **overwrites** the catalogs: This step will delete all information about previously ingested relations.


### 3. Running the cluster

Everything is now ready to start the MyriaX query execution engine.

#### Launch the cluster

To start the master and the worker processes, execute the following command

    ./launch_cluster.sh <deployment.cfg>

This command will output things like the following:

    starting master
    ...
    Waiting for the master to be up...

If everything is okay, it will start the workers:

	starting workers
	2 = localhost
	1 = localhost

#### Check the cluster status

A. Query which workers the master knows about. They better match what's in `deployment.cfg`!

    curl -i localhost:8753/workers

B. Query which workers are alive.

    curl -i localhost:8753/workers/alive

#### Using the cluster

To execute queries, we send requests to the coordinator using the coordinator's REST API.
The coordinator takes query plans in JSON format as input.

XXX HERE ADD LINK TO DOCUMENTATION OF THE API XXX

XXX HERE ADD LINK TO DOCUMENTATION OF THE JSON FORMAT FOR QUERIES  XXX

We illustrate the basic functionality using examples in the directory
`jsonQueries/getting_started`. The  `jsonQueries` directory contains additional examples.

A. Ingest some data.

To ingest tables that are not very large, we can send the data directly to the coordinator through the REST API.
We discuss how to ingest larger tables XXX POINTER TO DOCUMENTATION XXX.

The schema of the table `smallTable`, as specified in `ingest_smallTable.json`, is:

    "columnTypes" : ["LONG_TYPE", "LONG_TYPE"],
    "columnNames" : ["col1", "col2"]

To ingest it:

    curl -i -XPOST localhost:8753/dataset -H "Content-type: application/json"  -d @./ingest_smallTable.json

You may need to change the path to your source data file in `ingest_smallTable.json`.

B. Run a query.

    curl -i -XPOST localhost:8753/query -H "Content-type: application/json"  -d @./global_join.json

The Datalog expression of this query is specified in `global_join.json`. The SQL equivalence is:

    Select t1.col1, t2.col2
	From smallTable as t1, smallTable as t2
	Where t1.col2 = t2.col1;

This query writes results back to the backend storage. You should be able to find the result tables in your databases. The table name is specified in the `DbInsert` operator, change it if you want.

C. Download a dataset.

    curl -i localhost:8753/dataset/user-jwang/program-global_join/relation-smallTable/data

This will download the table `smallTable` in CSV format. JSON and TSV are also supported, to do that, specify the format like this:

    curl -i localhost:8753/dataset/user-jwang/program-global_join/relation-smallTable/data?format=json


#### Shutdown the cluster

A. Shutdown the whole cluster via the REST API:

    curl -i localhost:8753/server/shutdown

This will shutdown everything, including the master and all the workers.


B. If there was an error in any query, ingesting data, etc., then the cluster might freeze and most commands that involve workers (e.g., queries, ingestion, and shutdown) would not work. You can force-quit all machines:

    ./stop_all_by_force <deployment.cfg>

This will go to all the nodes, find the master/worker processes under your username, and kill them.



## Using a shared-nothing cluster

To use a shared-nothing cluster to run MyriaX instead of your local
machine, specify the cluster configuration, including the machine names,
port numbers, working directories, database names, etc, in your
`deployment.cfg` file.

See `deployment.cfg.sample` for an example.

Similar to local installation, make sure you have: Java 7,
passwordless SSH from the master machine(s) to all the worker
machine(s), Postgres users and databases created on your worker
machine(s) on your cluster.


## Questions, issues, problems

Welcome to check our [GitHub issues page](https://github.com/uwescience/myria/issues) and post your problems there. We will take care of them!

