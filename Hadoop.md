# Setup Hadoop on Windows 10 PC

## Prerequisites:
1. Windows 10 machine with administrative privileges.
2. Java Development Kit (JDK) installed.
3. 7-Zip - to extract the binary package.
4. Downloaded Hadoop distribution from the official website.

## Step 1 - Configure environment variables

Open the Start Menu and type in 'environment' and press enter. A new window with System Properties should open up. Click the `Environment Variables` button near the bottom right.

### `JAVA_HOME` environment variable
1. Find the location of where you installed Java and copy the path
2. Create a new **User variable** with the variable name as `JAVA_HOME` and paste the Java installation path as the value.

### `HADOOP_HOME` environment variable
1. Copy the directory you extracted the Hadoop binaries to.
2. Create a new **User variable** with the variable name as `HADOOP_HOME` and paste the Hadoop download path as the value.

### `PATH` environment variable 
We will now need to add the bin folders to the `PATH` environment variable.
1. Click `Path` then `Edit`
2. Click `New` on the top right
3. Add `%hadoop-3.2.1\bin`
4. Add `%hadoop-3.2.1\sbin`
5. Add `%jdk11\bin`

### Hadoop environment
In the default installation directory, `Program Files` has a space which is problematic to Hadoop configuration. To fix this, open the `%HADOOP_HOME%\etc\hadoop\hadoop-env.cmd` and change the `JAVA_HOME` line to the following:
```
set JAVA_HOME=C:\PROGRA~1\Java\jdk11
```

After setting those environment variables, you open CMD and verify that the `hadoop` command is available:
```
$ hadoop -version

## Step 2 - Configure Hadoop
Now we are ready to configure the **most important part** - Hadoop configurations which involves Core, YARN, MapReduce, and HDFS configurations.

Each of the files are in `%HADOOP_HOME%\etc\hadoop`.

### Configure core-site
Edit `core-site.xml` and replace the `configuration` element with the following:
```
<configuration>
  <property>
    <name>fs.default.name</name>
    <value>hdfs://localhost:9000</value>
  </property>
</configuration>
```

### Configure HDFS
Create two folders, one for the namenode directory and another for the datanode directory. The following are the two created folders in this example:

1. `%hadoop-3.2.1\data\namenode`
2. `%hadoop-3.2.1\data\datanode`

Edit `hdfs-site.xml` and replace the `configuration` element with the following:
```
<configuration>
  <property>
    <name>dfs.replication</name>
    <value>1</value>
  </property>
  <property>
    <name>dfs.namenode.name.dir</name>
    <value>%hadoop-3.2.1\data\namenode</value>
  </property>
  <property>
    <name>dfs.datanode.data.dir</name>
    <value>%hadoop-3.2.1\data\datanode</value>
  </property>
</configuration>
```

### Configure MapReduce
Edit `mapred-site.xml` and replace the `configuration` element with the following:
```
<configuration>
  <property>
    <name>mapreduce.framework.name</name>
    <value>yarn</value>
  </property>
</configuration>
```

### Configure YARN site
Edit `yarn-site.xml` and replace the `configuration` element with the following:
```
<configuration>
  <property>
    <name>yarn.resourcemanager.hostname</name>
    <value>localhost</value>
  </property>
  <property>
    <name>yarn.nodemanager.aux-services</name>
    <value>mapreduce_shuffle</value>
  </property>
  <property>
    <name>yarn.nodemanager.auxservices.mapreduce.shuffle.class</name>
    <value>org.apache.hadoop.mapred.ShuffleHandler</value>
  </property>
</configuration>
```

## Step 3 - Install Hadoop native IO binary
To be able to successfully run Hadoop on Windows, we need to download the [`hadoop-3.2.1`](https://github.com/ogbenisho/sho/blob/main/hadoop-3.2.1.zip) and paste the contents of `hadoop-3.2.1/bin` into the extracted location of the Hadoop binary package.

## Step 4 - Format namenode
Run the following command to format namenode
```
$ hdfs namenode -format
```

## Step 5 - Start Hadoop Cluster
Run the following command to start HDFS daemons. When you do so, there should be two new windows that open: one for datanode and the other for namenode:

```
$ cd %HADOOP_HOME%\sbin
$ start-dfs.cmd
```
To check the running status, run the following command:
```
$jps
```

## Step 6 - Start YARN daemons
Run the following command (with admin permissions) to start YARN daemons. When you do so, there should be two new windows that open: one for resource manager and the other for node manager:
```
$ %HADOOP_HOME%\sbin\start-yarn.cmd
```
To check the running status, run the following command:
```
$jps
```

## Step 7 - Check if the cluster is running
To check, go to your browser and paste the following IPs

### HDFS Namenode UI info
[http://localhost:9870/dfshealth.html#tab-overview](http://localhost:9870/dfshealth.html#tab-overview)

### YARN resource manager UI
[http://localhost:8088](http://localhost:8088)

### HDFS Namenode UI info
[http://localhost:9870/dfshealth.html#tab-overview](http://localhost:9870/dfshealth.html#tab-overview)

### HDFS Datanode UI info
[http://localhost:9864/datanode.html](http://localhost:9864/datanode.html)

## Step 8 - Shutdown Hadoop
You can stop the daemons by running the following commands:
```
$ %HADOOP_HOME%\sbin\stop-dfs.cmd
$ %HADOOP_HOME%\sbin\stop-yarn.cmd
```











