



[TOC]

## 机器节点

| 节点      | 配置        | IP         | 职责 |
| --------- | ----------- | ---------- | ---- |
| hadoop01  | 8C/32G/500G | 10.10.9.15 |      |
| hadoop02  | 8C/32G/500G | 10.10.9.16 |      |
| hadoop03  | 8C/32G/500G | 10.10.9.17 |      |
| hive01    | 8C/24G/200G | 10.10.9.21 |      |
| hive02    | 8C/24G/200G | 10.10.9.22 |      |
| hive03    | 8C/24G/200G | 10.10.9.23 |      |
| ds01      | 8C/32G/200G | 10.10.9.18 |      |
| ds02      | 8C/32G/200G | 10.10.9.19 |      |
| ds03      | 8C/32G/200G | 10.10.9.20 |      |
| common-db | 4C/8G/200G  | 10.10.9.24 |      |



## 通用安装

### java

```
yum install java-1.8.0-openjdk-devel

export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.412.b08-1.el7_9.x86_64
export CLASSPATH=$CLASSPATH:$JAVA_HOME/lib/
export PATH=$JAVA_HOME/bin:$PATH
```



### python38

```
cd /home/Python-3.8.19

./configure --prefix=/soft/python38

make

# 这里使用 make altinstall 而不是 make install，以避免覆盖系统自带的 python 命令。
sudo make altinstall
```

```
cp /soft/python38/bin/python3.8  /soft/python38/bin/python3
```



```
export PYTHON_LAUNCHER=/soft/python38
export PATH=$PYTHON_LAUNCHER/bin:$PATH
```



### 配置免密登录

hadoop01,hadoop02,hadoop03,hive01,hive02,hive03, ds01,ds02,ds03配置/etc/hosts：

```
10.10.9.15      hadoop01
10.10.9.16      hadoop02
10.10.9.17      hadoop03
10.10.9.21      hive01
10.10.9.22      hive02
10.10.9.23      hive03
10.10.9.18      ds01
10.10.9.19      ds02
10.10.9.20      ds03
```



hadoop01,hadoop02,hadoop03,hive01,hive02,hive03, ds01,ds02,ds03执行：

```
$ ssh-keygen -t dsa
$ cat /root/.ssh/id_dsa.pub
$ touch /root/.ssh/authorized_keys
# 复制haddop01,hadoop02,hadoop03,hive01,hive02,hive03,ds01,ds02,ds03中/root/.ssh/id_dsa.pub内容到authorized_keys
# 复制hadoop01的authorized_keys到其他节点
$ scp /root/.ssh/authorized_keys root@hadoop02:/root/.ssh/
$ scp /root/.ssh/authorized_keys root@hadoop03:/root/.ssh/
$ scp /root/.ssh/authorized_keys root@hive01:/root/.ssh/
$ scp /root/.ssh/authorized_keys root@hive02:/root/.ssh/
$ scp /root/.ssh/authorized_keys root@hive03:/root/.ssh/
$ scp /root/.ssh/authorized_keys root@ds01:/root/.ssh/
$ scp /root/.ssh/authorized_keys root@ds02:/root/.ssh/
$ scp /root/.ssh/authorized_keys root@ds03:/root/.ssh/
```



### MYSQL

```
hive/Tlwl@2024
dolphinscheduler/Tlwl@2024
```

## HADOOP集群

### 配置集群

#### 新建目录

**hadoop01,hadoop02,hadoop03**新增以下目录

```
mkdir  /root/hadoop
mkdir  /root/hadoop/tmp
mkdir  /root/hadoop/var
mkdir  /root/hadoop/dfs
mkdir  /root/hadoop/dfs/name
mkdir  /root/hadoop/dfs/data
```

#### 配置文件 core-site.xml  

**hadoop01,hadoop02,hadoop03**上core-site.xml增加如下配置项：

```
   <property>
        <name>hadoop.tmp.dir</name>
        <value>/root/hadoop/tmp</value>
        <description>Abase for other temporary directories.</description>
   </property>
   <property>
        <name>fs.default.name</name>
        <value>hdfs://hadoop01:9000</value>
   </property>

   <property>
        <name>hadoop.proxyuser.root.groups</name>
        <value>*</value>
   </property>
   <property>
        <name>hadoop.proxyuser.root.hosts</name>
        <value>*</value>
   </property>

    <property>
        <name>hadoop.proxyuser.hive.groups</name>
        <value>*</value>
    </property>
    <property>
        <name>hadoop.proxyuser.hive.hosts</name>
        <value>*</value>
    </property>
```



#### 配置文件 hadoop-env.sh  

**hadoop01,hadoop02,hadoop03**上hadoop-env.sh打开如下配置项：

```
export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.412.b08-1.el7_9.x86_64
```



#### 配置文件 hdfs-site.xml  

**hadoop01,hadoop02,hadoop03**上hdfs-site.xml 打开如下配置项：

