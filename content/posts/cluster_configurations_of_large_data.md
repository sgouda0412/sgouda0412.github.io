+++
title = '🚀 What 99% of PySpark Users Get Wrong About Processing Large Files (500GB-1TB)'
date = 2025-04-02T10:03:26+05:30
draft = false
tags = ['pyspark']
author = 'Santosh Kumar Gouda'
+++

## 📑 Table of Contents
- [🔍 Introduction](#-introduction)
- [⚠️ Understanding the Challenges of Large-Scale Data Processing](#️-understanding-the-challenges-of-large-scale-data-processing)
  - [💾 Memory Limitations](#-memory-limitations)
  - [💽 Disk I/O Bottlenecks](#-disk-io-bottlenecks)
  - [🌐 Network Overhead](#-network-overhead)
  - [🧩 Partitioning Issues](#-partitioning-issues)
- [⚙️ Cluster Configuration for Massive Datasets](#️-cluster-configuration-for-massive-datasets)
  - [🖥️ Executor Memory & Cores](#️-executor-memory--cores)
  - [🎮 Driver Memory Settings](#-driver-memory-settings)
  - [⚖️ Dynamic vs. Static Allocation](#️-dynamic-vs-static-allocation)
  - [🔢 Parallelism & Partition Tuning](#-parallelism--partition-tuning)
- [📊 Optimal File Formats for Big Data](#-optimal-file-formats-for-big-data)
  - [📝 CSV vs. Parquet vs. ORC vs. Avro](#-csv-vs-parquet-vs-orc-vs-avro)
  - [🗜️ Compression Techniques](#️-compression-techniques)
  - [✂️ Splittable vs. Non-Splittable Files](#️-splittable-vs-non-splittable-files)
- [📖 Reading Large Files Efficiently](#-reading-large-files-efficiently)
  - [🧩 Partitioned Reads](#-partitioned-reads)
  - [🔍 Predicate Pushdown & Column Pruning](#-predicate-pushdown--column-pruning)
  - [🧮 Chunked Processing](#-chunked-processing)
- [🧠 Memory Management & Optimization](#-memory-management--optimization)
  - [💾 Spill-to-Disk Strategies](#-spill-to-disk-strategies)
  - [💫 Caching & Persistence Levels](#-caching--persistence-levels)
  - [🗑️ Garbage Collection Tuning](#️-garbage-collection-tuning)
- [🔄 Handling Joins, Aggregations, and Shuffles](#-handling-joins-aggregations-and-shuffles)
  - [📡 Broadcast Joins for Small Tables](#-broadcast-joins-for-small-tables)
  - [🧂 Salting for Skewed Data](#-salting-for-skewed-data)
  - [🚫 Avoiding Full Shuffles](#-avoiding-full-shuffles)
- [📊 Monitoring & Debugging Performance](#-monitoring--debugging-performance)
  - [🔍 Spark UI Deep Dive](#-spark-ui-deep-dive)
  - [📝 Log Analysis & Metrics](#-log-analysis--metrics)
  - [💥 Handling OOM Errors](#-handling-oom-errors)
- [💾 Writing Large Outputs Efficiently](#-writing-large-outputs-efficiently)
  - [📂 Partitioned Writes](#-partitioned-writes)
  - [📄 Avoiding Small Files Problem](#-avoiding-small-files-problem)
  - [🔄 Coalesce vs. Repartition](#-coalesce-vs-repartition)
- [📚 Real-World Case Studies](#-real-world-case-studies)
  - [📊 Processing 1TB CSV Files](#-processing-1tb-csv-files)
  - [⚡ Optimizing a 500GB Parquet Dataset](#-optimizing-a-500gb-parquet-dataset)
- [💡 Best Practices & Pro Tips](#-best-practices--pro-tips)
- [🏁 Conclusion](#-conclusion)

## 🔍 Introduction

Processing files ranging from 100GB to 1TB in PySpark is a common challenge in big data pipelines. If not handled correctly, it can lead to:

- ❌ Out-of-Memory (OOM) crashes
- ⏱️ Slow processing due to excessive shuffles
- 💽 Disk I/O bottlenecks
- 🌋 Cluster instability

This guide provides every minute detail on how to read, process, and write massive datasets efficiently in PySpark without breaking your cluster.

## ⚠️ Understanding the Challenges of Large-Scale Data Processing

### 💾 Memory Limitations
- Spark loads data into executor memory before processing.
- If the dataset is larger than available memory, it spills to disk, slowing down jobs.
- **Fix**: Increase `spark.executor.memory` and optimize partitioning.

### 💽 Disk I/O Bottlenecks
- Reading/writing 1TB from HDD (vs. SSD) can take hours.
- **Fix**: Use columnar formats (Parquet/ORC) and distributed storage (S3, HDFS).

### 🌐 Network Overhead
- Shuffling 1TB across nodes can cause network congestion.
- **Fix**: Minimize shuffles with broadcast joins and partitioning.

### 🧩 Partitioning Issues
- Too few partitions → underutilized cluster.
- Too many partitions → overhead in task scheduling.
- **Fix**: Aim for 128MB–256MB per partition.

## ⚙️ Cluster Configuration for Massive Datasets

### 🖥️ Executor Memory & Cores

```python
spark = SparkSession.builder \
    .config("spark.executor.memory", "8g") \  # 8GB per executor
    .config("spark.executor.cores", "4") \   # 4 cores per executor
    .config("spark.executor.instances", "20") \  # 20 executors
    .getOrCreate()
```

**Rule of thumb**:
- Executor Memory = 4GB–16GB (avoid going beyond 64GB due to GC overhead).
- Cores per Executor = 4–8 (more leads to contention).

### 🎮 Driver Memory Settings

The driver manages task scheduling and metadata.
For 1TB datasets, set:

```python
.config("spark.driver.memory", "8g")  
```

If collecting data to driver (e.g., `.collect()`), increase further.

### ⚖️ Dynamic vs. Static Allocation

- **Static Allocation**: Fixed resources (good for predictable workloads).
- **Dynamic Allocation**: Scales executors based on load.

```python
.config("spark.dynamicAllocation.enabled", "true")  
.config("spark.shuffle.service.enabled", "true")  
```

### 🔢 Parallelism & Partition Tuning

Default partitions = 200, but for 1TB data:

```python
spark.conf.set("spark.sql.shuffle.partitions", "1000")  
```

Adjust partition size:

```python
spark.conf.set("spark.sql.files.maxPartitionBytes", "256MB")  
```

## 📊 Optimal File Formats for Big Data

| Format  | Splittable? | Compression | Best For                   |
|---------|-------------|-------------|----------------------------|
| CSV     | ❌ No       | Low         | Simple, human-readable     |
| JSON    | ❌ No       | Low         | Nested data                |
| Parquet | ✅ Yes      | High (Snappy/Zstd) | Analytics, columnar queries |
| ORC     | ✅ Yes      | High        | Hive optimizations         |
| Avro    | ✅ Yes      | Medium      | Row-based storage          |

Best choice for 1TB data:

```python
df = spark.read.parquet("s3://path/to/data.parquet")  
```

### 📝 CSV vs. Parquet vs. ORC vs. Avro

- **CSV**: Human-readable but inefficient for large data
- **Parquet**: Column-oriented, ideal for analytics queries
- **ORC**: Similar to Parquet but optimized for Hive
- **Avro**: Great for row-based processing with schema evolution

### 🗜️ Compression Techniques

- **Snappy**: Good balance of speed and compression (default for Parquet)
- **Gzip**: Higher compression ratio but slower processing
- **Zstd**: Better than Snappy in both compression and speed

### ✂️ Splittable vs. Non-Splittable Files

- **Splittable**: Can be processed in parallel chunks (Parquet, ORC, Avro)
- **Non-Splittable**: Must be read as a single file (CSV, JSON with compression)

## 📖 Reading Large Files Efficiently

### 🧩 Partitioned Reads

```python
df = spark.read \
    .option("partitionSize", "256MB") \  # Control split size
    .csv("s3://big-file.csv")
```

### 🔍 Predicate Pushdown & Column Pruning

Only read needed columns:

```python
df.select("column1", "column2")  
```

Filter early:

```python
df.filter(df["date"] > "2023-01-01")  
```

### 🧮 Chunked Processing

Useful for non-splittable formats (CSV/JSON).
Example:

```python
for i in range(0, file_size, chunk_size):
    chunk = spark.read \
        .option("range", f"{i}GB-{i+chunk_size}GB") \
        .csv("s3://big-file.csv")
    process(chunk)
```

## 🧠 Memory Management & Optimization

### 💾 Spill-to-Disk Strategies

If memory fills up, Spark spills to disk.
Tuning:

```python
.config("spark.memory.fraction", "0.8")  # 80% for execution/storage
.config("spark.memory.storageFraction", "0.5")  # 50% reserved for storage
```

### 💫 Caching & Persistence Levels

Cache wisely:

```python
df.persist(StorageLevel.MEMORY_AND_DISK)  # Spill to disk if OOM
```

Unpersist when done:

```python
df.unpersist()
```

### 🗑️ Garbage Collection Tuning

Use G1GC for large heaps:

```python
.config("spark.executor.extraJavaOptions", "-XX:+UseG1GC")  
```

## 🔄 Handling Joins, Aggregations, and Shuffles

### 📡 Broadcast Joins for Small Tables

```python
spark.conf.set("spark.sql.autoBroadcastJoinThreshold", "100MB")  
df_large.join(broadcast(df_small), "key")  
```

### 🧂 Salting for Skewed Data

Add a random salt to distribute skewed keys:

```python
from pyspark.sql.functions import rand
df = df.withColumn("salt", (rand() * 10).cast("int"))  
```

### 🚫 Avoiding Full Shuffles

- Use repartition before joins/aggregations.
- Bucketing (for frequently joined columns):

```python
df.write.bucketBy(100, "user_id").saveAsTable("bucketed_table")  
```

## 📊 Monitoring & Debugging Performance

### 🔍 Spark UI Deep Dive

Check:
- Stages with long GC time → Increase memory.
- Skewed partitions → Use salting/repartitioning.
- Spill to disk → Increase spark.memory.fraction.

### 📝 Log Analysis & Metrics

```python
spark.sparkContext.setLogLevel("INFO")  # DEBUG for detailed logs
```

### 💥 Handling OOM Errors

**Symptoms**: `java.lang.OutOfMemoryError`

**Solutions**:
- Increase `spark.executor.memory`.
- Use `.repartition()`.
- Avoid `.collect()`.

## 💾 Writing Large Outputs Efficiently

### 📂 Partitioned Writes

```python
df.write.partitionBy("date").parquet("s3://output/")  
```

### 📄 Avoiding Small Files Problem

**Coalesce**: Reduces partitions (may cause skew).

```python
df.coalesce(100).write.parquet("s3://output/")  
```

**Repartition**: Evenly distributes data.

```python
df.repartition(100).write.parquet("s3://output/")  
```

### 🔄 Coalesce vs. Repartition

- **Coalesce**: Combines partitions without full shuffle (faster but may be unbalanced)
- **Repartition**: Full shuffle to ensure even distribution (slower but balanced)

## 📚 Real-World Case Studies

### 📊 Processing 1TB CSV Files

**Problem**: Slow reads, OOM errors.

**Solution**:
1. Convert to Parquet first.
2. Use 256MB partitions.
3. Filter early.

### ⚡ Optimizing a 500GB Parquet Dataset

**Problem**: Skewed joins.

**Solution**:
1. Broadcast small tables.
2. Salt skewed keys.

## 💡 Best Practices & Pro Tips

- ✅ Always test on a sample first.
- ✅ Avoid `.collect()` on large datasets.
- ✅ Use structured streaming for incremental processing.
- ✅ Monitor Spark UI for bottlenecks.
- ✅ Consider using Delta Lake for ACID transactions on large datasets.
- ✅ Set appropriate checkpoint intervals for long-running jobs.
- ✅ Use window functions instead of joins when possible.

## 🏁 Conclusion

Processing 500GB–1TB files in PySpark requires:
- ✔️ Proper cluster configuration
- ✔️ Efficient file formats (Parquet/ORC)
- ✔️ Optimal partitioning & parallelism
- ✔️ Minimizing shuffles & memory spills

