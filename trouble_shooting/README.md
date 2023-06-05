- WordCount example:
    > https://hadoop.apache.org/docs/stable/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html#Example:_WordCount_v1.0

- Can we write the mapper and reducer script in python instead of java?
    > the purpose of this project is to help you to understand the fundamentals of the Hadoop MapReduce framework which is required to be written in Java without any other program languages API or toolkit support.


- Total revenue = ?
    > Think about this in this way, the sum of a column of ave price per room only reflects one person who books one room for one night, which should not be the true case since there will be more than one group who books several rooms for more than one day.
    Besides that, if nobody makes bookings in that month, would the revenue of the hotel for that month still increase? 
    Therefore, **the total revenue for a given month is the avg_price_per_room times number of stay_in_weekend/week_nights for that given month, per booking record**.
    Keep in mind, you also need to consider canceled bookings.

- Cannot compile jar file
    >Due to the configuration. Please take a look at your `/libexec/etc/hadoop/hadoop-env.sh` and make sure you take this configuration before compiling and running your Java code on Hadoop:
    ```Java
    export JAVA_HOME=/usr/java/default
    export PATH=${JAVA_HOME}/bin:${PATH}
    export HADOOP_CLASSPATH=${JAVA_HOME}/lib/tools.jar
    ```

    >`JAVA_HOME` override in zshrc that causes Java 20 to be set, even when I had it set to Java 8 in my Hadoop-env.sh

    > The `JAVA_HOME` should be already set in the installation setps, you also need to add the following two lines, which might be the reason for your /bin/java error. 
- Problems on Windows:

        C:\>hadoop version 

        The system cannot find the path specified.
        Error: JAVA_HOME is incorrectly set.
        Please update C:\hadoop-2.5.1\conf\hadoop-env.cmd '-Xmx512m' is not recognized as an internal or external command, ooperable program or batch file.
    
    > This link seems have similar problems:
    https://stackoverflow.com/questions/26990243/hadoop-installation-on-windows

    > In case it doesn't work, to save some time, WSL might be helpful when you are using Windows OS. Linux tutorial doesn't create such problems. Please search instruction of installing WSL, both 18.04 or 20.04 works perfectly fine. Once you are able to install WSL, please forward the following link as an instruction of installing and configuring Hadoop:
    https://phoenixnap.com/kb/install-hadoop-ubuntu#ftoc-heading-1
- Hints on how to merge the data:
    > To merge the dataset, think about you need to calculate the revenue using different attributes, such as price and stay time, and you need to consider the revenue of one month of different years(from different datasets) to get the revenues over 4 years. The merge should be achieved by Mapper and Reducer, and you could refer to the wordCount example on Hadoop MapReduce official documentation to get familiar with that. Noted because the contents of same attributes from both datasets are not exactly the same, so there are some conversion from one to the other as well.

    > **There are a lot merging/join methods** (Map-side Join Operation: the join is performed by the mapper. Here, the join is performed before the data could be consumed by the actual map function. This type of Join has the prerequisite that it requires the input given to the map to be in the form of a partition and all such inputs should be in the sorted order. The equal partitions must be sorted by the join key. Reduce-side Join Operations: the join is performed by the Reducer. Such type of join does not desire to have a dataset in a partitioned or structured form. Actually, the map side joins processing produces join key as well as the associated corresponding tuples from both of the tables. Therefore, all the tuples which have the same key fall into the same reducer, they are joined to form the output records) **available which you will need to compare and finally choose one, also tell me the reason why you choose it and the tradeoffs between different merging methods. That's all hints I can share with you, please try your best and explore the rest.**

- How to **rank**?

    > https://stackoverflow.com/questions/42092170/how-to-find-one-specific-key-value-pair-as-output-from-reducer
    to rank the key with respect to the value associated with keys.
    > As I remember in the output of the MapReduce you will get key-value pairs, therefore you should be able to get access to the value based on the key, then finding the key with max value should be possible (just similar to how to sort an array).

- Typing `jps`, no `namenode` shows
    > 1. ./stop-all.sh then type jps (checking if everything is shut down, the expected output should be a single line shows some number followed by jps, and another number in a separate line)
    > 2. Remove both directories:
        
        /tmp/hadoop-liam 
        /tmp/hadoop-yarn*
    > 3. Go back to /libexec/etc/hadoop, and double check if you have configured all of them correctly.
    > 4. Follow the formatting namenode and /.start-XX.sh steps on the instruction.
    > 5. type jps command on your terminal, check if "namenode" appears. Send me the screenshot if no namenode appears on the output.
    > 6. go to /tmp/hadoop-liam/dfs, and check whether there is a directory named "name" or "namenode".
