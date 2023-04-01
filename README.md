# AWS Serverless Architecture for Company Analysis

This project demonstrates how to build a serverless architecture using AWS Lambda, DynamoDB, SQS, S3, and AWS Comprehend. I have created entries of company names in a DynamoDB table. The architecture reads company data from the DynamoDB table every minute, generates Wikipedia snippets for each company, performs sentiment analysis, extracts key phrases and entities from the snippets, and writes the results to an S3 bucket.

The workflow consists of two Lambda functions. The first function reads from the DynamoDB table and puts messages into an SQS queue. The second function reads from the SQS queue, generates Wikipedia snippets, performs analysis using AWS Comprehend, and writes the results to an S3 bucket.

# Workflow

Lambda Function 1: Read from DynamoDB table and put messages into SQS.

Lambda Function 2: Read from SQS queue, generate Wikipedia snippets, perform analysis with AWS Comprehend, and write results to S3.

# Generated Result Structure
The generated results will have the following structure:

```json
{
  "company": "Apple",
  "snippet": "Wikipedia snippet for the company...",
  "sentiment": "POSITIVE",
  "key_phrases": ["phrase1", "phrase2", ...],
  "entities": ["entity1", "entity2", ...]
}
```

<img width="1426" alt="Screenshot 2023-04-01 at 4 49 12 PM" src="https://user-images.githubusercontent.com/123284219/229313465-948c5f48-1ea3-42f6-b670-debe4e69b7fb.png">

# Architecture

<img width="1263" alt="serverless" src="https://user-images.githubusercontent.com/123284219/227793727-9118dc78-7b09-41f0-9de7-6f91ee029223.png">


# Reference
Source code: github.com/noahgift/awslambda

# Setup and Deployment

To build this serverless architecture on AWS from scratch, follow these steps:

Step 1: Create DynamoDB table, SQS queue, S3 bucket & working environment

* Open AWS DynamoDB console and create a table called "fang". For the primary key, use "name" with type "string".

* Click on the newly created table and add items with names like "Apple", "Google", etc.

* Open AWS SQS console and create a standard queue called "readDBValue" .

* Open AWS S3 console and create a bucket called "companysentiment".

* Open AWS Cloud9 console, open the IDE of your working environment, create a new directory for this project, and cd into it.

Step 2: Create producer-side Lambda function (triggered by a CloudWatch timer)

This function reads data from the DynamoDB table and puts messages into the SQS queue.



2.1. Create a SAM application

* In the Cloud9 IDE, click on "AWS", then right-click on "Lambda", and choose to "Create Lambda SAM Application".

* For settings of the SAM app, select Python 3.7/3.8 as the runtime, select "AWS SAM Hello World" as the SAM application template, choose the directory to store the files, and name your app "readDBValue".

* In the Cloud9 IDE, click on "Environment" and check the file hierarchy. Find your Lambda function's folder and open the sub-folder "hello-world".

* Replace "app.py" with the file lambda-serverless/hello_world/app.py from this repository.

* Modify app.py, change the name of the DynamoDB table and the name of the SQS queue.

* Replace "requirements.txt" with the file lambda-serverless/hello_world/requirements.txt from this repository.

2.2. Build and deploy

Build the application using SAM:

```css
sam build --use-container
Deploy the application using SAM guided deployment:
```

``` css
sam deploy --guided
```

# Add Permission
Access the AWS Lambda console and locate the new app "sam-app" and a new Lambda function named "sam-app-HelloWorldFunction-xxxx". Click on the function, then navigate to "Configuration" > "Permission", and click on the existing role to be directed to the IAM console.



In the new window, click "Attach Policies". On the subsequent page, find the "AdministratorAccess" policy and attach it.

Return to the previous Lambda function page in the AWS Lambda console and select "Triggers". Remove the "API Gateway" trigger.

Add a new EventBridge (CloudWatch Events) trigger. For its configuration, create a new rule and name it "OneMinuteTimer" (or any desired name). For the "schedule expression", input "rate(1 minute)".

# Testing
You can now enable the trigger and view the messages in the SQS queue.

In the AWS SQS console, click on the "readDBValue" queue, followed by "Send and receive messages", and then "poll for messages".

In the AWS Lambda console, on the specific function page, click on "Monitor" to find more details about the activity. You can also select "View Logs in CloudWatch".

You have the option to disable the trigger and purge the SQS queue at any time.



# Consumer Lambda Function
Create a second Lambda function that reads messages from the SQS queue and extracts the company name. It then generates Wikipedia snippets of the name and uses the AWS Comprehend API to conduct sentiment detection, key phrase detection, and entity detection on the snippets. The analysis results are then written to S3. This function is triggered by SQS.

## Create SAM Application

Create a second SAM application named "checkSQS" like the previous one.

Replace "app.py" with the file lambda-checkSQS/hello_world/app.py from this repo.

Modify app.py to update the REGION and bucket name.

Replace "requirements.txt" with the file lambda-checkSQS/hello_world/requirements.txt from this repo.

## Build and Deploy

Run sam build --use-container.
Run sam deploy --guided.

## Add Permission & Trigger

Add permission and remove the "API Gateway" trigger as done previously.

Add a new SQS trigger with your queue selected and a batch_size of 1.

Modify Lambda Function Timeout. 

To successfully write results to S3, modify the timeout for the second Lambda function:

* Open the second Lambda function's page, click on "Configuration", and then click on "General configuration".
* Set the timeout to 1 minute.


# Testing

You can now activate both triggers for the two functions and view the output in the S3 bucket, where you will find a csv file with the sentiment analysis output

Clear the activity by disabling the triggers and purging the SQS queue

<img width="1083" alt="Screenshot 2023-04-01 at 5 30 30 PM" src="https://user-images.githubusercontent.com/123284219/229314829-d065d820-ecbd-4a46-accb-6834f31c0369.png">

