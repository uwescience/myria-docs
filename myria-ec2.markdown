---
layout: default
title: Myria EC2
group: "extra"
weight: 4
---

# Myria EC2

Myria-EC2 is a tool that allows you to automatically deploy Myria to a cluster through the Amazon EC2 service. To do this, you must have an [Amazon EC2](http://aws.amazon.com/ec2/) account. You will also need the Myria-EC2 project, which is found under the Myria-Stack. Alternatively, you can directly clone the Myria-EC2 [repository](https://github.com/uwescience/myria-ec2).

## Installation
Install and configure StarCluster (e.g., `sudo apt-get install starcluster`). To start the setup, run ```starcluster help``` and select the second option.  In the ```~/.starcluster/config``` file add your AWS credentials fill in your AWS\_ACCESS\_KEY\_ID, AWS\_SECRET\_ACCESS\_KEY and AWS\_USER\_ID. This information can be found under the [IAM Management Console](http://aws.amazon.com/iam/).

To create a key, run ```starcluster createkey UNIQUE_KEYNAME -o ~/.ssh/mykey.rsa```.

In the ```~/.starcluster/config``` file, change the key information with the name of your unique key by updating the following parameters:

```config
[key mykey]
KEY_LOCATION=~/.ssh/mykey.rsa
KEYNAME = mykey
```

Also in the `[global]` section of the file, edit the INCLUDE parameter to point to the myriaccluster.config file as follows: ```INCLUDE=~/.starcluster/myriacluster.config```

As a second step, clone the Myria-EC2 repository (`git clone https://github.com/uwescience/myria-ec2.git`). Install Myria-EC2 by running `sudo python setup.py install`. The cluster configuration is located under ```~/.starcluster/myriacluster.config```

In the myriacluster.config file, the first few lines are most useful to modify the cluster configuration.  The most relevant options are:

```
[cluster myriacluster]
CLUSTER_SIZE = 2                  # How many instances to deploy?
NODE_INSTANCE_TYPE = t1.micro     # What instance type?
SPOT_BID = 0.0035                 # Change your spot instance bid prices if requesting a larger instance type!
```

## Running the Cluster on EC2
Modify cluster settings in ~/.starcluster/myriaconfig (e.g., change instance sizes, add spot instances).

  a) Start a cluster:
       ```starcluster start -c myriacluster myriacluster```

  b) Alternatively, specify a cluster of size n instances:
       ```starcluster start -c myriacluster --cluster-size n myriacluster```

  c) Terminate a cluster:
       ```starcluster terminate myriacluster```


## Automatically Uploading Datasets to the Cluster on Launch

The `myriaingest` plugin may be used to automatically populate your Myria/Starcluster instance upon initialization.  The following configuration changes are required:

1. Add the plugin to your configuration:

```config
[cluster myriademocluster]
...
PLUGINS = ..., my-ingest
...
```

2. Specify the ingest metadata:

```config
[plugin my-ingest]
SETUP_CLASS = myriaingestplugin.MyriaIngest
NAME = MyRelationName
SCHEMA = {"columnNames": ["column0", "column1"], "columnTypes": ["INT_TYPE", "INT_TYPE"]}
SCAN_PARAMETERS = {"skip": 1}
URIs = https://s3-us-west-2.amazonaws.com/myria/public-adhoc-TwitterK.csv
       https://s3-us-west-2.amazonaws.com/myria/public-adhoc-TwitterK.csv
       https://s3-us-west-2.amazonaws.com/myria/public-adhoc-TwitterK.csv
       https://s3-us-west-2.amazonaws.com/myria/public-adhoc-TwitterK.csv
OVERWRITE_ON_RESTART = True
```