```
<property>
   <name>dfs.name.dir</name>
   <value>/root/hadoop/dfs/name</value>
   <description>Path on the local filesystem where theNameNode stores the namespace and transactions logs persistently.</description>
</property>
<property>
   <name>dfs.data.dir</name>
   <value>/root/hadoop/dfs/data</value>
   <description>Comma separated list of paths on the localfilesystem of a DataNode where it should store its blocks.</description>
</property>
<property>
   <name>dfs.replication</name>
   <value>2</value>
</property>
<property>
      <name>dfs.permissions</name>
      <value>false</value>
      <description>need not permissions</description>
</property>
<property>
  <name>dfs.client.read.shortcircuit</name>
  <value>false</value>
</property>
```



#### 配置文件 mapred-site.xml  

hadoop01

```
<property>
   <name>mapred.job.tracker</name>
   <value>hadoop01:49001</value>
</property>
<property>
      <name>mapred.local.dir</name>
       <value>/root/hadoop/var</value>
</property>
<property>
       <name>mapreduce.framework.name</name>
       <value>yarn</value>
</property>
<property>
  <name>yarn.app.mapreduce.am.env</name>
  <value>HADOOP_MAPRED_HOME=/soft/hadoop-3.2.4</value>
</property>
<property>
  <name>mapreduce.map.env</name>
  <value>HADOOP_MAPRED_HOME=/soft/hadoop-3.2.4</value>
</property>
<property>
  <name>mapreduce.reduce.env</name>
  <value>HADOOP_MAPRED_HOME=/soft/hadoop-3.2.4</value>
</property>
```

hadoop02

```
<property>
   <name>mapred.job.tracker</name>
   <value>hadoop02:49001</value>
</property>
<property>
      <name>mapred.local.dir</name>
       <value>/root/hadoop/var</value>
</property>
<property>
       <name>mapreduce.framework.name</name>
       <value>yarn</value>
</property>

<property>
  <name>yarn.app.mapreduce.am.env</name>
  <value>HADOOP_MAPRED_HOME=/soft/hadoop-3.2.4</value>
</property>
<property>
  <name>mapreduce.map.env</name>
  <value>HADOOP_MAPRED_HOME=/soft/hadoop-3.2.4</value>
</property>
<property>
  <name>mapreduce.reduce.env</name>
  <value>HADOOP_MAPRED_HOME=/soft/hadoop-3.2.4</value>
</property>
```

hadoop03

```
<property>
   <name>mapred.job.tracker</name>
   <value>hadoop03:49001</value>
</property>
<property>
      <name>mapred.local.dir</name>
       <value>/root/hadoop/var</value>
</property>
<property>
       <name>mapreduce.framework.name</name>
       <value>yarn</value>
</property>

<property>
  <name>yarn.app.mapreduce.am.env</name>
  <value>HADOOP_MAPRED_HOME=/soft/hadoop-3.2.4</value>
</property>
<property>
  <name>mapreduce.map.env</name>
  <value>HADOOP_MAPRED_HOME=/soft/hadoop-3.2.4</value>
</property>
<property>
  <name>mapreduce.reduce.env</name>
  <value>HADOOP_MAPRED_HOME=/soft/hadoop-3.2.4</value>
</property>
```



#### 配置文件 workers 

**hadoop01**

```
hadoop01
hadoop02
hadoop03
```

**hadoop02**

```
hadoop01
hadoop02
hadoop03
```

**hadoop03**

```
hadoop01
hadoop02
hadoop03
```

#### 配置文件 yarn-site.xml

**hadoop01,hadoop02,hadoop03**上yarn-site.xml增加如下配置项：

```
   <property>
        <name>yarn.resourcemanager.hostname</name>
        <value>hadoop01</value>
   </property>
   <property>
        <description>The address of the applications manager interface in the RM.</description>
        <name>yarn.resourcemanager.address</name>
        <value>${yarn.resourcemanager.hostname}:8032</value>
   </property>
   <property>
        <description>The address of the scheduler interface.</description>
        <name>yarn.resourcemanager.scheduler.address</name>
        <value>${yarn.resourcemanager.hostname}:8030</value>
   </property>
   <property>
        <description>The http address of the RM web application.</description>
        <name>yarn.resourcemanager.webapp.address</name>
        <value>${yarn.resourcemanager.hostname}:8088</value>
   </property>
   <property>
        <description>The https adddress of the RM web application.</description>
        <name>yarn.resourcemanager.webapp.https.address</name>
        <value>${yarn.resourcemanager.hostname}:8090</value>
   </property>
   <property>
        <name>yarn.resourcemanager.resource-tracker.address</name>
        <value>${yarn.resourcemanager.hostname}:8031</value>
   </property>
   <property>
        <description>The address of the RM admin interface.</description>
        <name>yarn.resourcemanager.admin.address</name>
        <value>${yarn.resourcemanager.hostname}:8033</value>
   </property>
   <property>
        <name>yarn.nodemanager.aux-services</name>
        <value>mapreduce_shuffle</value>
   </property>
   <property>
        <name>yarn.scheduler.maximum-allocation-mb</name>
        <value>4096</value>
   </property>
   <property>
        <name>yarn.nodemanager.vmem-pmem-ratio</name>
        <value>2.1</value>
   </property>
   <property>
        <name>yarn.nodemanager.resource.memory-mb</name>
        <value>4096</value>
   </property>
   <property>
        <name>yarn.nodemanager.vmem-check-enabled</name>
        <value>false</value>
</property>
```



