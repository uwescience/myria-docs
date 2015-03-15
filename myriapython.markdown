---
layout: default
title: Myria Python
group: "extra"
weight: 4
---

# Myria Python

Myria-Python is a Python interface to the [Myria project](http://myria.cs.washington.edu), a distributed, shared-nothing big data management system and Cloud service from the [University of Washington](http://www.cs.washington.edu).

The Python components include intuitive, high-level interfaces for working with Myria, along with lower-level operations for interacting directly with the Myria API.

```python
  # Lower-level interaction via the API connection
  connection = MyriaConnection(hostname='localhost', port=8753)
  datsets = connection.datasets()

  # Higher-level interaction via relation and query instances
  relation = MyriaRelation(datasets[0], connection=connection)
  json = relation.toJson()
```

## Installation

Users can install the Python libraries using `pip install myria-python`. Developers should clone the [repository](https://github.com/uwescience/myria-python) and run `python setup.py develop`.


## Using Python with the Myria Service

### Part 1: Uploading Data

We illustrate the basic functionality using examples in the directory
`jsonQueries/getting_started`. The  `jsonQueries` directory contains additional examples. In the example below, we upload the smallTable to the Myria Service. Here is an example you can run through your terminal (assuming you've setup myria-python):

```
myria_upload --overwrite --relation smallTable \path\to\data\file
```

### Part 2: Running MyriaQL Queries
In this Python example, we query the smallTable relation by creating a count(*) query using the MyriaQL language.In this query, we store our result to a relation called countResult. To learn more about the Myria query language, check out the [MyriaQL](myriaql.html) page.

```
from myria import MyriaConnection
program = "q = [from scan(public:adhoc:smallTable) as t emit count(*) as countRelation]; store(q, countResult);"
MyriaConnection.execute_program(program)
```

### Part 3: Downloading Data
Finally, we can download the result of our query by downloading the countResult table through the following Python program:

```
from myria import MyriaConnection
connection = MyriaConnection(hostname='rest.myria.cs.washington.edu', port=1776, ssl=True)
relation = {"userName": "public", "programName": "adhoc", "relationName": "countResult"}
response = connection.download_dataset(relation)
print response
```

## Using Python with your own Myria Deployment
For the examples below, we used localhost as the hostname example. This can be changed depending on where you are hosting Myria. 

### Part 1: Uploading Data
```
from myria import MyriaConnection
connection = MyriaConnection(hostname='localhost', port='8753')
relation = {"userName": "jwang", "programName": "global_join", "relationName": "smallTable"}
schema = {"columnTypes" : ["LONG_TYPE", "LONG_TYPE"], "columnNames" : ["follower", "followee"]}
source = {"dataType" : "File", "filename" : "/path/to/file"}
response = connection.upload_source(relation_key=relation, schema=schema, source=source)
```

Alternatively, you can upload data through the myira-upload tool:

```
myria_upload --hostname localhost --port 8753 --no-ssl --user jwang --program global_join --relation smallTable /path/to/file
```

### Part 2: Building Queries
```
from myria import MyriaConnection
connection = MyriaConnection(hostname='localhost', port='8753')
connection.submit_query(query="/path/to/json/query")
```

### Part 3: Downloading Data
Finally, we can download the result of our query from Part 2 by running the following Python program:
```
from myria import MyriaConnection
connection = MyriaConnection(hostname='localhost', port=8753)
relation = {"userName": "jwang", "programName": "global_join", "relationName": "smallTable_join_smallTable"}
response = connection.download_dataset(relation)
print response

```

## Loading Python in Parallel
Coming soon...


