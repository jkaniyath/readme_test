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
    ![init command](https://github.com/jkaniyath/readme_test/assets/165160551/9530e881-26a5-4aaf-866a-5876cb16e0f3)
