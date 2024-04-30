# Setup Hadoop on Windows 10 PC

## Prerequisites:
1. Windows administrative privileges.
2. Java Development Kit (JDK) installed. [`Java 11`] (https://www.oracle.com/ng/java/technologies/downloads/#java11-windows)
3. 7-Zip - to extract the Hadoop binary package. [`7-Zip`] (https://www.7-zip.org/download.html)
4. Downloaded Hadoop distribution from the official website. [`hadoop-3.2.1`] (https://www.apache.org/dyn/closer.cgi/hadoop/common/hadoop-3.2.1/hadoop-3.2.1.tar.gz)

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
3. Add `C:\Program Files\Java\jdk-11\bin`
4. Add `C:\Hadoop\bin`
5. Add `C:\Hadoop\bin`

### Hadoop environment
In the default installation directory, `Program Files` has a space which is problematic to Hadoop configuration. To fix this, open the `C:\Hadoop\etc\hadoop\hadoop-env.cmd` and change the `JAVA_HOME` line to the following:
```
set JAVA_HOME=C:\PROGRA~1\Java\jdk-11
```

After setting those environment variables, you open CMD and verify that the `hadoop` command is available:
```
$ hadoop -version
```

## Step 2 - Configure Hadoop
Now we are ready to configure the **most important part** - Hadoop configurations involves the Core, YARN, MapReduce, and HDFS configurations.

Each of these files are situated in `C:\Hadoop\etc\hadoop`.

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

1. `C:\Hadoop\data\dfs\namenode`
2. `C:\Hadoop\data\dfs\datanode`

Edit `hdfs-site.xml` and replace the `configuration` element with the following:
```
<configuration>
  <property>
    <name>dfs.replication</name>
    <value>1</value>
  </property>
  <property>
    <name>dfs.namenode.name.dir</name>
    <value>file:///C:/Hadoop/data/dfs/namenode</value>
  </property>
  <property>
    <name>dfs.datanode.data.dir</name>
    <value>file:///C:/Hadoop/data/dfs/datanode</value>
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
  <property>
    <name>yarn.nodemanager.hostname</name>
    <value>localhost</value>
  </property>
</configuration>
```

## Step 3 - Install Hadoop native IO binary
To be able to successfully run Hadoop on Windows, we need to download the [`hadoop-3.2.1`](https://github.com/ogbenisho/sho/blob/main/hadoop-3.2.1.zip) and paste the contents of `hadoop-3.2.1/bin` into the extracted location of the Hadoop binary package.

## Step 4 - Initialize HDFS and bugfix
Run the following command and you should find the following error:
```
$ hdfs namenode -format
...
ERROR namenode.NameNode: Failed to start namenode.
...
```
To fix this, you'll need to [download a JAR file with the fix](https://github.com/FahaoTang/big-data/blob/master/hadoop-hdfs-3.2.1.jar). Overwrite the existing `hadoop-hdfs-3.2.1.jar` in `C:\Hadoop\share\hadoop\hdfs` with this new JAR file (you can make a backup of the current one before overwriting if you wish).

## Step 5 - Start Hadoop Cluster
Run the following command to start HDFS daemons. When you do so, there should be two new windows that open: one for datanode and the other for namenode:

```
$ cd C:\Hadoop\sbin
$ start-dfs.cmd
```
To check the running status, run the following command:
```
$ jps
```

## Step 6 - Start YARN daemons
Run the following command (with admin permissions) to start YARN daemons. When you do so, there should be two new windows that open: one for resource manager and the other for node manager:
```
$ C:\Hadoop\sbin\start-yarn.cmd
```
To check the running status, run the following command:
```
$ jps
```

## Step 7 - Check if the cluster is running
To check, go to your browser and paste the following IPs

### HDFS Namenode UI info
[http://localhost:9870/dfshealth.html#tab-overview](http://localhost:9870/dfshealth.html#tab-overview)

### YARN resource manager UI
[http://localhost:8088](http://localhost:8088)

### HDFS Datanode UI info
[http://localhost:9864/datanode.html](http://localhost:9864/datanode.html)

## Step 8 - Shutdown Hadoop
You can stop the daemons by running the following commands:
```
$ C:\Hadoop\sbin\stop-dfs.cmd
$ C:\Hadoop\sbin\stop-yarn.cmd
```

Thank you
