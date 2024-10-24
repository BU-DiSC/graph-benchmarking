# Chaos Setup Instructions

### 1. Install Boost Numeric Bindings
To work with the required numeric bindings library, download the following version (other versions may not be compatible):

```bash
sudo wget https://mathema.tician.de/dl/software/boost-numeric-bindings/boost-numeric-bindings-20081116.tar.gz
tar -xvf boost-numeric-bindings-20081116.tar.gz
```
### 2. Clone the Chaos Repository
Retrieve the Chaos graph processing system source code:
```bash
git clone https://github.com/bindscha/chaos.git
```

### 3. Set Permissions for /usr/local/
It is helpful if you have permissions to the /usr/local/ repo
```bash
sudo chown -R ubuntu:ubuntu /usr/local/
```

### 4. Download and Install Boost 1.54.0
```bash
wget https://sourceforge.net/projects/boost/files/boost/1.54.0/boost_1_54_0.tar.gz
tar -xvf boost_1_54_0.tar.gz 
cd boost_1_54_0/
./bootstrap.sh
./b2 install
```

### 5. Install ZeroMQ:
This is to support distributed communication and is a prerequisite for chaos
```bash
  sudo apt-get install libzmq3-dev
```

### 6. Test Boost Installation
Try compiling a test program
```bash
  vi test.cpp
  g++ test.cpp -o test -L/usr/local/lib/ -lboost_thread
  rm test.cpp 
```

### 7. Set Up Chaos Directories
```bash
   cd chaos/
   mkdir outputs
   mkdir -p object_files
   chmod +w object_files/
```

### 8. Compile chaos
```bash
  cd chaos
  make clean
  make
```

  
### 9. Copy Boost Numeric Bindings
Copy the numeric-bindings into a location accessible by Chaos:
```bash
  cd boost-numeric-bindings/
  sudo cp -r boost/ /usr/include/
```

### 10. Iterative Compilation and Error Fixing
Errors will occur during compilation. In this case, inspect the source files, fix the issues, and recompile. An example of a common file that may need editing:
```bash
  vi benchmarks/../algorithms/hyper-anf/hyper-anf.hpp (there will be multiple files like this)
  make clean
  make
```

### 11. Install LAPACK  
```bash
This is for linear algebra computations
sudo apt-get install liblapack-dev
``` 

### 12. Configure the slipstore.ini File
(ensure node <number> denotes which node this is like master is 0 for me and workers are 1 and 2):
```bash
vi slipstore.ini
```
This has to be configured and make sure the interface for network connectivity is correct - for me it is ens5 - you can find it using:
```bash
ip link show
```
 
### 13. Generate Graph Partitions with RMAT(ensure node <number> denotes which node this is like master is 0 for me and workers are 1 and 2):
To create graph partitions for a specific node (e.g., master node 0):
```bash
  rmat --name test --scale 20 --edges 16777216 --xscale_interval 3 --xscale_node 0
```
  
### 14. To run the pagerank on this, use (10 iterations and amount of memory assigned)
Run PageRank with 10 iterations and 16 processing threads, adjusting the memory allocation as needed:
```bash
  ./bin/benchmark_driver -g test -b pagerank --pagerank::niters 10 -a -p 16 --physical_memory 268435456
```

### 15. Enable required ports for aws EC2 or any platform and ensure firewall is disabled
Chaos uses a range of random ports between 5000 and 5024. Note chaos uses random ports from 5000-5024 (as per my observation), so all these have to be opened.
``` bash
  sudo ufw disable
```

### Note: 
1. Ping machines is not a great idea to check connectivity because aws disables ping by default. If you want to ping a system and it is in your same vpc and subnet, you still have to enable ICMP to be able to do this
2. I tried to run the S3 dataset directly but have not had any luck so far. We might have to download the 30gb dataset and manually partition it:
./bin/chaos -algo pagerank -input s3://data-graph-benchmarking/com-friendster.ungraph.txt -output /path/to/output -iters 20 -machines 3