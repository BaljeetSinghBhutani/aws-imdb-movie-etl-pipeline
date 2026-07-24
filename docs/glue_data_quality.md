# Glue Data Quality Validation

This project uses **AWS Glue Data Quality** to validate the dataset before loading it into Amazon Redshift. Data quality checks help ensure that only clean and reliable records are available for analytics.

## Rules Applied

```text
Rules = [
    IsComplete "imdb_rating",
    ColumnValues "imdb_rating" between 8.5 and 10.0
]
```

- **IsComplete("imdb_rating")** ensures that every record contains a valid IMDb rating.
- **ColumnValues("imdb_rating") between 8.5 and 10.0** validates that the rating falls within the configured range.

After validation, the ETL job uses a **Conditional Router** to separate records:

- ✅ **Valid records** are cleaned and loaded into **Amazon Redshift**.
- ❌ **Failed records** are stored in an **Amazon S3 failed records folder** for investigation and possible reprocessing.

This approach improves data reliability, prevents invalid records from entering the data warehouse, and provides better monitoring and auditing of the ETL pipeline.
