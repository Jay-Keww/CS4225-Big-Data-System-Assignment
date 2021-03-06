# CS4225-Data-System-Assignment
CS4225 big data system for data science, Hadoop and Spark

## Assignment 1 - Hadoop
### Setting up IDE on windows
Note: The version I use for this assignment is: Java 11, Hadoop 2.9
* Download binary file from Hadoop and extract into 
```
C:\Program Files\
```
* Set the Environment variables:
<br/> Add to user variable
<br/> `HADOOP_BIN_PATH` : `%HADOOP_HOME%\bin`
<br/> `HADOOP_HOME` : `E:\soft\hadoop-2.7.4` (your Hadoop’s installation path)
<br/> `JAVA_HOME`: `C:\Program Files\Java\jdk-11.0.5`
<br/> Add to system variable Path
<br/> `%HADOOP_HOME%\bin` and `%HADOOP_HOME%\sbin`

* Download the files necessary for Windows(such as winutils.exe, hadoop.dll) from [this link](https://github.com/cdarlint/winutils)
<br/> find the version of your hadoop and put the files into `C:\Program Files\hadoop-2.9.2\bin`

* Setting up intellij
Please follow the part in [doc](https://github.com/CoderStellaJ/CS4225-Data-System/blob/master/setup_IDE_win_macos_linux.docx)

### Build cluster with docker
* Install Docker Toolbox or Docker Desktop
* Docker pull image
<br/> Open Docker Quickstart Terminal
<br/> `docker pull nusbigdatacs4225/ubuntu-with-hadoop-spark`
* Docker create containers
<br/> `docker run -it -h master --name master nusbigdatacs4225/ubuntu-with-hadoop-spark`
<br/> `ctrl+p` then `ctrl+q`
<br/> `docker run -it -h slave01 --name slave01 nusbigdatacs4225/ubuntu-with-hadoop-spark`
<br/> `ctrl+p` then `ctrl+q`
<br/> `docker run -it -h slave02 --name slave02 nusbigdatacs4225/ubuntu-with-hadoop-spark`
<br/> `ctrl+p` then `ctrl+q`
<br/> check the containers in docker: `docker ps -a`
* Configure nodes
<br/> Enter and execute a node: `docker exec -it [nodename] bash`
<br/> `vim /etc/hosts` 
<br/> add the following to all 3 nodes
```
172.17.0.2	master
172.17.0.3	slave01
172.17.0.4	slave02
```
Enter master node: `docker exec -it master bash`
<br/> `ssh slave01` then `yes` and type `ssh slave02` then `yes`
<br/> Enter slave01 and slave02 nodes and use `ssh` to connect them
* Configure master node
<br/> Enter master node
<br/> `cd /usr/local/hadoop/etc/hadoop` and `vim slaves`, delete `localhost` and add `slave01 slave02` into this file
* Build cluster
<br/> Enter master node
<br/> add `export PATH=$PATH:/usr/local/hadoop/bin/` in `~/.bashrc` 
<br/> run command `source ~/.bashrc`
```
cd /usr/local/hadoop
bin/hdfs namenode -format
sbin/start-all.sh
```
To stop your nodes: `sbin/stop-all.sh`
<br/> To let namenode leave safe mode: 
```
cd /usr/local/hadoop
bin/hadoop dfsadmin -safemode leave
```

### Create your jar file
* use JDK 8, otherwise hadoop doesn't support JDK11 version
* Project Structure -> Artifacts -> Copy to the output directory and link via manifest
 
### Run your application
* Create input and output folders
<br/> Enter master node `docker exec -it master bash`
```
mkdir input
echo "Hello World" >input/f1.txt
echo "Hello Docker" >input/f2.txt
```
Create input directory in HDFS: `hadoop fs -mkdir -p input`
<br/> Put the input files to all the datanodes on HDFS: `hdfs dfs -put ./input/* input`

* Put your application into cluster
<br/> put jar file to `C:\Program Files\Docker Toolbox\program\`
<br/> `docker cp ./program/xxxx.jar [container id of master]:xxxx.jar`
<br/> Run your application: `hadoop jar xxxx.jar org.apache.hadoop.examples.WordCount input output`
<br/> print out results: `hdfs dfs -cat output/part-r-00000`

### Others
* Difference between `docker attach` and `docker exec`?
<br/> If we use `docker attach`, we can use only one instance of shell.
<br/> So if we want to open new terminal with new instance of container's shell, we just need to run docker exec
<br/> When a container is started using /bin/bash then it becomes the containers PID 1 and docker attach is used to get inside PID 1 of a container. So docker attach < container-id > will take you inside the bash terminal as it's PID 1 as we mentioned while starting the container. Exiting out from the container will stop the container.
<br/> Whereas in docker exec command you can specify which shell you want to enter into. It will not take you to PID 1 of the container. It will create a new process for bash. docker exec -it < container-id > bash. Exiting out from the container will not stop the container.  

### References
https://clubhouse.io/developer-how-to/how-to-set-up-a-hadoop-cluster-in-docker/

## Assignment 2 - Spark
Note: **JDK 8, sbt 1.3.4, Scala 2.11.12, Spark 2.3.0**

### Set up Scala and sbt
Follow the instruction from this [link](https://kaizen.itversity.com/setup-development-environment-intellij-and-scala-big-data-hadoop-and-spark/)
* Add scala plugin from Intellij
* Download sbt and add to environment variable path: `C:\Program Files (x86)\sbt\bin`
<br/> The version I use is 1.3.4

### Set up Intellij
* Create a new project in Intellij
<br/> Choose `Scala` and `sbt`
<br/> Select proper versions. I use JDK 11, sbt 1.3.4, Scala 2.11.12
* Right click on your project directory, add framework support, choose Scala
* Go to src/main/scala, right click and create a new scala class

### Validate Scala and sbt
* You can run the scala code in Intellij directly
* open cmd, navigate to your project folder, you should see `build.sbt` here
* run `sbt package` to build a jar file which is located in target folder
* run `sbt run` to run the program

### Add Spark to Intellij
* Add `libraryDependencies += "org.apache.spark" %% "spark-core" % "2.3.0" ` to `build.sbt`

### Install Spark on your computer (Optional)
* Download Spark from Spark official website
You can follow the instructions from https://medium.com/big-data-engineering/how-to-install-apache-spark-2-x-in-your-pc-e2047246ffc3
<br/>The version I use for this assignment: Spark 2.4.5 with Hadoop 2.7
* Download the winutils (check the version) and put it to `C:\Program Files\spark-2.4.5-bin-hadoop2.7\bin`
* Add `SPARK_HOME` to environment variable. e.g. `C:\Progra~1\spark-2.4.5-bin-hadoop2.7`
<br/> Note: should use `Progra~1` instead of `Program Files` because space is not accepted.
* Modify or add `HADOOP_HOME` to to environment varibale. e.g. `C:\Progra~1\spark-2.4.5-bin-hadoop2.7`
* To check whether you have successfully installed: 
<br/> open `cmd` and navigate to `C:\Program Files\spark-2.4.5-bin-hadoop2.7\bin`
<br/> run `spark-shell`

