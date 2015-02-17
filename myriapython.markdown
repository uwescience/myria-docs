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