#### 启动脚本 start-dfs.sh 、stop-dfs.sh

**hadoop01,hadoop02,hadoop03**上start-dfs.sh 、stop-dfs.sh两个文件开头位置添加如下配置：

```
HDFS_DATANODE_USER=root
HADOOP_SECURE_DN_USER=root
HDFS_NAMENODE_USER=root
HDFS_SECONDARYNAMENODE_USER=root
```



#### 启动脚本 start-yarn.sh 、stop-yarn.sh

**hadoop01,hadoop02,hadoop03**上start-yarn.sh 、stop-yarn.sh两个文件开头位置添加如下配置：

```
YARN_RESOURCEMANAGER_USER=root
HADOOP_SECURE_DN_USER=root
YARN_NODEMANAGER_USER=root
```

#### 初始化hdfs

进入hadoop01节点，初始化hdfs:

(因为hadoop01是namenode，hadoop02和hadoop03都是datanode，只需要对hadoop01进行初始化操作，即对hdfs进行格式化)

```
cd /soft/hadoop-3.2.4/bin
./hadoop  namenode  -format
```

进入hadoop01节点，启动dfs，初始化hdfs集群（保证三个节点都有dataNode）

```
cd /soft/hadoop-3.2.4/sbin
start-dfs.sh
stop-dfs.sh
```



#### 启动集群

进入hadoop01节点

```
cd /soft/hadoop-3.2.4/ && bash ./sbin/start-all.sh
```



#### 停止集群

进入hadoop01节点

```
cd /soft/hadoop-3.2.4/ && bash ./sbin/stop-all.sh
```



#### 添加环境变量

**hadoop01,hadoop02,hadoop03**上/etc/profile

```
export HADOOP_HOME=/soft/hadoop-3.2.4
export PATH=$HADOOP_HOME/sbin:$HADOOP_HOME/bin:$PATH
```



#### 修改hdfs路径权限（按需）

```
# 查看当前权限
hdfs dfs -ls /
# 修改/tmp目录权限
hdfs dfs -chmod -R 777 /tmp
```

#### 调整hadoop关键参数（按需）

```
参考：https://blog.csdn.net/u013412066/article/details/129095011
```





## HIVE集群



**hive版本：3.1.3**  **zookeeper版本：3.9.0**



### ZOOKEEPER配置

#### hive01上/soft/zookeeper-3.9.0-bin/conf/zoo.cfg

```
tickTime=2000
initLimit=10
syncLimit=5
dataDir=/opt/data/zookeeper
clientPort=2181
server.1=hive01:2888:3888
server.2=hive02:2888:3888
server.3=hive03:2888:3888
```



#### hive02上/soft/zookeeper-3.9.0-bin/conf/zoo.cfg

```
tickTime=2000
initLimit=10
syncLimit=5
dataDir=/opt/data/zookeeper
clientPort=2181
server.1=hive01:2888:3888
server.2=hive02:2888:3888
server.3=hive03:2888:3888
```



#### hive03上/soft/zookeeper-3.9.0-bin/conf/zoo.cfg

```
tickTime=2000
initLimit=10
syncLimit=5
dataDir=/opt/data/zookeeper
clientPort=2181
server.1=hive01:2888:3888
server.2=hive02:2888:3888
server.3=hive03:2888:3888
```

#### hive01,hive02,hive03

```
echo "1" > /opt/data/zookeeper/myid # 在 hive01 上
echo "2" > /opt/data/zookeeper/myid # 在 hive02 上
echo "3" > /opt/data/zookeeper/myid # 在 hive03 上

```

#### 启动

hive01,hive02,hive03

```
/soft/zookeeper-3.9.0-bin/bin/zkServer.sh start
```



### HIVE配置



#### hive01上的hive-site.xml

