# Spark ETL

## Integrate Azure Storage Container Gen2 with Azure Kubernetes Services.

    - Load the hotels and weather data frames from the Azure storage container.
    - Check the hotels data for incorrect (null) values in the Latitude and Longitude columns. For incorrect values, map the Latitude and Longitude from the OpenCage Geocoding API on the fly (via REST API).
    - Generate a geohash using Latitude and Longitude with one of the geohash libraries (like geohash-java) with a length of 4 characters in an extra column.
    - Perform a left join of the weather and hotels data using the generated 4-character geohash (avoid data multiplication and make your job idempotent).
    - Deploy the Spark job on the Kubernetes Service.

### Pre-requisites

    - Azure Subscription
    - Azure CLI
    - Install Docker
    - Install kubectl
    - Install Terraform
    - Install Hadoop and Spark
    - Install Python3
    - Install Java (preferably version 8 or 11).

### Deploy infrastructure with terraform

    Go to the folder where the Terraform file is located and type the following commands. All commands and their results are shown in the following screenshots.

    1. To initializes a Terraform working directory.
![init](   https://private-user-images.githubusercontent.com/165160551/344597364-9530e881-26a5-4aaf-866a-5876cb16e0f3.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3MTk4MjI1NDIsIm5iZiI6MTcxOTgyMjI0MiwicGF0aCI6Ii8xNjUxNjA1NTEvMzQ0NTk3MzY0LTk1MzBlODgxLTI2YTUtNGFhZi04NjZhLTU4NzZjYjE2ZTBmMy5wbmc_WC1BbXotQWxnb3JpdGhtPUFXUzQtSE1BQy1TSEEyNTYmWC1BbXotQ3JlZGVudGlhbD1BS0lBVkNPRFlMU0E1M1BRSzRaQSUyRjIwMjQwNzAxJTJGdXMtZWFzdC0xJTJGczMlMkZhd3M0X3JlcXVlc3QmWC1BbXotRGF0ZT0yMDI0MDcwMVQwODI0MDJaJlgtQW16LUV4cGlyZXM9MzAwJlgtQW16LVNpZ25hdHVyZT1hNWNjM2QzNDYxNmUxNmYwY2E4N2NhOTM4ZTEyNWM4ZTQxNDg4NmY3MzU2OTQ4MjkzOTM4MjVkZWZhOTlhN2FiJlgtQW16LVNpZ25lZEhlYWRlcnM9aG9zdCZhY3Rvcl9pZD0wJmtleV9pZD0wJnJlcG9faWQ9MCJ9.YJgNRAF0FfBnPY3pZcxME7WlD6zwpZ5ncEa2N9Iis-4
)