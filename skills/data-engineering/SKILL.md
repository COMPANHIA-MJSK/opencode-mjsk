# Data Engineering Skill

## Purpose
Expert data pipeline design, ETL/ELT processes, data warehousing, and data lake architectures. Covers modern data stack with Spark, Airflow, dbt, and more.

## Data Processing Frameworks

### Apache Spark (PySpark)
```python
from pyspark.sql import SparkSession
from pyspark.sql.functions import col, sum, avg, window

spark = SparkSession.builder \
    .appName("DataPipeline") \
    .config("spark.sql.adaptive.enabled", "true") \
    .getOrCreate()

# Read from multiple sources
df = spark.read.format("parquet").load("s3://data-lake/raw/")
df_json = spark.read.format("json").option("multiline", "true").load("s3://data-lake/json/")

# Transformations
result = df.groupBy("category", "region") \
    .agg(
        sum("revenue").alias("total_revenue"),
        avg("quantity").alias("avg_quantity"),
        count("order_id").alias("order_count")
    ) \
    .filter(col("total_revenue") > 10000) \
    .orderBy(col("total_revenue").desc())

# Window functions
from pyspark.sql.window import Window
windowSpec = Window.partitionBy("category").orderBy("date")
df.withColumn("running_total", sum("revenue").over(windowSpec))

# Write to data warehouse
result.write \
    .mode("overwrite") \
    .partitionBy("year", "month") \
    .parquet("s3://data-lake/processed/")
```

### Apache Airflow DAG
```python
from airflow import DAG
from airflow.operators.python import PythonOperator
from airflow.providers.amazon.aws.transfers.s3_to_redshift import S3ToRedshiftOperator
from datetime import datetime, timedelta

default_args = {
    'owner': 'data-team',
    'depends_on_past': False,
    'start_date': datetime(2024, 1, 1),
    'retries': 2,
    'retry_delay': timedelta(minutes=5),
}

with DAG('etl_pipeline', default_args=default_args, schedule_interval='@daily') as dag:
    
    extract = PythonOperator(
        task_id='extract_data',
        python_callable=extract_from_source,
    )
    
    transform = PythonOperator(
        task_id='transform_data',
        python_callable=transform_data,
    )
    
    load = S3ToRedshiftOperator(
        task_id='load_to_redshift',
        schema='analytics',
        table='daily_metrics',
        s3_bucket='data-lake',
        s3_key='processed/daily_metrics/',
        copy_options=['FORMAT AS PARQUET'],
    )
    
    extract >> transform >> load
```

### dbt Models
```sql
-- models/staging/stg_orders.sql
WITH source AS (
    SELECT * FROM {{ source('raw', 'orders') }}
),

renamed AS (
    SELECT
        id AS order_id,
        customer_id,
        order_date,
        status,
        amount AS order_amount,
        _loaded_at
    FROM source
)

SELECT * FROM renamed

-- models/marts/fct_orders.sql
WITH orders AS (
    SELECT * FROM {{ ref('stg_orders') }}
),

customers AS (
    SELECT * FROM {{ ref('stg_customers') }}
),

final AS (
    SELECT
        o.order_id,
        o.order_date,
        c.customer_name,
        c.segment,
        o.order_amount,
        o.order_amount * 0.1 AS tax,
        o.order_amount * 1.1 AS total_amount
    FROM orders o
    JOIN customers c ON o.customer_id = c.customer_id
)

SELECT * FROM final
```

## Data Warehouse (Redshift/BigQuery)

### Redshift
```sql
-- Create table with sort and distribution keys
CREATE TABLE analytics.daily_metrics (
    date DATE,
    customer_id INTEGER,
    revenue DECIMAL(10,2),
    orders INTEGER
)
DISTSTYLE KEY
DISTKEY(customer_id)
SORTKEY(date);

-- Copy from S3
COPY analytics.daily_metrics
FROM 's3://data-lake/processed/daily_metrics/'
IAM_ROLE 'arn:aws:iam::role/CopyRole'
FORMAT AS PARQUET;
```

### BigQuery
```sql
-- Create external table
CREATE EXTERNAL TABLE `project.dataset.orders`
OPTIONS (
    format = 'PARQUET',
    uris = ['gs://data-lake/processed/orders/*']
);

-- Partitioned table
CREATE TABLE `project.dataset.analytics`
PARTITION BY date
CLUSTER BY customer_id
AS
SELECT * FROM `project.dataset.orders`;
```

## Data Quality

### Great Expectations
```python
import great_expectations as gx

context = gx.get_context()

# Define expectations
validator = context.sources.pandas_default.read_csv("data.csv")
validator.expect_table_values_to_not_be_null("customer_id")
validator.expect_column_values_to_be_between("amount", min_value=0, max_value=1000000)
validator.expect_column_values_to_be_unique("order_id")
```

## Best Practices
- Use incremental models in dbt
- Implement data contracts
- Use schema registries for data governance
- Partition large datasets by date
- Use columnar formats (Parquet, ORC)
- Implement data lineage tracking
- Monitor data quality metrics
- Use proper naming conventions
