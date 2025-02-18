# AI-Powered Data Quality & Anomaly Detection

## Overview
Ensuring high-quality data is crucial for accurate analytics and decision-making. This repository automates data cleansing, anomaly detection, and real-time alerting using **AWS Glue DataBrew, Databricks AutoML, and AWS CloudWatch**. It integrates seamlessly with AWS and Databricks to help teams monitor, govern, and enhance data quality efficiently.

## Features
 **Automated Data Cleaning**: Uses AWS Glue DataBrew for preprocessing.

 **AI-Powered Anomaly Detection**: Utilizes Databricks AutoML for real-time monitoring.

 **Real-Time Alerting**: AWS CloudWatch integration for instant notifications.
 
 **Cloud-Native Deployment**: Easily integrates with AWS Lambda and Databricks Jobs.

## Folder Structure
The repository is structured into **Accelerators**, **Operational Tools**, and **Documentation**:

### **1. Accelerators** (Pre-built solutions)
- **[Data Quality Governance](https://github.kadellabs.com/digiclave/databricks-accelerators/-/tree/dev/Accelerators/data_quality_governance?ref_type=heads)**: Ensures data consistency in Databricks.
- **[Automated Anomaly Detection](https://github.kadellabs.com/digiclave/databricks-accelerators/-/tree/dev/Accelerators/anomaly_detection?ref_type=heads)**: Uses AutoML for real-time anomaly detection.
- **[CloudWatch Alerting](https://github.kadellabs.com/digiclave/databricks-accelerators/-/tree/dev/Accelerators/cloudwatch_alerting?ref_type=heads)**: Real-time monitoring and alerting.

### **2. Operational Tools** (Utilities and frameworks)
- **[AWS Glue DataBrew Pipeline](https://github.kadellabs.com/digiclave/databricks-accelerators/-/tree/dev/OTS_Tool/aws_glue_databrew?ref_type=heads)**: Automates data transformation.
- **[Databricks AutoML Workflow](https://github.kadellabs.com/digiclave/databricks-accelerators/-/tree/dev/OTS_Tool/databricks_automl?ref_type=heads)**: ML-based anomaly detection.
- **[Real-Time Monitoring](https://github.kadellabs.com/digiclave/databricks-accelerators/-/tree/dev/OTS_Tool/real_time_monitoring?ref_type=heads)**: CloudWatch integration for alerts.

### **3. Documentation**
- **Accelerators Overview**: Details on available accelerators and their use cases.
- **Operational Tools Overview**: Guides for monitoring and anomaly detection.

## Getting Started

### **Prerequisites**
Ensure you have:
- **Databricks Workspace** (with appropriate AWS permissions)
- **AWS IAM Roles** (with access to DataBrew, CloudWatch, and SNS)
- **Databricks API Token** (for AutoML and job execution)
- **AWS CLI configured** (`aws configure`)

### **Installation**
Clone the repository:
```bash
git clone https://github.kadellabs.com/digiclave/databricks-accelerators.git
cd databricks-accelerators
```
Navigate to the required accelerator and follow the setup guide.

## Usage

### **1. Configure Cloud Credentials**
Ensure AWS IAM roles and Databricks tokens are properly set.

### **2. Run DataBrew Job**
```python
import boto3

data_brew = boto3.client('databrew')
response = data_brew.start_job_run(Name='your-databrew-job')
print(response)
```

### **3. Train AutoML Model**
```python
from databricks import sql

conn = sql.connect(server_hostname='your-databricks-url',
                   access_token='your-api-token')
cursor = conn.cursor()
cursor.execute("SELECT * FROM anomalies")
data = cursor.fetchall()
print(data)
```

### **4. Monitor and Alert Using CloudWatch**
```python
import boto3

cloudwatch = boto3.client('cloudwatch')
response = cloudwatch.put_metric_alarm(
    AlarmName='AnomalyDetectionAlert',
    MetricName='AnomalyScore',
    Namespace='YourNamespace',
    Statistic='Average',
    Threshold=0.8,
    ComparisonOperator='GreaterThanThreshold',
    Period=300,
    EvaluationPeriods=2,
    AlarmActions=['arn:aws:sns:your-region:your-account-id:your-topic']
)
print("CloudWatch Alarm Set Up Successfully")
```

## Automation
Deploy the solution as an **AWS Lambda function**:
```python
import json
import boto3

def lambda_handler(event, context):
    client = boto3.client('databrew')
    response = client.start_job_run(Name='your-databrew-job')
    return {
        'statusCode': 200,
        'body': json.dumps('DataBrew Job Started!')
    }
```

## Contributions
We welcome contributions! Submit a pull request or open an issue for feature requests or improvements.

## License
This project is licensed under the MIT License.
