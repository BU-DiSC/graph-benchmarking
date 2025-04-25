# GraphX Setup Instructions

-----
(Setup Instructions Here)

## Prerequisites

1. **Apache Spark** (e.g. 3.5.2)  
2. **sbt** (Scala Build Tool)  
3. **AWS credentials** (if you’ll read from S3)  



# Running PageRank with any Dataset 


### 1. Preparing your Dataset Input 

#### 1.1 Local Text File
1. Place your graph data in a text file (/data/graphx/dataset.txt)
2. You’ll pass this path when you run the job.
#### 1.2 S3 Object
1. Upload your file to S3: `aws s3 cp /data/graphx/dataset.txt s3://YOUR-BUCKET/path/`
2. Use its S3 URI, for example: `s3://YOUR-BUCKET/path/dataset.txt`

### 2. Edit the PageRank Example
1. Open the source: `$SPARK_HOME/examples/src/main/scala/org/apache/spark/examples/graphx/PageRankExampleS3.scala`
2. Add your dataset file: `val input = "s3://YOUR-BUCKET/path/dataset.txt"`
3. Save your changes.


### 3.Compile & Package
`cd $SPARK_HOME/examples`
`sbt compile` 
`sbt package`

You should end up with a JAR like: `$SPARK_HOME/examples/target/scala-2.12/sparkexamples_2.12-3.5.2.jar`

### 4. Submit the PageRank Job
Note: The exact `spark-submit` invocation will depend on your cluster setup (master URL, Spark version, resource settings, etc.). Here’s a template you can adapt—just swap in your own values for `<MASTER_URL>`, `<JAR_PATH>`, and `<DATASET_URI>`:

spark-submit \
>   --class org.apache.spark.examples.graphx.PageRankExampleS3 \
>   --master spark://<MASTER_URL>:7077 \
>   --deploy-mode client \
>   --executor-memory 4G \
>   --total-executor-cores 4 \
>   <JAR_PATH>/sparkexamples_2.12-3.5.2.jar \
>   <DATASET_URI>

<MASTER_URL>: your Spark master’s hostname or IP.  
<JAR_PATH>: e.g. `/usr/local/spark/examples/target/scala-2.12`.  
<DATASET_URI>: local file path (`/data/graphx/dataset.txt`) or S3 URI (`s3://bucket/path/dataset.txt`).  





