



# Automated Data Lineage & Compliance Reporting

## Overview

This accelerator automates data lineage tracking and enforces security policies using AWS Macie, AWS IAM, and Databricks Unity Catalog. It helps organizations maintain data governance and comply with regulations by identifying sensitive data, tracking its movement, and restricting access as needed.

## Features

- **Sensitive Data Discovery:** AWS Macie scans S3 data for sensitive information.
- **Data Lineage Tracking:** Databricks Unity Catalog tracks data lineage for better understanding and governance.
- **Dynamic Access Control:** AWS IAM roles and policies dynamically enforce access restrictions based on Macie findings.
- **Automated Workflow:** AWS Step Functions orchestrates the entire process, from Macie scans to IAM policy updates.

## Prerequisites

Before using this accelerator, ensure the following:

- An AWS account with necessary permissions (Macie, IAM, Lambda, Step Functions, S3).
- A Databricks workspace with Unity Catalog enabled.
- Basic understanding of AWS services (Macie, IAM, Lambda, Step Functions, S3).

## Setup Instructions

### Step 1: Clone the Repository
```bash
git clone https://github.kadellabs.com/digiclave/databricks-accelerators.git
cd databricks-accelerators/Accelerators/automated_data_lineage_compliance_reporting
```

### Step 2: Install Dependencies
Ensure the required Python libraries are installed in your Databricks cluster:

```python
import boto3
import json
import os
from pyspark.sql import SparkSession

# --- CONFIGURATION (Replace with actual values) ---
S3_BUCKET = "your-data-bucket"  # Replace with your S3 bucket name
MACIE_ROLE_ARN = "arn:aws:iam::YOUR_ACCOUNT_ID:role/MacieRole"  # Replace with Macie IAM role ARN
AWS_ACCOUNT_ID = "YOUR_ACCOUNT_ID"  # Replace with your AWS account ID
DATABRICKS_UNITY_CATALOG_BUCKET = "s3://databricks-unity-bucket/" # Replace with your Unity Catalog bucket
DATABRICKS_CATALOG = "my_catalog"
DATABRICKS_SCHEMA = "my_schema"
DATABRICKS_TABLE = "customer_data"
DATABRICKS_USER_NAME = "databricks-user" # Replace with Databricks user name
STEP_FUNCTION_NAME = "DataLineageComplianceWorkflow" # Replace with your Step Function name

# --- AWS Clients ---
macie = boto3.client("macie2")
iam = boto3.client("iam")
step_functions = boto3.client("stepfunctions")
```

### Step 3: Create and Configure AWS Lambda Functions
1. **Trigger Macie Scan Lambda**: This Lambda triggers the AWS Macie scan.
2. **Set Up Databricks Unity Catalog Lineage**: Ensure Unity Catalog is enabled and set up the required tables.
3. **Apply IAM Security Policies**: Create a Lambda function to enforce IAM policies based on Macie findings.
4. **Step Functions Orchestration**: Use AWS Step Functions to coordinate these services.

### Step 4: Set Up AWS Step Functions

Here’s a sample Step Function definition to automate the workflow:

```json
{
  "StartAt": "TriggerMacieScan",
  "States": {
    "TriggerMacieScan": {
      "Type": "Task",
      "Resource": "arn:aws:lambda:YOUR_AWS_REGION:YOUR_ACCOUNT_ID:function:trigger-macie",
      "Next": "CheckMacieResults",
      "ResultPath": "$.macie_results"
    },
    "CheckMacieResults": {
      "Type": "Choice",
      "Choices": [
        {
          "Variable": "$.macie_results.Payload.body.findingsCount",
          "NumericGreaterThan": 0,
          "Next": "GenerateIAMPolicy"
        }
      ],
      "Default": "EndProcess"
    },
    "GenerateIAMPolicy": {
      "Type": "Task",
      "Resource": "arn:aws:lambda:YOUR_AWS_REGION:YOUR_ACCOUNT_ID:function:generate-iam-policy",
      "Next": "ApplyIAMRestrictions",
      "ResultPath": "$.iam_policy"
    },
    "ApplyIAMRestrictions": {
      "Type": "Task",
      "Resource": "arn:aws:lambda:YOUR_AWS_REGION:YOUR_ACCOUNT_ID:function:apply-iam-restrictions",
      "Parameters": {
        "policy": "$.iam_policy.Payload"
      },
      "End": true
    },
    "EndProcess": {
      "Type": "Pass",
      "End": true
    }
  }
}
```

### Step 5: IAM Policy for Sensitive Data

Create an IAM policy for restricting access to sensitive data identified by AWS Macie:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Deny",
      "Action": [
        "s3:GetObject",
        "s3:ListBucket"
      ],
      "Resource": [
        "arn:aws:s3:::your-data-bucket",
        "arn:aws:s3:::your-data-bucket/sensitive-data/*"
      ],
      "Condition": {
        "StringEquals": {
          "aws:PrincipalTag/AccessLevel": "Restricted"
        }
      }
    }
  ]
}
```

### Step 6: Set Up Databricks Unity Catalog

Use the following SQL commands to configure Unity Catalog and enable data lineage:

```sql
-- Create a Metastore
CREATE METASTORE my_metastore
  MANAGED LOCATION 's3://databricks-unity-bucket/';

-- Create Catalog and Schema
CREATE CATALOG my_catalog;
USE CATALOG my_catalog;
CREATE SCHEMA my_schema;
USE my_schema;

-- Create a Table with Lineage Tracking
CREATE TABLE customer_data (
  id INT,
  name STRING,
  email STRING
) USING DELTA;
```

Enable lineage tracking in Databricks with the following Python code:

```python
from pyspark.sql import SparkSession

# Create Spark session with Unity Catalog
spark = SparkSession.builder \
    .config("spark.databricks.unityCatalog.enabled", "true") \
    .getOrCreate()

# Read and display data lineage
df = spark.read.table("my_catalog.my_schema.customer_data")
display(df.explain(True))
```

## Contributions
Feel free to submit pull requests for improvements or additional features.

## License
This project is licensed under the **MIT License**.

## Contact
For issues or support, reach out via **GitHub Issues** or email the project maintainer.

