FROM debian:9


####################
# JAVA
####################

ENV JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64/

RUN sed -i s/deb.debian.org/archive.debian.org/g /etc/apt/sources.list \
	&& sed -i s/security.debian.org/archive.debian.org/g /etc/apt/sources.list \
	&& sed -i s/stretch-updates/stretch/g /etc/apt/sources.list \
	&& apt-get update \
	&& DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
	openjdk-8-jdk \
	net-tools \
	curl \
	netcat \
	gnupg \
	libsnappy-dev \
	&& rm -rf /var/lib/apt/lists/*

####################
# HADOOP
####################

ENV HADOOP_VERSION 	3.3.6
ENV HADOOP_HOME 	/usr/local/hadoop
ENV HADOOP_OPTS 	-Djava.library.path=/usr/local/hadoop/lib/native
ENV HADOOP_URL  	https://www.apache.org/dist/hadoop/common/hadoop-$HADOOP_VERSION/hadoop-$HADOOP_VERSION.tar.gz
ENV HADOOP_CONF_DIR 	${HADOOP_HOME}/etc/hadoop
ENV USER		root
ENV PATH		$HADOOP_HOME/bin/:$PATH

RUN set -x \ 
	&& curl -O https://dist.apache.org/repos/dist/release/hadoop/common/KEYS \
	&& gpg --import KEYS \
	&& curl -fSL "$HADOOP_URL" -o /tmp/hadoop.tar.gz \
	&& curl -fSL "$HADOOP_URL.asc" -o /tmp/hadoop.tar.gz.asc \
	&& gpg --verify /tmp/hadoop.tar.gz.asc \
	&& tar -xvf /tmp/hadoop.tar.gz -C /tmp/ \
	&& rm /tmp/hadoop.tar.gz* \
	&& mv /tmp/hadoop-$HADOOP_VERSION /usr/local/hadoop \
	&& mkdir -p /usr/local/hadoop/logs

# Overwrite default HADOOP configuration files with our config files
COPY config $HADOOP_HOME/etc/hadoop/

RUN mkdir -p /hadoop/dfs/namenode /hadoop/dfs/datanode /hadoop/hadooptmp /hadoop/dfs/secondary \
	&& hdfs --config $HADOOP_CONF_DIR namenode -format

####################
# PORTS
####################
#
# http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.3.0/bk_HDP_Reference_Guide/content/reference_chap2.html
# http://www.cloudera.com/content/cloudera/en/documentation/core/latest/topics/cdh_ig_ports_cdh5.html
# http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/core-default.xml
# http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/hdfs-default.xml
# https://hadoop.apache.org/docs/r3.3.6/hadoop-yarn/hadoop-yarn-common/yarn-default.xml

# HDFS: NameNode (NN):
#	9820 = fs.defaultFS			(IPC / File system metadata operations)
#						(9000 is also frequently used alternatively)
#	9871 = dfs.namenode.https-address	(HTTPS / Secure UI)
#	9870 = dfs.namenode.https-address	(HTTPS / Secure UI)
# HDFS: DataNode (DN):
#	9866 = dfs.datanode.address		(Data transfer)
#	9867 = dfs.datanode.ipc.address	(IPC / metadata operations)
#	9864 = dfs.datanode.https.address	(HTTPS / Secure UI)
# HDFS: Secondary NameNode (SNN)
#	9868 = dfs.secondary.http.address	(HTTP / Checkpoint for NameNode metadata)
# YARN: ResourceMananger
#	8032 = yarn.resourcemanager.address	
#	8030 = yarn.resourcemanager.scheduler.address
#	8088 = yarn.resourcemanager.webapp.address
#	8090 = yarn.resourcemanager.webapp.https.address
EXPOSE 9000 9870 9866 9867 9864 9868 8088 8030

CMD ["hdfs"]
