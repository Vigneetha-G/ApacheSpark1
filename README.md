# ⚡️ Apache Spark

Apache Spark is a powerful **distributed computing engine** and a **Big Data framework**. It processes data in a distributed manner, enabling **massive parallel processing**.

---

## 🚀 Core Concepts & Performance

* Spark relies on a **cluster** (group of machines), where each machine is considered a **node**.
* It is significantly faster than its predecessor, **Hadoop MapReduce**,100 times quicker for many workloads.
* **Key Features**:
    * In-memory computation
    * Streaming and batch processing
    * Lazy evaluation
    * Fault tolerant
    * Partitioning

| Scaling Type            | Description                                | Availability                                      |
| :---------------------- | :----------------------------------------- | :------------------------------------------------ |
| **Vertical Scaling**    | Upgrade the system (e.g., adding RAM/Cores)| Low availability (if system fails, all work stops)|
| **Horizontal Scaling**  | Add more nodes to the cluster              | High availability (other nodes continue)          |

---

## 🛠️ Spark Architecture


![Spark Cluster Architecture](https://spark.apache.org/docs/latest/img/cluster-overview.png)

* **Driver Node / Driver Program**: Contains the Spark Context, acting like a "team lead" that divides up the work.
* **Worker Node**: Runs tasks. Contains an Executor and a Cache.
* **Resource Manager / Cluster Manager**: Manages cluster resources.

The architecture follows a **Master-Slave model**

---

## 🔄 Execution Flow & Fault Tolerance

* **Lazy Evaluation**: Transformations are only executed during an "Action".
* **Spark Jobs,DAG (Directed Acyclic Graph)**: Created when transformations are performed; shows the data flow and transformation stages.
* **RDD (Resilient Distributed Dataset)**: Spark's core abstraction for data, maintains fault tolerance by automatically recreating failed partitions.
* **RDD**:List of logical partitions.

### Transformations

| Type         | Data Movement      | Example Operations         |
| :----------- | :---------------- | :-------------------------|
| **Narrow**   | No shuffling      | Filter, select            |
| **Wide**     | Requires shuffling| GroupBy, join, agg        |

### Partitioning Operations

| Operation       | Action                                    | Shuffling Involved |
| :-------------- | :---------------------------------------- | :---------------- |
| **repartition** | Changes number of partitions (e.g. 2→10)  | Yes               |
| **coalesce**    | Merges partitions (decreases count)        | No                |

---

## 💻 Databricks

* **Photon**: Databricks' next-gen query engine.
* **Performance**: Written in C++ (instead of only JVM/Scala), speeds up Spark SQL and DataFrame processing.
* **Data Format Compatibility**: Optimized for columnar formats like **Delta Lake**, **Parquet**, as well as **CSV** and **JSON** for stateless streaming.

***
## JOBS, Stages, & Tasks

* **JOB**: A job is created for a Spark action.
* **Stage**: A stage is nothing but transformations.
    * **Narrow Transformation (1 Stage)**: Operations like `filter` or `select` where one input partition contributes to only one output partition.
    * **Wide Transformation (Shuffle Stage)**: Operations like `groupBy` which require data shuffling across the network.
* **Task**: The smallest unit of work in Spark.
    * A task is associated with the **number of partitions**. For example, 200 tasks for 200 partitions in a wide transformation.
* **Relationship**: Always 1 Job ⟹ 1 Stage + 1 Task (at least).

***

## Shuffle Joins in PySpark

For joins, the same key must reside on the same partition.  
If DataFrames are not reshuffled, a join cannot be performed on partitions individually.

If we reshuffle `df1` and `df2`, it will make 200 partitions (Shuffle Stage).

### Two Major Joins

1. **Shuffle Sort Merge Join (SMJ)**:
    * Data is **shuffled** and **sorted** by the join key within each partition before merging (join).
    * **Fact** tables often have multiple (redundant) ID values, while **dimension** tables have just one value per ID.
    * If data is highly redundant, SMJ will take **a lot of time** due to the sort operation.

2. **Shuffle Hash Join (HHJ)**:
    * Happens in **every partition**.
    * A **Hash Table** (a lookup table/dictionary) is created from the smaller relation (e.g., dimension table).
    * It can quickly apply the join by looking into the hash table.
    * The hash table allocates memory.

***

## Broadcast Hash Join (BHJ)

* **Condition**: Used when one DataFrame is significantly smaller (e.g., `df1 = 450 MB`, `df2 = 5 MB`).
* **Mechanism**:
    1. There is **no shuffling** of the small DataFrame (`df2`).
    2. The **driver node** stores the broadcasted table temporarily.
    3. The driver broadcasts the entire small DataFrame to **all executors**.
    4. Executors use this broadcasted table to easily apply the join.
* **Recommendation**: The table size should be **small enough to fit in the driver's memory** because the driver needs to broadcast it.
### Example Spark Session Setup

```python
from pyspark.sql import SparkSession

# Build the SparkSession entry point
spark = (
    SparkSession.builder
    .appName("tutorial") 
    .master("local[*]")  
    .getOrCreate()
)


