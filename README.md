# Real-time data processing in AWS

## Contextual overview

<p align="justify">
The administration of a city would like to improve the efficiency of its wind farms and anticipate possible defects in wind turbines due to excessively high wind speeds. They would like to know when wind speeds get severe so that they can quickly alert maintenance teams and dispatch them where needed. 
</p>

## Architecture diagram

![Screenshot 2023-09-13 at 22 29 06](https://github.com/martins-jean/Real-time-data-processing-in-AWS/assets/118685801/34804c71-53c0-4691-b73c-9035b3bb2266)

## Project objectives

<p align="justify">
1. To collect and process large streams of wind speed sensor data in real-time, we will use Kinesis Data Streams. <br> <br>
2. To analyze the data, we will use Kinesis Data Analytics for Apache Flink which is used with either Java or Scala. <br> <br>
3. To perform anomaly detection, we will use the Random Cut Forest (RCF) algorithm. The process will assign an anomaly score to each record based on values in the numeric columns. A record is an anomaly if it is distant from other records. <br> <br>
4. To configure an external destination, we will use a Lambda function. The function code will then take the processed data and parse it into records in an Amazon DynamoDB table. The data includes the wind farm location, wind speed and the assigned anomaly score. <br> <br>
5. To scan the DynamoDB table and filter for anomaly scores greater or equal to 2, we will use a second Lambda function. For each discovered anomaly, the function publishes a notification message to an SNS topic. <br> <br>
6. Subscribers to the SNS topic receive a notification email each time an anomaly is identified so the maintenance can be alerted and dispatched as soon as possible to the affected wind farm. <br> <br>
7. To further our analysis, we will create a new Kinesis Data Analytics application that will calculate the wind speed maximum for one of the wind farms.
</p>

## Reproducibility guidelines

<details>
  <summary>
    Required setup
  </summary>
  1. Create a bucket in S3 for the Apache Flink application and upload the two .jar files to it.
</details>

<details>
  <summary>
    Deploy a Kinesis Data Stream to ingest streaming data from the wind speed sensors
  </summary>
  1. Navigate to S3 and inside your kinesis-flink bucket, copy the name of the anomaly detection .jar file and paste it in a text editor. <br>
</details>

<details>
  <summary>
    Create a Kinesis Data Analytics for Apache Flink application to process the incoming data
  </summary>
</details>

<details>
  <summary>
    Use a Lambda function to write application output data to a DynamoDB table
  </summary>
</details>

<details>
  <summary>
    Use another Lambda function to filter the DynamoDB table for anomalies and publish them to an SNS topic
  </summary>
</details>

<details>
  <summary>
    Discover further insights by creating a second Kinesis Data Analytics application
  </summary>
</details>
