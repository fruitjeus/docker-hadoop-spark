# Dockerised HDFS with YARN Resource Manager
Purpose of this project is to set up a minimum Hadoop (3.x.x) cluster to test submission of spark jobs via YARN

## Architecture
2 Containers named `master` and `worker`

Master has the following responsibilities:
HDFS
1. Name Node
2. Data Node
YARN
3. Resource Manager
4. Node Manager
5. Timeline History Server
Spark
6. History Server
Map Reduce
7. Map Reduce History Server

Worker has the following responsibilities:
HDFS
1. Data Node
YARN
2. Node Manager

In this architecture, when jobs are submitted in cluster mode, the driver can be located on either `master` or `worker`.

## Smoke Test
### Starting up the Hadoop cluster
Run `docker-compose up -d` to start up the cluster locally.

### Relevant UIs are up
Assuming the docker-compose file are launched locally, the following URLs should be accessible:
1. Namenode UI at localhost:9870
2. Resource Manager UI at localhost:8088
3. Spark History Server at localhost:18080
4. YARN Timeline History Server at localhost:19888

### Running `spark-submit` locally

1. Download and setup the corresponding version of spark with hadoop on your local machine.
2. Set environment variable `HADOOP_CONF_DIR` to `/path/to/local-hadoop-config` where `local-hadoop-config` is the directory at the root of this repository. This ensures that any `hdfs` or `spark-submit` command will run with the options found in the relevant `.xml` files.
3. Ensure the following entries are set in the host file:
```
127.0.0.1 host.docker.internal
127.0.0.1 master
127.0.0.1 worker
```
4. Run `hdfs dfs -ls /` to confirm correct setup of hadoop client.
5. Run the following command to confirm correct setup of spark client. 
```
spark-submit --class org.apache.spark.examples.SparkPi --master yarn --deploy-mode cluster --driver-memory 2g --executor-memory 2g --executor-cores 1 --conf "spark.eventLog.enabled=true" --conf "spark.eventLog.dir=hdfs:///spark-logs" ${SPARK_HOME}/examples/jars/spark-examples*.jar 10
```