```
<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
<configuration>
    <!-- metabase server -->
    <property>
        <name>javax.jdo.option.ConnectionURL</name>
        <value>jdbc:mysql://10.10.9.24:3306/hivedb?useUnicode=true&amp;characterEncoding=UTF-8&amp;useSSL=false&amp;createDatabaseIfNotExist=true</value>
    </property>
    <property>
        <name>javax.jdo.option.ConnectionDriverName</name>
        <value>com.mysql.cj.jdbc.Driver</value>
    </property>
    <property>
        <name>javax.jdo.option.ConnectionUserName</name>
        <value>hive</value>
    </property>
    <property>
        <name>javax.jdo.option.ConnectionPassword</name>
        <value>Tlwl@2024</value>
    </property>
    <property>
        <name>hive.metastore.schema.verification</name>
        <value>false</value>
    </property>
    <property>
        <name>hive.cli.print.current.db</name>
        <value>true</value>
    </property>
    <property>
        <name>hive.cli.print.header</name>
        <value>true</value>
    </property>
        <property>
        <name>hive.metastore.uris</name>
        <value>thrift://hive01:9083,thrift://hive02:9083,thrift://hive03:9083</value>
    </property>

    <!-- hiveserver2 -->
    <property>
        <name>hive.server2.thrift.port</name>
        <value>10000</value>
    </property>
    <property>
        <name>hive.server2.thrift.bind.host</name>
        <value>hive01</value>
        <description>hiveserver2 bind hivenode hostname</description>
    </property>

    <property>
        <name>hive.server2.support.dynamic.service.discovery</name>
        <value>true</value>
    </property>
    <property>
        <name>hive.zookeeper.quorum</name>
        <value>hive01:2181,hive02:2181,hive03:2181</value>
    </property>
    <property>
        <name>hive.zookeeper.client.port</name>
        <value>2181</value>
    </property>
    <property>
        <name>hive.server2.zookeeper.namespace</name>
        <value>hiveserver2</value>
    </property>

    <property>
        <name>hive.server2.thrift.client.user</name>
        <value>root</value>
    </property>
    <property>
        <name>hive.server2.thrift.client.password</name>
        <value>Tlwl@7777</value>
    </property>

    <property>
        <name>hive.execution.engine</name>
        <value>mr</value>
        <description>mr|tez|spark,default mr</description>
    </property>

    <!-- for hive user config -->
    <property>
        <name>hive.server2.enable.doAs</name>
        <value>false</value>
        <description>false: yarn job as hive; true: yarn job as real username</description>
    </property>
    <!--
    <property>
        <name>hive.server2.authentication</name>
        <value>NONE</value>
    </property>
    <property>
        <name>hive.security.authorization.enabled</name>
        <value>false</value>
    </property>
    <property>
        <name>hive.security.authorization.manager</name>
        <value>org.apache.hadoop.hive.ql.security.authorization.plugin.sqlstd.SQLStdConfOnlyAuthorizerFactory</value>
    </property>
    -->
    <property>
        <name>hive.users.in.admin.role</name>
        <value>root</value>
        <description>Users in admin role</description>
    </property>
    <property>
        <name>hive.exec.scratchdir</name>
        <value>/user/hive/tmp</value>
        <description>Hive query execution temporary scratch directory.</description>
    </property>
    <!-- 
    <property>
        <name>hadoop.proxyuser.hive.groups</name>
        <value>*</value>
    </property>
    <property>
        <name>hadoop.proxyuser.hive.hosts</name>
        <value>*</value>
    </property>
    -->
    <!-- for support merge into -->
    <property>
        <name>hive.support.concurrency</name>
        <value>true</value>
    </property>
    <property>
        <name>hive.txn.manager</name>
        <value>org.apache.hadoop.hive.ql.lockmgr.DbTxnManager</value>
    </property>
    <property>
        <name>hive.compactor.initiator.on</name>
        <value>true</value>
    </property>
    <property>
        <name>hive.compactor.worker.threads</name>
        <value>1</value>
    </property>
    <property>
        <name>hive.enforce.bucketing</name>
        <value>true</value>
    </property>
    <property>
        <name>hive.enforce.sorting</name>
        <value>true</value>
    </property>
    <property>
        <name>hive.exec.dynamic.partition.mode</name>
        <value>nonstrict</value>
    </property>
    <property>
        <name>hive.exec.max.dynamic.partitions</name>
        <value>1000</value>
    </property>
    <property>
        <name>hive.exec.max.dynamic.partitions.pernode</name>
        <value>100</value>
    </property>
    <!-- HDFS Namenode address -->
    <property>
      <name>fs.defaultFS</name>
      <value>hdfs://hadoop01:9000</value>
      <description>The HDFS namenode URI.</description>
    </property>

    <!-- YARN ResourceManager address -->
    <property>
      <name>yarn.resourcemanager.address</name>
      <value>hadoop01:8032</value>
      <description>The YARN ResourceManager address.</description>
    </property>
</configuration>

```



#### hive02上的hive-site.xml

