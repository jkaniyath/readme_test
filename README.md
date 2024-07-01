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

![init](https://private-user-images.githubusercontent.com/165160551/344597364-9530e881-26a5-4aaf-866a-5876cb16e0f3.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3MTk4MjI1NDIsIm5iZiI6MTcxOTgyMjI0MiwicGF0aCI6Ii8xNjUxNjA1NTEvMzQ0NTk3MzY0LTk1MzBlODgxLTI2YTUtNGFhZi04NjZhLTU4NzZjYjE2ZTBmMy5wbmc_WC1BbXotQWxnb3JpdGhtPUFXUzQtSE1BQy1TSEEyNTYmWC1BbXotQ3JlZGVudGlhbD1BS0lBVkNPRFlMU0E1M1BRSzRaQSUyRjIwMjQwNzAxJTJGdXMtZWFzdC0xJTJGczMlMkZhd3M0X3JlcXVlc3QmWC1BbXotRGF0ZT0yMDI0MDcwMVQwODI0MDJaJlgtQW16LUV4cGlyZXM9MzAwJlgtQW16LVNpZ25hdHVyZT1hNWNjM2QzNDYxNmUxNmYwY2E4N2NhOTM4ZTEyNWM4ZTQxNDg4NmY3MzU2OTQ4MjkzOTM4MjVkZWZhOTlhN2FiJlgtQW16LVNpZ25lZEhlYWRlcnM9aG9zdCZhY3Rvcl9pZD0wJmtleV9pZD0wJnJlcG9faWQ9MCJ9.YJgNRAF0FfBnPY3pZcxME7WlD6zwpZ5ncEa2N9Iis-4)

   2. To create an execution plan, showing what actions Terraform will take to achieve the desired state described in the configuration files.
![plan command](https://private-user-images.githubusercontent.com/165160551/344600565-a25ebcef-0091-420c-b6c2-e5eca3988727.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3MTk4MjI4NTAsIm5iZiI6MTcxOTgyMjU1MCwicGF0aCI6Ii8xNjUxNjA1NTEvMzQ0NjAwNTY1LWEyNWViY2VmLTAwOTEtNDIwYy1iNmMyLWU1ZWNhMzk4ODcyNy5wbmc_WC1BbXotQWxnb3JpdGhtPUFXUzQtSE1BQy1TSEEyNTYmWC1BbXotQ3JlZGVudGlhbD1BS0lBVkNPRFlMU0E1M1BRSzRaQSUyRjIwMjQwNzAxJTJGdXMtZWFzdC0xJTJGczMlMkZhd3M0X3JlcXVlc3QmWC1BbXotRGF0ZT0yMDI0MDcwMVQwODI5MTBaJlgtQW16LUV4cGlyZXM9MzAwJlgtQW16LVNpZ25hdHVyZT04YmExM2E0ZTQzYzM5NjcxYzIzYjQxMTk3NDBiODM2ZWE0YjgwMzg5OGE3MWRmNjM3YzJlZWMwOWM0MTY5MmMwJlgtQW16LVNpZ25lZEhlYWRlcnM9aG9zdCZhY3Rvcl9pZD0wJmtleV9pZD0wJnJlcG9faWQ9MCJ9.gsnvpdXry0HWX72eRY-A1W7usVe0yCFOfy84Joe41Dw)

    3. To apply the changes required to reach the desired state of the configuration.
    ![apply command](https://private-user-images.githubusercontent.com/165160551/344600583-cec9687c-7532-48cd-8e9a-a434b7baba11.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3MTk4MjI4NTAsIm5iZiI6MTcxOTgyMjU1MCwicGF0aCI6Ii8xNjUxNjA1NTEvMzQ0NjAwNTgzLWNlYzk2ODdjLTc1MzItNDhjZC04ZTlhLWE0MzRiN2JhYmExMS5wbmc_WC1BbXotQWxnb3JpdGhtPUFXUzQtSE1BQy1TSEEyNTYmWC1BbXotQ3JlZGVudGlhbD1BS0lBVkNPRFlMU0E1M1BRSzRaQSUyRjIwMjQwNzAxJTJGdXMtZWFzdC0xJTJGczMlMkZhd3M0X3JlcXVlc3QmWC1BbXotRGF0ZT0yMDI0MDcwMVQwODI5MTBaJlgtQW16LUV4cGlyZXM9MzAwJlgtQW16LVNpZ25hdHVyZT1hMmFkYmQwN2Q0MGNlYmRmZDk4NTk4MmM2Yjg0YWYxODhjY2NhOWViNzQ2MWNlN2ZiYjg4ZWIwODdhZThlYmI3JlgtQW16LVNpZ25lZEhlYWRlcnM9aG9zdCZhY3Rvcl9pZD0wJmtleV9pZD0wJnJlcG9faWQ9MCJ9.MU0pg3TcYpePCnbGlid-Oy6vsXxr4uPdMzQnERvUbIc)
    ![apply command result](https://private-user-images.githubusercontent.com/165160551/344600586-7c203cd5-230b-493e-b93c-88c6bfbfe18a.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3MTk4MjI4NTAsIm5iZiI6MTcxOTgyMjU1MCwicGF0aCI6Ii8xNjUxNjA1NTEvMzQ0NjAwNTg2LTdjMjAzY2Q1LTIzMGItNDkzZS1iOTNjLTg4YzZiZmJmZTE4YS5wbmc_WC1BbXotQWxnb3JpdGhtPUFXUzQtSE1BQy1TSEEyNTYmWC1BbXotQ3JlZGVudGlhbD1BS0lBVkNPRFlMU0E1M1BRSzRaQSUyRjIwMjQwNzAxJTJGdXMtZWFzdC0xJTJGczMlMkZhd3M0X3JlcXVlc3QmWC1BbXotRGF0ZT0yMDI0MDcwMVQwODI5MTBaJlgtQW16LUV4cGlyZXM9MzAwJlgtQW16LVNpZ25hdHVyZT1kMjJhMGQwNjAwMzM4ZTEzNzM3MThmZmVjYmY1MTJlNDkzZGJmMmJhMmNjMWY5Mzc1NmFjMTZmOTBkNGJhYjA4JlgtQW16LVNpZ25lZEhlYWRlcnM9aG9zdCZhY3Rvcl9pZD0wJmtleV9pZD0wJnJlcG9faWQ9MCJ9.6P11FMJ6lBsVts5meCEFDu-_HQPUx2_c1hL9OJyBOLs)

### Development and Testing

    You can perform development and testing either in a local environment or within a Docker container. In my case, I use a Docker container for these tasks. To set this up, you need to copy both your main Python files and your test files into the Docker container by specifying them in the Dockerfile. The code is organized in the src/main/ and src/tests/ directories in this repository.Below, I will show the Dockerfile creation and test results as screenshots.

    1. To create Docker image from a Dockerfile and a specified context.
    ![Docker build command](docker-build.png)
    2. To list all the Docker images.
    ![Docker list command](docker-images.png)
    3. To run a container from a specified Docker image.
    ![Docker run command](docker-run.png)
    4. After a successful docker run command, it will start a Docker container command prompt.
    ![Docker run result](docker-run-result.png)
    5. We can run tests using Docker containers. Below are the commands to run tests with pytest, along with screenshots. The main and test code can be found in the src/main/python and src/test folders, respectively.
    ![Pytest command](test1.png)
    6. Test result
    ![Pytest result](test-result.png)

### Push Docker image to Azure Container Registry

    1. Log into  Azure Container Registry.
    2. Create Docker image to upload.
    ![Image tag](docker-tag.png)
    3. Push created docker image to Azure Container Registry.
    ![Push docker image](docker-push1.png)

### Deploy Spark job on Kubernetes Service

    1. Login to your Azure account using azure CLI
    2. Integrate Azure Kubernetes Service with Azure Container Registry.
    3. Service account creation and assigning required cluster role.
    ![Service creation1](create_service1.png)
    ![Service creation2](create_service2.png)
    4. Run a Spark job within the bin directory in Spark Home on AKS.
    ![spark_submit](spark_sub.png)
    5. Follow screenshot shows details about running pods.
    ![Running pods](get_pods.png)
    6. After succefull job submision you will get following messages.
    ![Success message](spark_success.png)
    7. Check pod after job submit.
    ![Last pod](final_pod.png)
