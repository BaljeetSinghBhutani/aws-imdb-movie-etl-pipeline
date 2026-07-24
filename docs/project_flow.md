# Project Flow

## AWS Serverless ETL Pipeline for IMDb Movie Ratings

This document explains the complete end-to-end data flow of the IMDb Movie Ratings ETL pipeline built using AWS services. The pipeline ingests raw CSV data from Amazon S3, validates data quality, transforms the dataset using AWS Glue Visual ETL, loads clean records into Amazon Redshift, and sends notifications for monitoring.

---

## Architecture Overview

```text
IMDb CSV File
    ↓
Amazon S3 (Input Bucket)
    ↓
AWS Glue Crawler
    ↓
Glue Data Catalog
    ↓
AWS Glue Visual ETL Job
    ↓
Glue Data Quality
    ↓
Conditional Router
   ↙               ↘
Failed Records     Valid Records
   ↓               ↓
Amazon S3      Amazon Redshift
   ↓
EventBridge → SNS → Email Notification
```

---

## Detailed Workflow

### Step 1: Source Data Arrival

The IMDb movie ratings dataset is uploaded into the Amazon S3 input bucket.

**S3 Input Path:**

```text
s3://movies-data-analysis-project-etl/input/imdb_movies_rating.csv
```

This CSV file acts as the raw source dataset for the ETL pipeline.

---

### Step 2: Schema Discovery using AWS Glue Crawler

An AWS Glue Crawler scans the input CSV file stored in Amazon S3.

The crawler automatically detects:

* Column names
* Data types
* Table structure
* Metadata information

The discovered schema is stored in the AWS Glue Data Catalog.

---

### Step 3: Glue Data Catalog Table Creation

The crawler creates the source table inside the Glue Data Catalog database.

**Database:**

```text
imdb_movie_ratings
```

**Source Table:**

```text
input
```

This table is connected to the raw CSV file stored in Amazon S3.

---

### Step 4: Redshift Destination Table Metadata

A destination table was created in Amazon Redshift.

A separate Glue Crawler was used to scan the Redshift table and create a corresponding Glue Catalog table.

**Destination Table:**

```text
dev_movies_imdb_movies_rating
```

This allows AWS Glue Visual ETL to read from the source table and write directly to the Redshift destination table using a JDBC connection.

---

### Step 5: AWS Glue Visual ETL Processing

The AWS Glue Visual ETL job performs the main transformation logic.

**ETL Job Name:**

```text
Movie_data_ETL_Job
```

The ETL job performs the following operations:

1. Reads data from the Glue Catalog source table.
2. Applies Glue Data Quality checks.
3. Generates rule outcomes and row-level outcomes.
4. Uses a Conditional Router to separate valid and failed records.
5. Stores failed records in Amazon S3.
6. Removes unnecessary columns from valid records.
7. Loads clean records into Amazon Redshift.

---

### Step 6: Glue Data Quality Validation

Before loading data into Redshift, the dataset is validated using AWS Glue Data Quality.

The following rules are applied:

```text
IsComplete "imdb_rating"
ColumnValues "imdb_rating" between 8.5 and 10.0
```

These rules ensure that:

* The imdb_rating column is not null.
* The imdb_rating value falls within the configured range.

---

### Step 7: Rule Outcomes and Row-Level Outcomes

AWS Glue Data Quality generates two outputs:

#### Rule Outcomes

Contains the summary of data quality evaluation results.

Examples:

* Number of records passed
* Number of records failed
* Rule execution status

#### Row-Level Outcomes

Contains the validation result for each individual record.

Examples:

* Passed record
* Failed record
* Failed rule information

---

### Step 8: Conditional Routing

A Conditional Router separates the dataset into two groups:

#### Failed Records

Records that do not satisfy the data quality rules are routed to a failed records path in Amazon S3.

This allows failed data to be reviewed and corrected later.

#### Valid Records

Records that satisfy all data quality checks are routed to the next transformation stage.

---

### Step 9: Column Cleanup

For valid records, unnecessary metadata columns generated during the ETL process are removed before loading the data into Amazon Redshift.

This keeps the destination table clean and optimized for analytics.

---

### Step 10: Load into Amazon Redshift

The cleaned and validated records are loaded into the Amazon Redshift destination table.

**Redshift Table:**

```text
dev_movies_imdb_movies_rating
```

The Redshift table acts as the final analytics-ready dataset for querying and reporting.

---

### Step 11: Event Monitoring using EventBridge

AWS Glue job events are monitored using Amazon EventBridge.

When the configured Glue job event occurs, EventBridge captures the event and triggers the next action.

---

### Step 12: Email Notification using Amazon SNS

Amazon SNS is used to send email notifications.

Notifications can be used to alert about:

* Glue job failures
* Failed records detected
* Succeeded records detected 

This provides operational visibility into the pipeline.

---

## Final Output

After successful execution:

* Valid and clean records are stored in Amazon Redshift.
* Failed records are stored in Amazon S3 for investigation.
* Data quality reports are available for auditing.
* Email notifications provide monitoring and alerting.

---

## Key Features of the Pipeline

* Automated schema discovery using AWS Glue Crawler
* Centralized metadata management using Glue Data Catalog
* Visual ETL development using AWS Glue Studio
* Data quality validation before loading
* Conditional routing of failed records
* Storage of rejected records for investigation
* Loading clean data into Amazon Redshift
* Event-driven monitoring using EventBridge
* Automated email notifications using Amazon SNS

---

## Conclusion

This project demonstrates a complete serverless AWS ETL pipeline that handles data ingestion, schema discovery, metadata management, data quality validation, transformation, conditional routing, data warehousing, and operational monitoring.

The pipeline ensures that only validated IMDb movie rating records are loaded into Amazon Redshift, while failed records are preserved for further analysis.