```
<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
<configuration>
    <!-- metabase server -->
    <property>
        <name>javax.jdo.option.ConnectionURL</name>
        <value>jdbc:mysql://10.10.9.24:3306/hivedb?useUnicode=true&amp;characterEncoding=UTF-8&amp;useSSL=false&amp;createDatabaseIfNotExist=true</value>
    </property>
    <property>
        <name>javax.jdo.option.ConnectionDriverName</name>
        <value>com.mysql.cj.jdbc.Driver</value>
    </property>
    <property>
        <name>javax.jdo.option.ConnectionUserName</name>
        <value>hive</value>
    </property>
    <property>
        <name>javax.jdo.option.ConnectionPassword</name>
        <value>Tlwl@2024</value>
    </property>
    <property>
        <name>hive.metastore.schema.verification</name>
        <value>false</value>
    </property>
    <property>
        <name>hive.cli.print.current.db</name>
        <value>true</value>
    </property>
    <property>
        <name>hive.cli.print.header</name>
        <value>true</value>
    </property>
        <property>
        <name>hive.metastore.uris</name>
        <value>thrift://hive01:9083,thrift://hive02:9083,thrift://hive03:9083</value>
    </property>

    <!-- hiveserver2 -->
    <property>
        <name>hive.server2.thrift.port</name>
        <value>10000</value>
    </property>
    <property>
        <name>hive.server2.thrift.bind.host</name>
        <value>hive02</value>
        <description>hiveserver2 bind hivenode hostname</description>
    </property>

    <property>
        <name>hive.server2.support.dynamic.service.discovery</name>
        <value>true</value>
    </property>
    <property>
        <name>hive.zookeeper.quorum</name>
        <value>hive01:2181,hive02:2181,hive03:2181</value>
    </property>
    <property>
        <name>hive.zookeeper.client.port</name>
        <value>2181</value>
    </property>
    <property>
        <name>hive.server2.zookeeper.namespace</name>
        <value>hiveserver2</value>
    </property>

    <property>
        <name>hive.server2.thrift.client.user</name>
        <value>root</value>
    </property>
    <property>
        <name>hive.server2.thrift.client.password</name>
        <value>Tlwl@7777</value>
    </property>

    <property>
        <name>hive.execution.engine</name>
        <value>mr</value>
        <description>mr|tez|spark,default mr</description>
    </property>

    <!-- for hive user config -->
    <property>
        <name>hive.server2.enable.doAs</name>
        <value>false</value>
        <description>false: yarn job as hive; true: yarn job as real username</description>
    </property>
    <!--
    <property>
        <name>hive.server2.authentication</name>
        <value>NONE</value>
    </property>
    <property>
        <name>hive.security.authorization.enabled</name>
        <value>false</value>
    </property>
    <property>
        <name>hive.security.authorization.manager</name>
        <value>org.apache.hadoop.hive.ql.security.authorization.plugin.sqlstd.SQLStdConfOnlyAuthorizerFactory</value>
    </property>
    -->
    <property>
        <name>hive.users.in.admin.role</name>
        <value>root</value>
        <description>Users in admin role</description>
    </property>
    <property>
        <name>hive.exec.scratchdir</name>
        <value>/user/hive/tmp</value>
        <description>Hive query execution temporary scratch directory.</description>
    </property>
    <!-- 
    <property>
        <name>hadoop.proxyuser.hive.groups</name>
        <value>*</value>
    </property>
    <property>
        <name>hadoop.proxyuser.hive.hosts</name>
        <value>*</value>
    </property>
    -->
    <!-- for support merge into -->
    <property>
        <name>hive.support.concurrency</name>
        <value>true</value>
    </property>
    <property>
        <name>hive.txn.manager</name>
        <value>org.apache.hadoop.hive.ql.lockmgr.DbTxnManager</value>
    </property>
    <property>
        <name>hive.compactor.initiator.on</name>
        <value>true</value>
    </property>
    <property>
        <name>hive.compactor.worker.threads</name>
        <value>1</value>
    </property>
    <property>
        <name>hive.enforce.bucketing</name>
        <value>true</value>
    </property>
    <property>
        <name>hive.enforce.sorting</name>
        <value>true</value>
    </property>
    <property>
        <name>hive.exec.dynamic.partition.mode</name>
        <value>nonstrict</value>
    </property>
    <property>
        <name>hive.exec.max.dynamic.partitions</name>
        <value>1000</value>
    </property>
    <property>
        <name>hive.exec.max.dynamic.partitions.pernode</name>
        <value>100</value>
    </property>
    <!-- HDFS Namenode address -->
    <property>
      <name>fs.defaultFS</name>
      <value>hdfs://hadoop01:9000</value>
      <description>The HDFS namenode URI.</description>
    </property>

    <!-- YARN ResourceManager address -->
    <property>
      <name>yarn.resourcemanager.address</name>
      <value>hadoop01:8032</value>
      <description>The YARN ResourceManager address.</description>
    </property>
</configuration>

```



#### hive03上的hive-site.xml

