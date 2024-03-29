

# 95-702 Distributed Systems       

## Distributed Computation

## lab9-MapReduceAndSpark

### Part 1. HDFS and Mapreduce  Due Monday 11:59 PM, April 11, 2022

There is no in-class lab this week due to CMU Carnival activities.
The full one point for this lab is earned by entering your cluster-id
password in the Canvas quiz by the due date.

The purpose of Part 1 is for the student to establish credentials
on the Heinz College High Performance Computing Cluster and to run
a simple MapReduce job.

You will find your login ID on Canvas. Look in the area where your grades are
normally posted. Look under "Cluster Login ID". There you will find a one or two
or three digit number. To form your login ID you must concatenate the string
'student' with the number expanded to exactly three digits. Here are some
examples:

If your number is 1 then your login ID is student001.
If you number is 17 then your login ID is student017.
If your number is 98 then your login ID is student098.
If you number is 102 then your login ID is student102.

Here are two links to examine what is going on on the cluster:

http://heinz-jumbo.heinz.cmu.local:50030/

http://heinz-jumbo.heinz.cmu.local:50070/

Output from System.out.println() statements is available in log files.  
To view the log visit:

http://heinz-jumbo.heinz.cmu.local:50030/jobtracker.jsp

Find your completed job in the list. On the left, click on the job ID.
Select the map or reduce task.
Select the task number and then the task logs.

Your initial password will be provided in class.

Before doing this lab, you will need to have the ability to run a secure
shell. You may use putty or some other secure telnet client.

To work from home, you will first need to install Cisco's AnyConnect
available from CMU's software download page shown next.

https://www.cmu.edu/computing/software/all/cisco-anyconnect/index.html

The TA's will be using time stamps on files to verify on-time or late submissions.
So, please do not perform any unintended work after the deadline for an assignment.

Before beginning, I highly recommend that you make a bulk replacement in this file. That
is, I would suggest that you change every occurrence of the string "mm6" with the
user ID that you have been assigned, e.g., student003. You may do this with a local editor. In that way
the commands that you will use below will be tailored to your machine. For example,
student110 would change every occurrence of the string "mm6" to the string
"student110". This will prevent common typing errors. It will allow you to copy and
paste commands.

Note that the tab key is useful for showing your command history. After using the tab
key, you may hit return to execute commands that you previously ran.

1.  Log in to the Hadoop cluster by using SSH to connect.
```
ssh -l mm6 heinz-jumbo.heinz.cmu.local
```

Note: If this ssh fails, it may be a problem with your DNS configuration. Use an IP address instead of the name. Ask a colleague to ping the name (heinz-jumbo.heinz.cmu.local) to see
the IP address.

You must change your password now. Use the "passwd" command. PLEASE remember this
new password.

Your password requires a capital letter, a number and one of
the following characters: !@#$%^&*()_-+=.

:checkered_flag:**Now, complete the 'quiz' that is on Canvas so that your password is available to the TA's for the course. WE NEED THIS TO GRADE YOUR PROJECT 5. The quiz is called "Cluster Password Quiz". By completing this quiz by the deadline, you earn the full point for this lab.**


<!--
### :checkered_flag: **Checkpoint:**  For credit for this part of the lab, you must have your HDFS cluster password placed on Canvas. Show your TA.
-->

2.  Your current directory is /home/mm6. Verify this with the "pwd" command.

3.  Create a directory named "input" and one named "output" in your
    /home/mm6 directory.

```
mkdir input
mkdir output
```

Verify this with the "ls" command.

4.  For testing, calculate PI with the following command:

```
hadoop jar /usr/local/hadoop/hadoop-examples-*.jar pi 10 100000

```
Twenty mappers may be employed with the following:
```
hadoop jar /usr/local/hadoop/hadoop-examples-*.jar pi 20 100000
```

Verify: Did the system compute a reasonable value of PI?

How did we use parallelization to compute Pi? See the course slides.

5.  Normally, to upload files, we will use "scp" (secure copy) to transfer files
    to the cluster.

6.  For now, simply construct a text file (pico or vi) and place it under your
    /home/mm6/input directory.

```
cd input

```
Verify with "pwd".

Run the pico editor and create a file called "test".

pico test

Copy and paste this file into test. By "this file" we mean the document you
are currently reading. Use ^o followed by ^x. The ^ symbol is the control key.

