### **What is MapReduce?**

**MapReduce** is a programming model and processing technique for handling large-scale data processing in a distributed environment, primarily used in **Hadoop**. It breaks down a task into two phases: the **Map** phase and the **Reduce** phase, which are executed in parallel across a cluster of computers. It is widely used for **parallel processing** of massive datasets in **distributed systems**.

---

### **MapReduce Workflow**:

The basic idea behind MapReduce is to perform parallel computation on a distributed set of data by breaking the task into two steps:

#### 1. **Map Phase**:
   - In this phase, the input data is divided into smaller chunks (splits) and processed by **Map tasks** in parallel across the cluster.
   - Each **map function** processes an individual split of the input data and generates a set of **intermediate key-value pairs**.
   - Example: In a word count example, the map function will read text and output key-value pairs such as `(word, 1)`.

#### 2. **Shuffle and Sort Phase**:
   - After the map phase, the system performs a **shuffle** operation, where all intermediate key-value pairs are moved from the mappers to the appropriate **reducers**.
   - The system then **sorts** the intermediate data by key to ensure that all values for a given key are grouped together. This is crucial for the reduce phase.

#### 3. **Reduce Phase**:
   - In the reduce phase, the **reducer** function takes the sorted intermediate key-value pairs (grouped by key) and processes them.
   - The **reducer** typically aggregates or combines the values associated with each key.
   - Example: In a word count example, the reduce function will sum up the counts for each word.

#### 4. **Output**:
   - After the reduce phase, the results are written to the **output** location, typically stored in **HDFS (Hadoop Distributed File System)**.

---

### **MapReduce Key Concepts**:

1. **Input Data**: 
   - The data is usually split into fixed-size chunks called **splits**. Each chunk is processed by a separate map task.

2. **Mappers**:
   - Each mapper reads data from its assigned split and processes it using the user-defined **map function**. The mapper outputs key-value pairs.

3. **Reducers**:
   - Reducers aggregate or combine the intermediate key-value pairs produced by mappers. Each reducer works with a particular key and all the values associated with it.

4. **Fault Tolerance**:
   - MapReduce is fault-tolerant, meaning if a task fails, it can be retried. In case of node failures, tasks are reallocated, and data is replicated in HDFS to ensure no data is lost.

5. **Scalability**:
   - MapReduce is designed to scale with the size of the cluster and the data. It can handle petabytes of data by distributing the tasks across multiple nodes.

6. **Parallelism**:
   - The job is parallelized by splitting it into smaller tasks (map and reduce). These tasks can be executed concurrently across a distributed cluster, significantly improving processing time.

---

### **MapReduce Example: Word Count**:

Let's consider a simple example of **word counting**:

