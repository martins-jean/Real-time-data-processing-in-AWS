# Real-time data processing in AWS

## Contextual overview

<p align="justify">
The administration of a city would like to improve the efficiency of its wind farms and anticipate possible defects in wind turbines due to excessively high wind speeds. They would like to know when wind speeds get severe so that they can quickly alert maintenance teams and dispatch them where needed. 
</p>

## Architecture diagram

![Screenshot 2023-09-13 at 22 29 06](https://github.com/martins-jean/Real-time-data-processing-in-AWS/assets/118685801/34804c71-53c0-4691-b73c-9035b3bb2266)

## Project objectives

<p align="justify">
1. To collect and process large streams of wind speed sensor data in real-time, we will use Kinesis Data Streams. <br> 
2. To analyze the data, we will use Kinesis Data Analytics for Apache Flink which is used with either Java or Scala. <br> 
3. To perform anomaly detection, we will use the Random Cut Forest (RCF) algorithm. The process will assign an anomaly score to each record based on values in the numeric columns. A record is an anomaly if it is distant from other records. <br> 
4. To configure an external destination, we will use a Lambda function. The function code will then take the processed data and parse it into records in an Amazon DynamoDB table. The data includes the wind farm location, wind speed and the assigned anomaly score. <br> 
5. To scan the DynamoDB table and filter for anomaly scores greater or equal to 2, we will use a second Lambda function. For each discovered anomaly, the function publishes a notification message to an SNS topic. <br> 
6. Subscribers to the SNS topic receive a notification email each time an anomaly is identified so the maintenance can be alerted and dispatched as soon as possible to the affected wind farm. 
</p>

## Reproducibility guidelines

<details>
  <summary>
    Required setup
  </summary>
  
1. Create a bucket in S3 for the Apache Flink application and use GitHub Desktop to upload the AnomalyDetection.jar file to it. <br>
2. Create an EC2 instance called "Wind Turbine Simulator" with a boto3 script that generates wind speed data. <br>
3. Create an IAM role for Kinesis Data Analytics.
4. Create several AWS Lambda functions using the boto3 scripts I provided.

</details>

<details>
  <summary>
    Deploy a Kinesis Data Stream to ingest streaming data from the wind speed sensors
  </summary>
  
1. Navigate to S3 and inside your kinesis-flink bucket, copy the name of the anomaly detection .jar file and paste it in a text editor. <br>
2. Navigate to the Amazon EC2 dashboard and click on instances (running) and copy the public IPv4 address of the EC2 instance you created earlier. <br>
3. In a new browser tab, paste the address and add /kinesis to it at the end. This opens the wind turbine data simulator. <br>
4. Navigate to Amazon Kinesis and create a provisioned Data Stream named "WindDataStream". <br>
5. Return to the Wind Turbine Data Simulator, type the name of your data stream and start sending the data. <br>
6. In the test data section, review that the data is being generated. <br>
7. Return to the data stream page and click on the data viewer option. <br>
8. Choose the only available shard, latest starting position and click get records. To view incoming data, click next records. If you don't see any records, wait for a few seconds and try again. <br>
9. Create another provisioned Data Stream named "AnomalyDetectionStream".

</details>

<details>
  <summary>
    Create a Kinesis Data Analytics for Apache Flink application to process the incoming data
  </summary>
  
1. On the Kinesis console, click Managed Apache Flink and then create a streaming application: <br>
  - Name: AnomalyDetection. <br>
  - Access to application resources: Choose from IAM roles that Kinesis Data Analytics can assume. <br>
  - Service role: choose the IAM role you created earlier. <br>
  - Templates: Development. <br> <br>
  
2. At the top of the application page, click configure: <br>
  - Amazon S3 bucket: click Browse and choose the kinesis-flink bucket you created earlier. <br>
  - Path to S3 object: AnomalyDetection.jar. <br>
  - Access to application resources: Choose from IAM roles that Kinesis Data Analytics can assume. <br>
  - Service role: choose the IAM role you created earlier. <br>
  - Under Runtime properties: click add item: <br>
    - Group ID: project. <br>
    - Key: inputStreamName. <br>
    - Value: WindDataStream. <br> <br>
    
  - Add another item: <br>
    - Group ID: project. <br>
    - Key: ouputStreamName. <br>
    - Value: AnomalyDetectionStream. <br> <br>
  
  - Add another item: <br>
    - Group ID: project. <br>
    - Key: region. <br>
    - Value: us-east-1. <br> 
  - Click run to start the application with the latest snapshot. <br>
  
3. Return to the Wind Turbine Data Simulator and under "Wind Speed Data Set" click start and review to ensure data is being generated. <br>
4. Click on the AnomalyDetectionStream on the Kinesis page. <br>
5. Under data viewer, choose the only shard available, the latest starting position, get records and then next records to review the data. <br>
6. Start the "Wind Speed Anomaly Data Set" and review it to ensure the simulator is producing anomaly data.
   
</details>

<details>
  <summary>
    Use a Lambda function to write application output data to a DynamoDB table
  </summary>
  
1. Go to the AWS Lambda console and click on the AnalyticsDestinationFunction. The function accepts the wind data from analytics application destination stream in JSON format and parses it to store it in a DynamoDB table. <br>
2. In the function overview section, click add trigger: <br> <br>
  
  - Choose kinesis. <br>
  - Select the AnomalyDetectionStream in the drop-down menu. <br>
  - Review that "Activate trigger" is checked and click add. <br>

  
</details>

<details>
  <summary>
    Use another Lambda function to filter the DynamoDB table for anomalies and publish them to an SNS topic
  </summary>
</details>
