---
layout: default
title: Start Here 
group: "extra"
weight: 0
---

# N-body Use Case

## Ingesting Data
To ingest simulation data into Myria, you can either upload data in Tipsy or NChilada format. We detail instructions for each below.

* Tipsy
  * For this format, first upload the simulation data under the master at vega.cs.washington.edu. If you need a directory or more space to upload your data, please contact the myria-users mailing list. 
  * After the data is uploaded in the cluster you'll then need to ingest it into the Myria system. Use the following [bash](/usecase-scripts/nbody/generate_ingest.sh) file to ingest the data. In this file, you'll need to replace a few sections that are placed in brackets (we also have a working example based on the romulus8 dataset [here](/usecase-scripts/nbody/generate_ingest_example.sh)):
    * [SNAPSHOTS] - This should be replaced with the snapshot numbers listed in order separated by spaces
    * [PATH\_TO_SCRIPTS] - This should be replaced by the path of the data in vega.cs.washington.edu.
    * [SIMULATION_NAME] - This should be replaced by the name fo the simulation you would like to address the dataset by (i.e. Romulus, Cosmo50, etc.). 
    * [USER_NAME] - your username. This can be anything you'd like. 
  * Once this is filled out, run the bash script. This will create several ingest files as well as a file called `ingest_all_cosmo.sh`. Running `ingest_all_cosmo.sh` will sequentially ingest each snapshot you've listed out in the bash script. In the Myria website, you can check the progress of each ingest under the Queries tab. 

* NChilada
	* For this format, it is recommended to upload the data into HDFS since the file sizes are larger. (Instructions to do this from Globus coming soon).
	* After the data is loaded into HDFS on vega.cs.washington.edu, we can then run the following [script](/usecase-scripts/nbody/parallel_ingest.sh) to ingest the data into Myria.  (Instructions coming soon).
	
## Running Queries

## Example Custom Queries

## Using the MyMergerTree Visualization