1. **Map Phase**:
   - Input: A large collection of text.
   - Map function: For each line of text, break it into words and output a key-value pair: `(word, 1)`.

   Example:
   ```text
   "Hello World"

## 1. Anatomy of a MapReduce Job Run
   - **Job Initialization**:
     - The execution of a MapReduce job begins when a **JobClient** submits the job to the **JobTracker** (in older Hadoop versions) or **ResourceManager** (in YARN-based Hadoop).
     - **JobTracker** or **ResourceManager** is responsible for allocating resources and assigning tasks (map or reduce tasks) to **TaskTrackers** or **NodeManagers** on the cluster.
   - **Map Phase**:
     - The input data is split into smaller chunks called **splits**. Each split is processed by a **map task**.
     - The **Mapper** processes the data from the splits in parallel, reading each input record and transforming it into key-value pairs.
     - The output of the **Map** function is a set of intermediate key-value pairs.
   - **Shuffle and Sort Phase**:
     - After the map phase, the **shuffle** phase begins. The intermediate key-value pairs are grouped and sorted by key.
     - **Shuffle**: Moves data from mappers to reducers by partitioning the data based on the key.
     - **Sort**: Sorts the data so that all records with the same key are grouped together before passing them to reducers.
   - **Reduce Phase**:
     - The **Reducer** receives the sorted data, grouped by key, and applies the **reduce** function to aggregate or combine the values.
     - After all reducers have finished their tasks, the final output is written to HDFS (Hadoop Distributed File System).
---

## 2. Failures in MapReduce
   - **Task Failures**:
     - If a task fails (e.g., a map or reduce task), the **JobTracker** (or **ResourceManager** in YARN) will reschedule the task on another node.
     - **Retries**: Task retries are configurable, meaning that MapReduce will attempt to rerun the task on different nodes to recover from failures.
   - **Node Failures**:
     - If an entire node fails during task execution, the **TaskTracker** (or **NodeManager**) on that node will notify the **JobTracker** (or **ResourceManager**), and the tasks will be reassigned to other available nodes.
     - Data replication in HDFS ensures data availability even in the case of node failure.
   - **Speculative Execution**:
     - To address the issue of **straggler tasks** (tasks that are taking longer than expected), MapReduce supports **speculative execution**, where duplicate tasks are launched in parallel. The first task to complete is used, while the other tasks are killed.
   
## 3. Job Scheduling
   - **JobTracker** (or **ResourceManager** in YARN) is responsible for managing jobs and task scheduling.
   - **TaskTracker** (or **NodeManager** in YARN) is responsible for executing the tasks as assigned by the JobTracker.
   - **Scheduling Policies**:
     - **FIFO (First-In, First-Out)**: The default scheduling algorithm where jobs are executed in the order they are submitted.
     - **Fair Scheduler**: Allocates resources to jobs so that each job gets a fair share of the resources available.
     - **Capacity Scheduler**: Allocates resources based on predefined capacities, which allows for resource isolation among different jobs or users.
   - **Job Queues**: In a multi-tenant cluster, jobs are queued, and the scheduling policy determines the order and resources allocated to each job.
   - **Preemption**: If a job is not progressing, resources may be preempted to allow higher-priority jobs to complete.
   
## 4. Shuffle and Sort
   - **Shuffle Phase**: 
     - After the Map phase, the intermediate key-value pairs are shuffled. The **Shuffle** ensures that all values for a given key end up at the same **Reducer**.
     - The data from different mappers is transferred over the network to the reducers.
   - **Sort Phase**:
     - Sorting is done after the shuffle phase. The **sort** step ensures that the keys are ordered so that all values associated with the same key are grouped together.
     - Sorting improves the efficiency of the reduce function, which processes the values for each key in sorted order.
   - **Importance of Shuffle and Sort**:
     - This phase is crucial for performance because it dictates how the data is organized before it is processed by the reducers.
     - Efficient sorting and partitioning help reduce the overall runtime of the MapReduce job.

## 5. Task Execution
   - **Map Task Execution**:
     - Each **map task** processes an individual **split** of the input data. It processes each record using the **Map function** and emits intermediate key-value pairs.
     - The **Map function** is typically user-defined and will depend on the specific job.
   - **Reduce Task Execution**:
     - After the shuffle and sort phases, **reduce tasks** are executed. Each reducer processes a group of key-value pairs for a single key.
     - The **Reduce function** typically aggregates, filters, or processes the values associated with each key, depending on the logic defined by the user.
     - Reducers execute in parallel across the nodes in the cluster, handling different partitions of data.
   
## 6. MapReduce Types and Formats
   - **Types of MapReduce Jobs**:
     - **Classic MapReduce**: The traditional MapReduce model that uses Java as the programming language for defining Map and Reduce functions.
     - **Streaming MapReduce**: Allows writing the map and reduce functions in languages other than Java (like Python or Ruby). It uses standard input and output for data processing.
     - **MapReduce for Structured Data**: Supports handling structured data types (e.g., Avro, Parquet). Involves creating custom **InputFormat** and **OutputFormat** classes to support these data types.
   - **Input Formats**:
     - **TextInputFormat**: The default format where each line of input data is treated as a record (i.e., key-value pair).
     - **KeyValueTextInputFormat**: Each line is split into a key and value based on a separator (often space or tab).
     - **SequenceFileInputFormat**: Reads binary files that store key-value pairs.
     - **Avro/Parquet InputFormat**: For working with binary formats like Avro and Parquet.
   - **Output Formats**:
     - **TextOutputFormat**: The default output format, writing the results as plain text with each record on a new line.
     - **SequenceFileOutputFormat**: Writes the output as key-value pairs in a binary format.
   
## 7. MapReduce Features
   - **Parallelism**: 
     - MapReduce inherently supports parallelism by dividing tasks into smaller sub-tasks (map and reduce). These sub-tasks run concurrently across multiple nodes.
   - **Fault Tolerance**: 
     - MapReduce ensures fault tolerance by re-executing failed tasks on different nodes, using HDFS to store replicated data, making sure that a node or task failure doesn’t affect the overall job execution.
   - **Data Locality**: 
     - One of the performance optimizations in MapReduce is **data locality**, which ensures that map tasks are run on nodes where the data resides, minimizing data transfer time.
   - **Scalability**:
     - MapReduce is designed to scale out horizontally, meaning it can handle increasing amounts of data by adding more nodes to the cluster.
   - **Composability**: 
     - MapReduce jobs can be chained together, allowing users to build complex workflows where the output of one job is the input to the next.
   - **Custom Input and Output Formats**: 
     - MapReduce supports the creation of custom input and output formats, making it possible to work with any kind of data structure, including structured, semi-structured, and unstructured data.

---


