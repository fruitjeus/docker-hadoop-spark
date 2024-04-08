# Dockerised HDFS with YARN Resource Manager
Purpose of this project is to set up a minimum Hadoop (3.x.x) cluster to test submission of spark jobs via YARN

## Architecture
2 Containers named master and worker

Master has the following responsibilities:
HDFS
1. Name Node
2. Data Node
YARN
3. Resource Manager
4. Node Manager
Spark
5. History Server

Worker has the following responsibilities:
HDFS
1. Data Node
YARN
2. Node Manager

In this architecture, the driver can be located on either Master or Worker.

## Smoke Test

```
spark-submit 
```
