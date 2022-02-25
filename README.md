
# Hadoop One Node Virtual Machine Installation


## System Requirement

- Vmware Workstation/ Virtualbox
- Ubuntu 20.04.03 LTS Focal
- Minimum 4 GB RAM
- 60GB HDD/SSD


## Installation

Download Vmware Workstation/Virtualbox and install ubuntu 20.04.03
on it. Run the following commands.

```bash
  sudo apt update
  sudo apt upgrade -y
  sudo apt-get install default-jdk
```

Now check that it has properly installed on the computer by
running
```bash
  java --version
```

Now add a dedicated Hadoop User (Non Root) and install SSH
```bash
  sudo apt install openssh-server openssh-client -y
  sudo useradd hadoop  
```
Enable Passwordless SSH for Hadoop User
```bash
  su - hadoop
  ssh-keygen -t rsa -P '' -f ~/.ssh/id_rsa
  chmod 0600 ~/.ssh/authorized_keys
  ssh localhost   //to verify 
```

Next step is to Download and install the hadoop. You can visit https://hadoop.apache.org/releases.html to download the hadoop latest version.
Click on Binary and download hadoop tar pakage (with .tar.gz extension). Use the following commands to unzip the hadoop tar file.

```bash
  tar xzf hadoop-3.2.2.tar.gz    
```
Remember to use your specific downloaded hadoop version in above command.





## Conifgurating the Hadoop Files

Now we have to configure the following files to setup hadoop one node cluster.

- bashrc
- hadoop-env.sh
- core-site.xml
- hdfs-site.xml
- mapred-site-xml
- yarn-site.xml


## Configure .bashrc File
```bash
  sudo nano .bashrc   

  #Hadoop Related Options
export HADOOP_HOME=/home/hdoop/hadoop-3.2.2
export HADOOP_INSTALL=$HADOOP_HOME
export HADOOP_MAPRED_HOME=$HADOOP_HOME
export HADOOP_COMMON_HOME=$HADOOP_HOME
export HADOOP_HDFS_HOME=$HADOOP_HOME
export YARN_HOME=$HADOOP_HOME
export HADOOP_COMMON_LIB_NATIVE_DIR=$HADOOP_HOME/lib/native
export PATH=$PATH:$HADOOP_HOME/sbin:$HADOOP_HOME/bin
export HADOOP_OPTS"-Djava.library.path=$HADOOP_HOME/lib/native"
```

## Configure hadoop-env.sh File
Get the java path and locate the openJDk directory.
```bash
  which javac
  readlink -f /usr/bin/javac
  sudo nano $HADOOP_HOME/etc/hadoop/hadoop-env.sh 
```

Uncomment the $JAVA_HOME and copy the section of the path just before the "/bin/javac" output from the terminal and paste it after $JAVA_HOME
```bash
  sudo nano $HADOOP_HOME/etc/hadoop/hadoop-env.sh 
```

## Configure core-site.xml File
This file consist the information about the HDFS and HAdoop core properties
```bash
  sudo nano $HADOOP_HOME/etc/hadoop/core-site.xml
```
Add the following Configuration in your file. Make sure add your directory URL.
- Make sure to add the "tmpdata" directory in your specified location.

```bash
  <configuration>
  <property>
  <name>hadoop.tmp.dir</name>
  <value>/home/hdoop/tmpdata</value>
  </property>
  <property>
  <name>fs.default.name</name>
  <value>hdfs://127.0.0.1:9000</value>
  </property>
  </configuration>
```
## Configure hdfs-site.xml File
This file deals with storage management reagrding the node metadata, fsimage file, and edit log file and other things. Here we have to set the path for the NAMENODE and DATANODE
```bash
  sudo nano $HADOOP_HOME/etc/hadoop/hdfs-site.xml
```
Add the following Configuration in your file. Make sure add your directory URL.
- If you do not see the specified directories of NAMENODE and DATANODE, then you can make one.

```bash
  <configuration>
  <property>
  <name>dfs.data.dir</name>
  <value>/home/hdoop/dfsdata/namenode</value>
  </property>
  <property>
  <name>dfs.data.dir</name>
  <value>/home/hdoop/dfsdata/datanode</value>
  </property>
  <property>
  <name>dfs.replication</name>
  <value>1</value>
  </property>
  </configuration>
```

## Configure mapred-site.xml File
Here we will define the MapReduce Values.
```bash
  sudo nano $HADOOP_HOME/etc/hadoop/mapred-site.xml
```
Add the following Configuration in your file.

```bash
  <configuration>
  <property>
  <name>mapreduce.framework.name</name>
  <value>yarn</value>
  </property>
  </configuration>
```

## Configure yarn-site.xml File
This file is used to adjust the settings related to the YARN, which generally includes Node Manager, Resource Manager, Containers, and Application Master.
```bash
  sudo nano $HADOOP_HOME/etc/hadoop/yarn-site.xml
```
Add the following Configuration in your file.

```bash
  <configuration>
  <property>
  <name>yarn.nodemanager.aux-services</name>
  <value>mapreduce_shuffle</value>
  </property>
  <property>
  <name>yarn.nodemanager.aux-services.mapreduce.shuffle.class</name>
  <value>org.apache.hadoop.mapred.ShuffleHandler</value>
  </property>
  <property>
  <name>yarn.resourcemanager.hostname</name>
  <value>127.0.0.1</value>
  </property>
  <property>
  <name>yarn.acl.enable</name>
  <value>0</value>
  </property>
  <property>
  <name>yarn.nodemanager.env-whitelist</name>   
  <value>JAVA_HOME,HADOOP_COMMON_HOME,HADOOP_HDFS_HOME,HADOOP_CONF_DIR,CLASSPATH_PERPEND_DISTCACHE,HADOOP_YARN_HOME,HADOOP_MAPRED_HOME</value>
  </property>
  </configuration>
```
## Format HDFS NAMENODE
Before starting the Hadoop services it is important to format the NAMENODE. The Shutdown message you recieve will ensure that NAMENODE has successfully formated.

## Starting the Hadoop
Run the following commands to start the hadoop service.
```bash
  start-dfs.sh
  start-yarn.sh
```

Check wheather all service are running successfully which includes: DataNode, SecondaryNameNode, NameNode, NodeManager, ResourceManager and Jps.
```bash
  jps
```

### Access Hadoop User Interface from browser
For NameNode
```bash
  http://localhost:9870
```
For DataNode
```bash
  http://localhost:9864
```

For Yarn Resource Manager.
```bash
  http://localhost:8088
```

Congratulation you have successfully installed Hadoop.
