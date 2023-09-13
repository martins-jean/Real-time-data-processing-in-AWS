# Real-time data processing in AWS

## Contextual overview

<p align="justify">
The administration of a city would like to improve the efficiency of its wind farms and anticipate possible defects in wind turbines due to excessively high wind speeds. They would like to know when wind speeds get severe so that they can quickly alert maintenance teams and dispatch them where needed. 
</p>

## Architecture diagram

![Screenshot 2023-09-13 at 22 29 06](https://github.com/martins-jean/Real-time-data-processing-in-AWS/assets/118685801/34804c71-53c0-4691-b73c-9035b3bb2266)

## Project objectives

<p align="justify">
1. To collect and process streaming data, we will use Amazon Kinesis Data Streams. <br> <br>
2. To analyze the data from the data stream, we will use Amazon Kinesis Data Analytics for Apache Flink. We can author and run an Apache Flink application against streaming sources to detect higher than normal wind speeds. <br> <br>
3. To store the output data, we can configure an external destination. We can use a Lambda function to send the data to an Amazon DynamoDB table, which is an ideal service for scenarios where low latency is required. <br> <br>
4. To alert the maintenance team when an anomaly occurs, we can create a second Lambda function to scan and filter the DynamoDB table for anomaly records, and then publish a notification to an Amazon SNS topic and get real-time alerts when there is a need for maintenance. <br> <br>
5. Create a new Kinesis Data Analytics streaming application to calculate the wind speed maximum for one of the wind farms.
</p>

## Reproducibility guidelines
