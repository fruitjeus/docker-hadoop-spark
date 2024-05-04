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

### Running `spark-submit` locally

1. Download and setup the corresponding version of spark with hadoop on your local machine.
2. Set environment variable `HADOOP_CONF_DIR` to `/path/to/local-hadoop-config` where `local-hadoop-config` is the directory at the root of this repository. This ensures that the `spark-submit` command will run with the options found in the relevant `.xml` files.
3. Run the following command from the `SPARK_HOME` directory to confirm correct setup of spark client. 
```
spark-submit --class org.apache.spark.examples.SparkPi --master yarn --deploy-mode cluster --driver-memory 2g --executor-memory 2g --executor-cores 1 examples/jars/spark-examples*.jar 10
```