^o  is used to write out the file to disk.
^x is used to exit the pico editor.

7.  Copy the directory where the input resides to HDFS. ***Note: This is a different file system.***

```
cd ..
hadoop dfs -copyFromLocal /home/mm6/input /user/mm6/input

```

8. Look in the HDFS input directory and see if test is there.

```
hadoop dfs -ls /user/mm6/input
```

9. Run word count:

```
hadoop jar /usr/local/hadoop/hadoop-examples-*.jar wordcount /user/mm6/input  /user/mm6/output

```
Note: we are counting all of the words in all of the files in the input directory.
In this case, we only have one file, i.e., the file named 'test'.

If you get an error then you may have to remove an old output directory with
```
hadoop dfs -rmr /user/mm6/output
```

And run word count again.

10. See if the result files are there:
```
hadoop dfs -ls /user/mm6/output
```
11. Place the results in the output folder in your /home/mm6/output directory.
```
hadoop dfs -getmerge /user/mm6/output ~/output/
```

12. Examine the results:
```
cat ~/output/output
```

13. How many times did the word 'the' appear in the file? __________

14. How many time did the word 'you' appear in the file? ________

<!--
### :checkered_flag: For credit for this part of the lab, you must have your HDFS cluster password placed on Canvas and have answers for 13. and 14.
-->
Part 1 Notes:

You may view what jobs are running on the cluster with the command:

```
	hadoop job -list
```

Kill a job that is not making progress. Do this if you need to. You will need
the Job ID.

```
	bin/hadoop job -kill job_201310251241_0754
```

### Part 2. Apache Spark on IntelliJ

The file that you are currently reading is named "hadoop-lab.txt". We will
be working with this file in the steps below.

0. Open IntelliJ and select JDK 1.8 for the compiler.
1. Create a command line Java project in IntelliJ.
2. Name the project Spark-Example-Lab9.
3. Right click the project and do the following to install the Spark library.
Open Module Settings/Libraries/+/From Maven
Enter org.apache.spark:spark-core_2.10:1.0.0

```

OK/Apply/OK

```
4. In the src directory, create a Java class named WordCounter.java in the package
edu.cmu.andrew.userID.spark.example.java.wordcount.

5. Copy the code below into WordCounter.java.

```
package edu.cmu.andrew.userID.spark.example.java.wordcount;

import org.apache.spark.SparkConf;
import org.apache.spark.api.java.JavaPairRDD;
import org.apache.spark.api.java.JavaRDD;
import org.apache.spark.api.java.JavaSparkContext;
import scala.Tuple2;

import java.util.Arrays;

public class WordCounter {

    private static void wordCount(String fileName) {

        SparkConf sparkConf = new SparkConf().setMaster("local").setAppName("JD Word Counter");

        JavaSparkContext sparkContext = new JavaSparkContext(sparkConf);

        JavaRDD<String> inputFile = sparkContext.textFile(fileName);

        JavaRDD<String> wordsFromFile = inputFile.flatMap(content -> Arrays.asList(content.split(" ")));

        JavaPairRDD countData = wordsFromFile.mapToPair(t -> new Tuple2(t, 1)).reduceByKey((x, y) -> (int) x + (int) y);

        countData.saveAsTextFile("CountData");
    }

    public static void main(String[] args) {

        if (args.length == 0) {
            System.out.println("No files provided.");
            System.exit(0);
        }

        wordCount(args[0]);
    }
}
```
6. We need to specify a file name as a command line parameter.
Select the project node. From the Run menu, select Edit Configurations.

7. The main class is:
edu.cmu.andrew.mm6.spark.example.java.wordcount.WordCounter

Set the command line argument to the name of this file:
hadoop-lab.txt and set the working directory to the directory holding hadoop-lab.txt.

8. Compile and run the Java application.

9. The output file will be in a directory named CountData in your working directory.

10. If you get a "file already exists exception", be sure to delete the output directory
named "CountData" in your working directory.

11. If you receive strange errors, it may be the case that JDK 1.8 is required. JDK 8 needs to be downloaded and selected when you first run IntelliJ.

It is important that you complete Part 2 of this lab before working on Project 5. We will not be collecting or grading Part 2 of this lab.
<!--
### :checkered_flag: For credit for this part, show your TA that you have a file called part-00000 and it lists a wordcount for this file.
-->