```
<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
<configuration>
    <!-- metabase server -->
    <property>
        <name>javax.jdo.option.ConnectionURL</name>
        <value>jdbc:mysql://10.10.9.24:3306/hivedb?useUnicode=true&amp;characterEncoding=UTF-8&amp;useSSL=false&amp;createDatabaseIfNotExist=true</value>
    </property>
    <property>
        <name>javax.jdo.option.ConnectionDriverName</name>
        <value>com.mysql.cj.jdbc.Driver</value>
    </property>
    <property>
        <name>javax.jdo.option.ConnectionUserName</name>
        <value>hive</value>
    </property>
    <property>
        <name>javax.jdo.option.ConnectionPassword</name>
        <value>Tlwl@2024</value>
    </property>
    <property>
        <name>hive.metastore.schema.verification</name>
        <value>false</value>
    </property>
    <property>
        <name>hive.cli.print.current.db</name>
        <value>true</value>
    </property>
    <property>
        <name>hive.cli.print.header</name>
        <value>true</value>
    </property>
        <property>
        <name>hive.metastore.uris</name>
        <value>thrift://hive01:9083,thrift://hive02:9083,thrift://hive03:9083</value>
    </property>

    <!-- hiveserver2 -->
    <property>
        <name>hive.server2.thrift.port</name>
        <value>10000</value>
    </property>
    <property>
        <name>hive.server2.thrift.bind.host</name>
        <value>hive03</value>
        <description>hiveserver2 bind hivenode hostname</description>
    </property>

    <property>
        <name>hive.server2.support.dynamic.service.discovery</name>
        <value>true</value>
    </property>
    <property>
        <name>hive.zookeeper.quorum</name>
        <value>hive01:2181,hive02:2181,hive03:2181</value>
    </property>
    <property>
        <name>hive.zookeeper.client.port</name>
        <value>2181</value>
    </property>
    <property>
        <name>hive.server2.zookeeper.namespace</name>
        <value>hiveserver2</value>
    </property>

    <property>
        <name>hive.server2.thrift.client.user</name>
        <value>root</value>
    </property>
    <property>
        <name>hive.server2.thrift.client.password</name>
        <value>Tlwl@7777</value>
    </property>

    <property>
        <name>hive.execution.engine</name>
        <value>mr</value>
        <description>mr|tez|spark,default mr</description>
    </property>

    <!-- for hive user config -->
    <property>
        <name>hive.server2.enable.doAs</name>
        <value>false</value>
        <description>false: yarn job as hive; true: yarn job as real username</description>
    </property>
    <!--
    <property>
        <name>hive.server2.authentication</name>
        <value>NONE</value>
    </property>
    <property>
        <name>hive.security.authorization.enabled</name>
        <value>false</value>
    </property>
    <property>
        <name>hive.security.authorization.manager</name>
        <value>org.apache.hadoop.hive.ql.security.authorization.plugin.sqlstd.SQLStdConfOnlyAuthorizerFactory</value>
    </property>
    -->
    <property>
        <name>hive.users.in.admin.role</name>
        <value>root</value>
        <description>Users in admin role</description>
    </property>
    <property>
        <name>hive.exec.scratchdir</name>
        <value>/user/hive/tmp</value>
        <description>Hive query execution temporary scratch directory.</description>
    </property>
    <!-- 
    <property>
        <name>hadoop.proxyuser.hive.groups</name>
        <value>*</value>
    </property>
    <property>
        <name>hadoop.proxyuser.hive.hosts</name>
        <value>*</value>
    </property>
    -->
    <!-- for support merge into -->
    <property>
        <name>hive.support.concurrency</name>
        <value>true</value>
    </property>
    <property>
        <name>hive.txn.manager</name>
        <value>org.apache.hadoop.hive.ql.lockmgr.DbTxnManager</value>
    </property>
    <property>
        <name>hive.compactor.initiator.on</name>
        <value>true</value>
    </property>
    <property>
        <name>hive.compactor.worker.threads</name>
        <value>1</value>
    </property>
    <property>
        <name>hive.enforce.bucketing</name>
        <value>true</value>
    </property>
    <property>
        <name>hive.enforce.sorting</name>
        <value>true</value>
    </property>
    <property>
        <name>hive.exec.dynamic.partition.mode</name>
        <value>nonstrict</value>
    </property>
    <property>
        <name>hive.exec.max.dynamic.partitions</name>
        <value>1000</value>
    </property>
    <property>
        <name>hive.exec.max.dynamic.partitions.pernode</name>
        <value>100</value>
    </property>
    <!-- HDFS Namenode address -->
    <property>
      <name>fs.defaultFS</name>
      <value>hdfs://hadoop01:9000</value>
      <description>The HDFS namenode URI.</description>
    </property>

    <!-- YARN ResourceManager address -->
    <property>
      <name>yarn.resourcemanager.address</name>
      <value>hadoop01:8032</value>
      <description>The YARN ResourceManager address.</description>
    </property>
</configuration>

```



#### 初始化Hive Metastore数据库-Mysql数据库

在hive01上根据hive-site.xml配置，使用如下命令可以初始化数据库。

```
$ schematool -initSchema -dbType mysql
```

因为sql初始化脚本/soft/hive/scripts/metastore/upgrade/mysql中默认编码是latin1，故需修改字段编码，避免COMMENT字段显示乱码：

```
-- hive 3.1.3/4.0.0
alter table COLUMNS_V2 modify column COMMENT varchar(256) character set utf8;
alter table TABLE_PARAMS modify column PARAM_VALUE varchar(4000) character set utf8;
alter table PARTITION_PARAMS modify column PARAM_VALUE varchar(4000) character set utf8;
alter table PARTITION_KEYS modify column PKEY_COMMENT varchar(4000) character set utf8;
alter table TABLE_PARAMS modify column PARAM_KEY varchar(256) character set utf8;
alter table TABLE_PARAMS modify column PARAM_VALUE varchar(4000) character set utf8;
alter table database_params modify column PARAM_KEY varchar(180) character set utf8;
alter table database_params modify column PARAM_VALUE varchar(4000) character set utf8;
-- hive3.1.3 only
-- alter table INDEX_PARAMS modify column PARAM_VALUE varchar(4000) character set utf8; 
```



### 启动

#### hive01,hive02,hive03

```
#后台启动
cd /soft/hive && HADOOP_OPTS="-Xmx2g" nohup ./bin/hive --service metastore &> /soft/hive/logs/hive-meta.log &
#后台启动server2
cd /soft/hive && HADOOP_OPTS="-Xmx4g" nohup ./bin/hive --service hiveserver2 &> /soft/hive/logs/hiveserver2.log &
```

#### 客户端连接

