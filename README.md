# Docker running Hadoop

Started using Git Bash as my Command Line

1. Make a working directory
   ```
     mkdir hadoop
     cd hadoop 
   ```

2. Clone the git hub
   ```
    git clone https://github.com/big-data-europe/docker-hadoop.git
    cd docker-hadoop
   ```
   
3. Started my docker and double checked it was running correctly
   ```
   docker run hello-world 
   ```
   
4. Composed the Hadoop containers 
   ```
   docker-compose up -d
   ```

5. Check the containers are up and what the names are
   ```
   docker ps
   ```
This was my output
   
   ```
   CONTAINER ID   IMAGE                                                    COMMAND                  CREATED       STATUS                 PORTS                                            NAMES
   181c1598b155   bde2020/hadoop-namenode:2.0.0-hadoop3.2.1-java8          "/entrypoint.sh /run…"   2 hours ago   Up 2 hours (healthy)   0.0.0.0:9000->9000/tcp, 0.0.0.0:9870->9870/tcp   namenode
   9b61e7d71ecb   bde2020/hadoop-historyserver:2.0.0-hadoop3.2.1-java8     "/entrypoint.sh /run…"   2 hours ago   Up 2 hours (healthy)   8188/tcp                                         historyserver
   1f6639e56b6b   bde2020/hadoop-nodemanager:2.0.0-hadoop3.2.1-java8       "/entrypoint.sh /run…"   2 hours ago   Up 2 hours (healthy)   8042/tcp                                         nodemanager
   f15a802e1845   bde2020/hadoop-datanode:2.0.0-hadoop3.2.1-java8          "/entrypoint.sh /run…"   2 hours ago   Up 2 hours (healthy)   9864/tcp                                         datanode
   02ad9cb7a5d8   bde2020/hadoop-resourcemanag
   ```
Here I had to change my command line to PowerShell because in git I keep getting this error when trying to run the next step
   ```
   OCI runtime exec failed: exec failed: unable to start container process: exec: "C:/Program Files/Git/usr/bin/bash": stat C:/Program Files/Git/usr/bin/bash: no such file or directory: unknown
   ```
6. Start an interactive Bash shell session within the nodename container

   ```
   docker exec -it namenode /bin/bash
   ```

7. Need to set up the node

   ```
   mkdir app
   cd app
   mkdir data
   mkdir res
   mkdir jars
   ```

8. Get text to run word count on

   ```
   cd data
   curl https://www.gutenberg.org/cache/epub/1342/pg1342.txt -o austen.txt
   curl https://www.gutenberg.org/cache/epub/84/pg84.txt -o shelley.txt
   curl https://www.gutenberg.org/cache/epub/768/pg768.txt -o bronte.txt
   ```

9. Get the wordcount.jars

   ```
   cd /app/jars
   curl https://github.com/wxw-matt/docker-hadoop/blob/master/jobs/jars/WordCount.jar -o WordCounter.jar
   ```
   
10. Load data in HDFS

   ```
   cd /
   hdfs dfs -mkdir /test-1-input
   hdfs dfs -copyFromLocal -f /app/data/*.txt /test-1-input/
   ```
11. Run Hadoop

   ```
   hadoop jar jars/WordCount.jar WordCount /test-1-input /test-1-output
   ```

This is where I got stuck always getting an error that I could not find away around.
   ```
   Exception in thread "main" java.io.IOException: Error opening job jar: WordCount.jar
        at org.apache.hadoop.util.RunJar.run(RunJar.java:261)
        at org.apache.hadoop.util.RunJar.main(RunJar.java:236)
Caused by: java.util.zip.ZipException: error in opening zip file
        at java.util.zip.ZipFile.open(Native Method)
        at java.util.zip.ZipFile.<init>(ZipFile.java:225)
        at java.util.zip.ZipFile.<init>(ZipFile.java:155)
        at java.util.jar.JarFile.<init>(JarFile.java:166)
        at java.util.jar.JarFile.<init>(JarFile.java:103)
        at org.apache.hadoop.util.RunJar.run(RunJar.java:259)
        ... 1 more
   ```

Instead of trying the curl I added the wxw repo to my system and used docker -cp to put it into my jars file in the container.
I had to start by leaving the container then ran the code below.

   ```
   mkdir wxw
   cd wxw
   git clone https://github.com/wxw-matt/docker-hadoop.git
   cd docker-hadoop/jobs/jars
   docker cp WordCount.jar namenode:/app/jars/WordCount.jar
   ```
This fixed the error I previously had and it worked and Hadoop is ran 

12. Copy results out of hdfs

   ```
   hdfs dfs -copyToLocal /test-1-output /app/res/
   ```

13. See the results!

   ```
   cat /app/res/test-1-output/part-r-00000
   ```
