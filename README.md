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


### Example Spark Session
```python
from pyspark.sql import SparkSession

spark = (
    SparkSession.builder
    .appName("tutorial") 
    .master("local[8]") # Example of setting cores
    .getOrCreate()
)
