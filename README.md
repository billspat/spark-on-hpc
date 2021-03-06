# Introduction

Spark-on-HPC dynamically provisions Apache Spark clusters and run spark jobs on an HPC under its traditional resource manager. Currently, PBS or Torque on a linux cluster are supported. 

# Features
* Run under PBS resource limit, i.e. number of nodes, number of cores, memory and walltime
* Multiple spark jobs (master port is selected randomly for each job) for any user
* Only master and workers of the same job are allowed to connect together by a shared secret.

# Requirements
* Linux (should work with most distributions)
* Apache Spark 1.3.0+

# Installation
* Download and unpack Spark package into `SPARK_HOME` directory
* Download and unzip the Spark-on-HPC package. Change directory to `SPARK_ON_HPC` root directory
```
#cd $SPARK_ON_HPC
```
* Copy scripts into `$SPARK_HOME/sbin`
```
#cp pbs/spark-sbin/* $SPARK_HOME/sbin
```

# Usage
Root permission is NOT required.

* Once installed, create a job directory.
```
#cd $HOME
#mkdir test
#cd test
```
* Copy an example job script inside the package. There are two examples in the package. One is for single node script. The other is for multiple node script.
```
#cp $SPARK_ON_HPC/examples/test_spark_multi/spark_multi.sh test_spark_job.sh
```
* Make change to the script. Usually, the directives, shell variables and spark-submit arguments are changed. Set directives. For a PBS example, request 5 nodes (1 master + 4 workers), each with 2 cores and 1gb memory allocated, and queue "test".
```
#PBS -l nodes=5:ppn=2
#PBS -l vmem=1gb
#PBS -q test
```
* In job script, set `SPARK_HOME` to where the spark package is installed, and `SPARK_JOB_DIR` to the directory where the configuration and log files will be created. Note that the `PBS_O_WORKDIR` is the location where qsub command is executed.
```
export SPARK_HOME=$HOME/spark-1.4.1-bin-1.2.1
export SPARK_JOB_DIR=$PBS_O_WORKDIR
```
* In job script, change the spark-submit arguments. For example, running JavaSparkPi 10 tasks
```
SPARK_HOME/bin/spark-submit --master $SPARK_URL --class org.apache.spark.examples.JavaSparkPi $SPARK_HOME/lib/spark-examples-1.4.1-hadoop1.2.1.jar 10 > $PBS_O_WORKDIR/pi.txt
```
* Submit a job
```
#qsub test_spark_job.sh
```
The directory conf, logs, and work will be created in `SPARK_JOB_DIR` during the execution of spark. Examine them if necessary in addition to the normal job stdout and stderr files.

# How to run spark-on-hpc manually for testing purpose
Set environment variables `SPARK_JOB_DIR`, `SPARK_HOME`, and `PBS_NODEFILE`.