```
$ beeline
$ !connect jdbc:hive2://hive01,hive02,hive03/;password=Tlwl7777;serviceDiscoveryMode=zooKeeper;zooKeeperNamespace=hiveserver2  root

##### 以下测试建表与查询 #####

CREATE TABLE base(
   id int COMMENT "ID",
   name string COMMENT '姓名',
   address string COMMENT '地址',
   lastmodificationtime timestamp)
    COMMENT '基础表'
  CLUSTERED BY (id) into 5 buckets 
  STORED AS ORC TBLPROPERTIES ('transactional'='true');
  
INSERT INTO TABLE base VALUES
    (1, 'John Doe1', '北京', '2024-07-08 10:10:10'),
    (2, 'John Doe2', '北京', '2024-07-08 10:10:10'),
    (3, 'John Doe3', '北京', '2024-07-08 10:10:10');
```

推荐使用dbeaver作为客户端连接。

#### 使用zookeeper客户端查看节点信息

```
$ cd /soft/zookeeper-3.9.0-bin && ./bin/zkCli.sh -server 127.0.0.1:2181
$ ls /
$ ls /hiveserver2
[serverUri=hive01:10000;version=3.1.3;sequence=0000000022, serverUri=hive02:10000;version=3.1.3;sequence=0000000023, serverUri=hive03:10000;version=3.1.3;sequence=0000000024]
```

#### 调整hive关键参数（按需）

```
参考：
```





## DholphiScheduler集群

参考：https://dolphinscheduler.apache.org/zh-cn/docs/3.2.0/guide/installation/pseudo-cluster

### 免密安装

需保证所有hadoop,hive,ds节点均能免密登录，第一步已经完整，这里不再操作

### 下载

apache-dolphinscheduler-3.2.0-bin.tar.gz，解压到/home/apache-dolphinscheduler-3.2.0-bin

mysql-connector-java-8.0.16.jar，复制到/home/apache-dolphinscheduler-3.2.0-bin/{xxx}-server/libs下

### 初始化数据库

mysql5.7新增数据库dolphinscheduler，初始化/home/apache-dolphinscheduler-3.2.0-bin/tools/sql/sql/dolphinscheduler_mysql.sql脚本数据进入数据库

### 安装

##### 新增用户

创建部署用户，并且一定要**配置 `sudo` 免密**。以创建 dolphinscheduler 用户为例

```
# 创建用户需使用 root 登录
useradd dolphinscheduler

# 添加密码
echo "dolphinscheduler" | passwd --stdin dolphinscheduler

# 配置 sudo 免密
sed -i '$adolphinscheduler  ALL=(ALL)  NOPASSWD: NOPASSWD: ALL' /etc/sudoers
sed -i 's/Defaults    requirett/#Defaults    requirett/g' /etc/sudoers

# 修改目录权限，使得部署用户对二进制包解压后的 apache-dolphinscheduler-*-bin 目录有操作权限
chown -R dolphinscheduler:dolphinscheduler apache-dolphinscheduler-*-bin
chmod -R 755 apache-dolphinscheduler-*-bin
```

##### 修改配置

```
cd /home/apache-dolphinscheduler-3.2.0-bin/bin/env
```

修改 `dolphinscheduler_env.sh`

```
# applicationId auto collection related configuration, the following configurations are unnecessary if setting appId.collect=log
#export HADOOP_CLASSPATH=`hadoop classpath`:${DOLPHINSCHEDULER_HOME}/tools/libs/*
#export SPARK_DIST_CLASSPATH=$HADOOP_CLASSPATH:$SPARK_DIST_CLASS_PATH
#export HADOOP_CLIENT_OPTS="-javaagent:${DOLPHINSCHEDULER_HOME}/tools/libs/aspectjweaver-1.9.7.jar":$HADOOP_CLIENT_OPTS
#export SPARK_SUBMIT_OPTS="-javaagent:${DOLPHINSCHEDULER_HOME}/tools/libs/aspectjweaver-1.9.7.jar":$SPARK_SUBMIT_OPTS
#export FLINK_ENV_JAVA_OPTS="-javaagent:${DOLPHINSCHEDULER_HOME}/tools/libs/aspectjweaver-1.9.7.jar":$FLINK_ENV_JAVA_OPTS


# JAVA_HOME, will use it to start DolphinScheduler server
export JAVA_HOME=${JAVA_HOME:-/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.412.b08-1.el7_9.x86_64}

# Database related configuration, set database type, username and password
export DATABASE=${DATABASE:-mysql}
export SPRING_PROFILES_ACTIVE=${DATABASE}
export SPRING_DATASOURCE_URL="jdbc:mysql://10.10.9.24:3306/dolphinscheduler?useUnicode=true&characterEncoding=UTF-8&useSSL=false"
export SPRING_DATASOURCE_USERNAME="dolphinscheduler"
export SPRING_DATASOURCE_PASSWORD="Tlwl@2024"

# DolphinScheduler server related configuration
export SPRING_CACHE_TYPE=${SPRING_CACHE_TYPE:-none}
export SPRING_JACKSON_TIME_ZONE=${SPRING_JACKSON_TIME_ZONE:-UTC}

# Registry center configuration, determines the type and link of the registry center
export REGISTRY_TYPE=${REGISTRY_TYPE:-zookeeper}
export REGISTRY_ZOOKEEPER_CONNECT_STRING=${REGISTRY_ZOOKEEPER_CONNECT_STRING:-hive01:2181,hive02:2181,hive03:2181}

# Tasks related configurations, need to change the configuration if you use the related tasks.
export HADOOP_HOME=${HADOOP_HOME:-/soft/hadoop-3.2.4}
export HADOOP_CONF_DIR=${HADOOP_CONF_DIR:-/soft/hadoop-3.2.4/etc/hadoop}
export SPARK_HOME=${SPARK_HOME:-/opt/soft/spark}
export PYTHON_LAUNCHER=${PYTHON_LAUNCHER:-/soft/python38/bin/python3}
export HIVE_HOME=${HIVE_HOME:-/soft/hive}
export FLINK_HOME=${FLINK_HOME:-/opt/soft/flink}
export DATAX_LAUNCHER=${DATAX_LAUNCHER:-/soft/datax/bin/datax.py}

export PATH=$HADOOP_HOME/bin:$SPARK_HOME/bin:$PYTHON_LAUNCHER:$JAVA_HOME/bin:$HIVE_HOME/bin:$FLINK_HOME/bin:$DATAX_LAUNCHER:$PATH
```

