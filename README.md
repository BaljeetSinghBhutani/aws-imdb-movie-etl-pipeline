🎬 AWS Serverless ETL Pipeline for IMDb Movie Ratings

![AWS](https://img.shields.io/badge/AWS-Glue-orange)
![Redshift](https://img.shields.io/badge/Amazon-Redshift-red)
![S3](https://img.shields.io/badge/Amazon-S3-blue)
![EventBridge](https://img.shields.io/badge/EventBridge-Event--Driven-purple)
![SNS](https://img.shields.io/badge/SNS-Notifications-yellow)


This project demonstrates an end-to-end serverless ETL pipeline built on AWS. The pipeline ingests IMDb movie ratings from Amazon S3, automatically discovers schemas using AWS Glue Crawlers, validates the data with Glue Data Quality, transforms it using Glue Visual ETL, loads only high-quality records into Amazon Redshift, and generates email notifications through Amazon SNS whenever data quality issues are detected.
