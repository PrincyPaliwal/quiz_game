
# Databricks Data Ingestion and Transformation Pipeline

## Overview
This project is a data ingestion and transformation pipeline built using Databricks, PySpark, AWS Glue, Kinesis, Delta Lake, and EventBridge. It also integrates Databricks Auto Loader, Azure Event Hub, and Kafka for real-time data streaming.

## Features
- **Real-time data ingestion** using Kafka, Azure Event Hub, and AWS Kinesis.
- **Efficient data transformation** using PySpark and Delta Lake.
- **Automated schema evolution** with Databricks Auto Loader.
- **Event-driven architecture** utilizing AWS EventBridge.
- **Optimized storage** leveraging Delta Lake's ACID properties.

## Folder Structure
```
├── notebooks
│   ├── main_notebook.py   # Main pipeline execution
│   ├── variables_notebook.py  # Configuration variables
│   ├── ingestion.py  # Data ingestion logic
│   ├── transformation.py  # Data transformation logic
│   ├── utils.py  # Helper functions
│
├── configs
│   ├── config.json  # JSON configuration file
│
├── README.md
```

## Installation
1. Clone the repository:
   ```sh
   git clone <repository_url>
   cd <repository_name>
   ```
2. Set up a virtual environment and install dependencies:
   ```sh
   python -m venv venv
   source venv/bin/activate  # On Windows use: venv\Scripts\activate
   pip install -r requirements.txt
   ```

## Usage
### Step 1: Define Variables
Store environment variables and configurations in `variables_notebook.py`:

```python
# variables_notebook.py
KAFKA_BROKER = "<broker_url>"
TOPIC_NAME = "<topic>"
S3_BUCKET = "<s3_bucket>"
```

### Step 2: Data Ingestion
Implement data ingestion using Kafka, Event Hub, or Kinesis:

```python
# ingestion.py
from pyspark.sql import SparkSession

def ingest_kafka():
    spark = SparkSession.builder.appName("KafkaIngestion").getOrCreate()
    df = spark.readStream.format("kafka").option("kafka.bootstrap.servers", KAFKA_BROKER).option("subscribe", TOPIC_NAME).load()
    return df
```

### Step 3: Data Transformation
Apply transformations using PySpark:

```python
# transformation.py
def transform_data(df):
    return df.selectExpr("CAST(value AS STRING)")
```

### Step 4: Save to Delta Lake
Write transformed data to Delta Lake:

```python
# main_notebook.py
def write_to_delta(df):
    df.writeStream.format("delta").option("path", "s3://" + S3_BUCKET).option("checkpointLocation", "s3://" + S3_BUCKET + "/_checkpoints").start()
```

### Step 5: Run the Pipeline
Run the pipeline in Databricks:

```sh
python main_notebook.py
```

## Configuration
- Modify `config.json` for environment-specific configurations.
- Update `variables_notebook.py` with necessary credentials and endpoints.

## Example Code for Databricks Notebooks

### Variables and Configuration (`variables_notebook.py`)

```python
# variables_and_values notebook

# AWS configuration and paths
AWS_REGION = "us-east-1"
KINESIS_STREAM_NAME = "your-kinesis-stream"
delta_path = "s3://your-delta-lake-bucket/delta-table"
GLUE_CRAWLER_NAME = "my-glue-crawler"
GLUE_DATABASE_NAME = "my-database"
S3_BUCKET_NAME = "your-data-bucket"
DATABRICKS_HOST = "https://your-databricks-instance"
DATABRICKS_TOKEN = "your-databricks-token"
NOTEBOOK_PATH = "/Shared/your_notebook"
```

### Configuration with Databricks Widgets (`variables_notebook.py`)

```python
# variables_and_values notebook
dbutils.widgets.text("S3_BUCKET", "your-updated-bucket")
dbutils.widgets.text("MACIE_ROLE_ARN", "your-updated-role")
dbutils.widgets.text("AWS_ACCOUNT_ID", "your-updated-account")

IAM_USER = "databricks-user"
S3_BUCKET = dbutils.widgets.get("S3_BUCKET")
MACIE_ROLE_ARN = dbutils.widgets.get("MACIE_ROLE_ARN")
AWS_ACCOUNT_ID = dbutils.widgets.get("AWS_ACCOUNT_ID")
```

## Contributing
Feel free to fork this repository and contribute improvements via pull requests.

## License
This project is licensed under the MIT License.

