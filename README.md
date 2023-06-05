# installing-hadoop-onMac
Instruction about installing Hadoop on Mac specifically

## Install HomeBrew
Copy the command at the top of the page and paste into a new terminal window. You will be notified of what will be installed. Pressing RETURN initiates the process:

> \$ /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

Confirm you have the correct version of java (version 8) on your machine. If it returns anything other than 1.8., be sure to install the correct version.

> \$ java -version

> \$ brew cask install homebrew/cask-versions/adoptopenjdk8

if `adoptopenjdk8` exists on your machine, the following warning will pop up:

    Warning: Cask 'adoptopenjdk8' is already installed.
To solve this problem, first check all Java virtual machines are installed on your local computer by using this command:
> /usr/libexec/java_home -V

    Matching Java Virtual Machines (5):

    18.0.2 (arm64) "Oracle Corporation" - "Java SE 18.0.2" /Library/Java/JavaVirtualMachines/jdk-18.0.2.jdk/Contents/Home
    11.0.12 (x86_64) "Microsoft Build of OpenJDK" - "Microsoft Build of OpenJDK 11" /Library/Java/JavaVirtualMachines/microsoft-11.jdk/Contents/Home
    1.8.341.10 (x86_64) "Oracle Corporation" - "Java" /Library/Internet Plug-Ins/JavaAppletPlugin.plugin/Contents/Home
    1.8.0_302 (x86_64) "Eclipse Temurin" - "Eclipse Temurin 8" /Library/Java/JavaVirtualMachines/temurin-8.jdk/Contents/Home
    1.8.0_292 (x86_64) "AdoptOpenJDK" - "AdoptOpenJDK 8" /Library/Java/JavaVirtualMachines/adoptopenjdk-8.jdk/Contents/Home
    /Library/Java/JavaVirtualMachines/jdk-18.0.2.jdk/Contents/Home
Since Hadoop is only compitible with Java8, you need to switch your default JAVA version to Java8 (1.8.x)
> \$ export JAVA_HOME=`/usr/libexec/java_home -v 1.8`  

Once checking correct Java version is being used, you can move forward to the next step


## Install Hadoop
Install the most current version of Hadoop at the path: `/opt/homebrew/Cellar/hadoop/3.3.4/libexec/share/hadoop/` This happens to be 3.3.4 at the time of the writing of this article:

> \$ brew install hadoop

## Configure Hadoop
Configuring Hadoop will take place over a few steps.

Find the directoy where Hadoop is installed:
> \$ hadoop version 

### Updating the environment variable settings
Open the directory above containing the environment variable settings:

> \$ cd /opt/homebrew/Cellar/hadoop/3.3.4/libexec/etc/hadoop/  
> \$ vim hadoop-env.sh

* Make the following changes to the document, **save and close**.

    Add the location for export `JAVA_HOME`

    ```export JAVA_HOME= “/Library/Java/JavaVirtualMachines/adoptopenjdk-8.jdk/Contents/Home”```

    You can find this path by using the following code in the terminal window:

    > \$ /usr/libexec/java_home

* Replace information for export `HADOOP_OPTS`
    
    Change export HADOOP_OPTS=”-Djava.net.preferIPv4Stack=true”

    ```export HADOOP_OPTS = ”-Djava.net.preferIPv4Stack=true -Djava.security.krb5.realm= -Djava.security.krb5.kdc=”```
* Make changes to core files
```
$ vim core-site.xml

<configuration>
  <property>
    <name>fs.defaultFS</name>
    <value>hdfs://localhost:9000</value>
  </property>
</configuration>
```

* Make changes to hdfs files
```
$ vim hdfs-site.xml

<configuration>
  <property>
    <name>dfs.replication</name>
    <value>1</value>
  </property>
</configuration>
```

* Make changes to mapred files
```
$ vim mapred-site.xml
<configuration>
  <property>
    <name>mapreduce.framework.name</name>
    <value>yarn</value>
  </property>
  <property>
    <name>mapreduce.application.classpath</name>   <value>$HADOOP_MAPRED_HOME/share/hadoop/mapreduce/*:$HADOOP_MAPRED_HOME/share/hadoop/mapreduce/lib/*</value>
  </property>
</configuration>
```

* Make changes to yarn files
```
$ vim yarn-site.xml

<configuration>
<property>
<name>yarn.nodemanager.aux-services</name>
<value>mapreduce_shuffle</value>
</property>
<property>
<name>yarn.nodemanager.env-whitelist</name>
<value>JAVA_HOME,HADOOP_COMMON_HOME,HADOOP_HDFS_HOME,HADOOP_CONF_DIR,CLASSPATH_PREPEND_DISTCACHE,HADOOP_YARN_HOME,HADOOP_MAPRED_HOME</value>
</property>
</configuration>
```

## Remove password requirement
Check if you’re able to `ssh` without a password before moving to the next step to prevent unexpected results when formatting the NameNode.

> \$ ssh localhost

If this does not return a last login time, use the following commands to remove the need to insert a password.

> \$ ssh-keygen -t rsa -P '' -f ~/.ssh/id_rsa

```
Generating public/private rsa key pair.
Your identification has been saved in /Users/usrname/.ssh/id_rsa
Your public key has been saved in /Users/usrname/.ssh/id_rsa.pub
The key fingerprint is:
SHA256:mH***********oeU/fpE cbq@CBQ.lan
The key's randomart image is:
+---[RSA 3072]----+
|        .o ..o +=|
|        *.o o O.o|
...................
...................
...................
|                 |
+----[SHA256]-----+
```

> \$ cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys

> \$ chmod 0600 ~/.ssh/authorized_keys

## Format NameNode
> \$ cd /opt/homebrew/Cellar/hadoop/3.3.4/libexec/bin 

> \$ hdfs namenode -format

A warning will tell you that a directory for logs is being created. You will be prompted to re-format filesystem in Storage Directory root. Say Y and press RETURN.

## Run Hadoop
> \$ cd /opt/homebrew/Cellar/hadoop/3.3.4/libexec/sbin 

> \$ ./start-all.sh

> \$ jps

After running `jps`, you should have confirmation that all the parts of Hadoop have been installed and running. You should see something like this:

```
66896 ResourceManager
66692 SecondaryNameNode
66535 DataNode
67350 Jps
66422 NameNode67005 NodeManager
```

Open a web browser to see your configurations for the current session.

http://localhost:9870


## Close Hadoop
Close Hadoop when you are all done.

> \$ ./stop-all.sh
