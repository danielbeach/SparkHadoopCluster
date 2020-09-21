# SparkHadoopCluster
create your own Apache Spark cluster with Hadoop/HDFS installed.

full blog post...

1. install java of course sudo apt-get -y install openjdk-8-jdk-headless default-jre
     This must be done on all nodes.
Install Scala sudo apt install scala
I have no idea if this must be done on all nodes, I did.
Setup password-less ssh between all nodes.
sudo apt install openssh-server openssh-client
create keys ssh-keygen -t rsa -P ""
move .pub key into each worker nodes ~/.ssh/authorized_keys location.
sudo vim /etc/hosts
add lines for master and nodes with name and ip address... something like follows.
173.255.199.161 master and maybe 198.58.124.54 worker1
Probably want to install Spark on master and nodes.... wget https://archive.apache.org/dist/spark/spark-2.4.3/spark-2.4.3-bin-hadoop2.7.tgz
unpack tar xvf spark-2.4.3-bin-hadoop2.7.tgz
move sudo mv spark-2.4.3-bin-hadoop2.7/ /usr/local/spark
We need to set Spark/Java path etc.... sudo vim ~/.bashrc
export PATH=/usr/local/spark/bin:$PATH
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
export PATH=$JAVA_HOME/bin:$PATH
source ~/.bashrc
vim /usr/local/spark/conf/spark-env.sh
export SPARK_MASTER_HOST=<master-ip   <- fill in your IP address of master node here.
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
modify the ridiculously named file vim /usr/local/spark/conf/slaves
add names of master and workers from .hosts file above.
Finally, start Spark.... sh /usr/local/spark/sbin/start-all.sh


Hadoop

wget https://archive.apache.org/dist/hadoop/core/hadoop-2.7.3/hadoop-2.7.3.tar.gz
tar -xvf hadoop-2.7.3.tar.gz
mv hadoop-2.7.3  hadoop<
vim ~/hadoop/etc/hadoop/hadoop-env.sh
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
vim ~/hadoop/etc/hadoop/core-site.xml
<configuration>
        <property>
                <name>fs.default.name</name>
                <value>hdfs://master:9000</value>
        </property>
</configuration>

vim ~/hadoop/etc/hadoop/hdfs-site.xml
<configuration>
        <property>
                <name>dfs.namenode.name.dir</name>
                <value>/home/beach/data/nameNode</value>
        </property>
        <property>
                <name>dfs.namenode.data.dir</name>
                <value>/home/beach/data/dataNode</value>
        </property>
        <property>
                <name>dfs.replication</name>
                <value>2</value>
        </property>
</configuration>

cd ~/hadoop/etc/hadoop 
    mv mapred-site.xml.template mapred-site.xml
    sudo vim ~/hadoop/etc/hadoop/mapred-site.xml
    <configuration>
<property>
                <name>mapreduce.framework.name</name>
                <value>yarn</value>
        </property>
        <property>
                <name>yarn.app.mapreduce.am.resource.mb</name>
                <value>800</value>
        </property>
        <property>
                <name>mapreduce.map.memory.mb</name>
                <value>400</value>
        </property>
        <property>
                <name>mapreduce.reduce.memory.mb</name>
                <value>400</value>
        </property>
</configuration>


vim ~/hadoop/etc/hadoop/slaves
localhost
worker1
worker2

vim ~/hadoop/etc/hadoop/yarn-site.xml
<property>
                <name>yarn.acl.enable</name>
                <value>0</value>
        </property>
        <property>
                <name>yarn.resourcemanager.hostname</name>
                <value>master</value>
        </property>
        <property>
                <name>yarn.nodemanager.aux-services</name>
                <value>mapreduce_shuffle</value>
        </property>
        <property>
                <name>yarn.nodemanager.resource.memory-mb</name>
                <value>800</value>
        </property>
        <property>
                <name>yarn.scheduler.maximum-allocation-mb</name>
                <value>800</value>
        </property>
        <property>
                <name>yarn.scheduler.minimum-allocation-mb</name>
                <value>400</value>
        </property>
</configuration>


sudo vim ~/.bashrc
export PATH=/home/beach/hadoop/bin:/home/beach/hadoop/sbin:$PATH
