# Apache Spark Framework

## Introduction
Apache Spark is an open-source unified analytics engine for large-scale data processing. It provides high-level APIs in Java, Scala, Python, and R, and an optimized engine that supports general execution graphs.

## Core Components

### Spark RDDs
```python
from pyspark import SparkContext, SparkConf

# Initialize Spark
conf = SparkConf().setAppName("SparkApp").setMaster("local[*]")
sc = SparkContext(conf=conf)

# Create RDD
rdd = sc.parallelize([1, 2, 3, 4, 5])

# Transformations
doubled = rdd.map(lambda x: x * 2)
filtered = rdd.filter(lambda x: x > 5)

# Actions
sum_result = rdd.reduce(lambda a, b: a + b)
count = rdd.count()
```

### Spark SQL and DataFrames
```python
from pyspark.sql import SparkSession
from pyspark.sql.functions import col, sum, avg, count

# Create SparkSession
spark = SparkSession.builder \
    .appName("SparkSQL") \
    .getOrCreate()

# Create DataFrame
df = spark.read.csv("data.csv", header=True, inferSchema=True)

# Basic operations
result = df.select("name", "age") \
    .filter(col("age") > 25) \
    .groupBy("department") \
    .agg(
        avg("salary").alias("avg_salary"),
        count("*").alias("employee_count")
    )

# SQL queries
df.createOrReplaceTempView("employees")
result = spark.sql("""
    SELECT department,
           AVG(salary) as avg_salary,
           COUNT(*) as employee_count
    FROM employees
    WHERE age > 25
    GROUP BY department
""")
```

## Data Processing

### ETL Operations
```python
def process_data(spark, input_path, output_path):
    """Example ETL pipeline."""
    
    # Read data
    raw_data = spark.read.parquet(input_path)
    
    # Transform
    processed = raw_data.transform(clean_data) \
                       .transform(enrich_data) \
                       .transform(validate_data)
    
    # Write results
    processed.write \
        .mode("overwrite") \
        .partitionBy("date") \
        .parquet(output_path)

def clean_data(df):
    """Clean and preprocess data."""
    return df.dropDuplicates() \
            .na.fill(0) \
            .filter(col("value").isNotNull())

def enrich_data(df):
    """Add derived columns and enrich data."""
    return df.withColumn("year", year(col("date"))) \
            .withColumn("month", month(col("date"))) \
            .withColumn("derived_value", 
                       col("value") * col("multiplier"))
```

### Streaming
```python
from pyspark.sql.functions import window

def process_stream(spark):
    """Process streaming data."""
    
    # Create streaming DataFrame
    stream_df = spark.readStream \
        .format("kafka") \
        .option("kafka.bootstrap.servers", "localhost:9092") \
        .option("subscribe", "events") \
        .load()
    
    # Process stream
    query = stream_df \
        .selectExpr("CAST(value AS STRING)") \
        .select(from_json(col("value"), schema).alias("data")) \
        .select("data.*") \
        .groupBy(
            window(col("timestamp"), "1 hour"),
            col("event_type")
        ) \
        .count() \
        .writeStream \
        .outputMode("complete") \
        .format("console") \
        .start()
    
    query.awaitTermination()
```

## Performance Optimization

### Memory Management
```python
def optimize_memory(spark):
    """Configure Spark for optimal memory usage."""
    
    # Memory configuration
    spark.conf.set("spark.memory.fraction", 0.8)
    spark.conf.set("spark.memory.storageFraction", 0.3)
    
    # Garbage collection
    spark.conf.set("spark.executor.extraJavaOptions",
                  "-XX:+UseG1GC -XX:MaxGCPauseMillis=200")

def cache_optimization(df):
    """Optimize caching strategy."""
    
    # Cache frequently accessed data
    df.cache()
    
    # Use checkpointing for long lineages
    sc.setCheckpointDir("/checkpoint/dir")
    df.checkpoint()
```

### Partitioning Strategy
```python
def optimize_partitioning(df, partition_column):
    """Implement optimal partitioning strategy."""
    
    num_partitions = df.rdd.getNumPartitions()
    
    # Repartition if needed
    if num_partitions > 2000:
        df = df.repartition(2000, partition_column)
    
    # Coalesce if too many small partitions
    elif num_partitions > 200 and df.count() / num_partitions < 10000:
        df = df.coalesce(200)
    
    return df
```

## Monitoring and Debugging

### Performance Monitoring
```python
from pyspark.accumulators import AccumulatorParam

class MetricsAccumulator(AccumulatorParam):
    def zero(self, value):
        return dict()
    
    def addInPlace(self, value1, value2):
        for k, v in value2.items():
            value1[k] = value1.get(k, 0) + v
        return value1

def monitor_performance(spark):
    """Monitor Spark job performance."""
    
    # Create accumulator
    metrics = spark.sparkContext.accumulator(
        dict(), MetricsAccumulator())
    
    def log_metrics(partition):
        metrics.add({
            'partition_count': 1,
            'record_count': sum(1 for _ in partition)
        })
        return partition
    
    # Apply monitoring
    df.rdd.mapPartitions(log_metrics).count()
    
    return metrics.value
```

### Debugging Tools
```python
def debug_spark_job(df):
    """Debug Spark job execution."""
    
    # Enable debug logging
    spark.sparkContext.setLogLevel("DEBUG")
    
    # Explain execution plan
    df.explain(extended=True)
    
    # Sample data for debugging
    debug_sample = df.sample(False, 0.01).collect()
    
    # Print lineage
    print(df.rdd.toDebugString())
```

## Best Practices

### Error Handling
```python
def robust_spark_processing(spark, input_path):
    """Implement robust error handling."""
    
    try:
        df = spark.read.parquet(input_path)
        
        # Handle corrupt records
        corrupt_count = df.filter(col("_corrupt_record").isNotNull()) \
                         .count()
        
        if corrupt_count > 0:
            raise ValueError(f"Found {corrupt_count} corrupt records")
        
        return process_data(df)
        
    except Exception as e:
        logging.error(f"Processing failed: {str(e)}")
        raise
```

## Conclusion
Apache Spark provides a powerful engine for large-scale data processing with high-level APIs and optimized execution.