修改`install_env.sh`

```
# ---------------------------------------------------------
# INSTALL MACHINE
# ---------------------------------------------------------
# A comma separated list of machine hostname or IP would be installed DolphinScheduler,
# including master, worker, api, alert. If you want to deploy in pseudo-distributed
# mode, just write a pseudo-distributed hostname
# Example for hostnames: ips="ds1,ds2,ds3,ds4,ds5", Example for IPs: ips="192.168.8.1,192.168.8.2,192.168.8.3,192.168.8.4,192.168.8.5"
ips=${ips:-"ds01,ds02,ds03"}

# Port of SSH protocol, default value is 22. For now we only support same port in all `ips` machine
# modify it if you use different ssh port
sshPort=${sshPort:-"22"}

# A comma separated list of machine hostname or IP would be installed Master server, it
# must be a subset of configuration `ips`.
# Example for hostnames: masters="ds1,ds2", Example for IPs: masters="192.168.8.1,192.168.8.2"
masters=${masters:-"ds01"}

# A comma separated list of machine <hostname>:<workerGroup> or <IP>:<workerGroup>.All hostname or IP must be a
# subset of configuration `ips`, And workerGroup have default value as `default`, but we recommend you declare behind the hosts
# Example for hostnames: workers="ds1:default,ds2:default,ds3:default", Example for IPs: workers="192.168.8.1:default,192.168.8.2:default,192.168.8.3:default"
workers=${workers:-"ds03:default,ds02:default,ds01:default"}

# A comma separated list of machine hostname or IP would be installed Alert server, it
# must be a subset of configuration `ips`.
# Example for hostname: alertServer="ds3", Example for IP: alertServer="192.168.8.3"
alertServer=${alertServer:-"ds02"}

# A comma separated list of machine hostname or IP would be installed API server, it
# must be a subset of configuration `ips`.
# Example for hostname: apiServers="ds1", Example for IP: apiServers="192.168.8.1"
apiServers=${apiServers:-"ds03"}

# The directory to install DolphinScheduler for all machine we config above. It will automatically be created by `install.sh` script if not exists.
# Do not set this configuration same as the current path (pwd). Do not add quotes to it if you using related path.
installPath=${installPath:-"/soft/dolphinscheduler"}

# The user to deploy DolphinScheduler for all machine we config above. For now user must create by yourself before running `install.sh`
# script. The user needs to have sudo privileges and permissions to operate hdfs. If hdfs is enabled than the root directory needs
# to be created by this user
deployUser=${deployUser:-"dolphinscheduler"}

# The root of zookeeper, for now DolphinScheduler default registry server is zookeeper.
# It will delete ${zkRoot} in the zookeeper when you run install.sh, so please keep it same as registry.zookeeper.namespace in yml files.
# Similarly, if you want to modify the value, please modify registry.zookeeper.namespace in yml files as well.
zkRoot=${zkRoot:-"/dolphinscheduler"}
```

执行安装脚本，复制程序到ds01,ds02,ds03的/soft/dolphinscheduler，并会根据`install_env.sh`的配置在每个节点启动响应的进程。

```
cd /home/apache-dolphinscheduler-3.2.0-bin/bin/
./install.sh
```

##### 访问地址

ApiApplicationServer部署在ds03，故访问地址为：http://10.10.9.20:12345/dolphinscheduler/ui/

用户/密码:admin/dolphinscheduler123

### 其他

复制datax到ds01,ds02,ds03的/soft/datax

复制hive01上的/soft/hive到ds01,ds02,ds03的/soft/hive; 

```
`注意`：hive-site.xml中的hive.server2.thrift.bind.host不影响ds01的调度,因为ds与hive隔离部署，ds节点上没有hiveserver2，此参数不会导致
```



复制hadoop01上的/soft/hadoop3.2.4到ds01,ds02,ds03的/soft/hadoop3.2.4