## Running custom algorithms 

To run your own algorithms they need to be first written locally and then uploaded to AWS. The commands to upload the directory is the following: 

```bash
scp -i ~/.ssh/aws.pem -r localdirectory AWSIPV4LocalKey:/home/ubuntu/flink-1.20.0
```
The code to compile the jar file is the following from the flink main directory: 
```bash
javac custom/BFS/GfG.java
Cd custom 
jar cfm bfs.jar manifest.txt BFS/*.class
Cd ..
./bin/flink run custom/bfs.jar
```

Running custom algorithm on AWS

```bash
./bin/flink run directory/.jar file
#Example code 
./bin/flink run custom/bfs.jar
```
Run preset algorithms with custom input data
```bash 
./bin/flink run examples/directory/.jar file --input ./dataset link
#Example code 
./bin/flink run examples/batch/PageRank.jar --input ./custom/BFS/com-youtube.ungraph.txt
```

Example code 