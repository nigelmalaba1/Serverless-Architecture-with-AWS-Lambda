# AWS Serverless Architecture for Company Analysis

This project demonstrates how to build a serverless architecture on AWS using Lambda, DynamoDB, SQS, S3, and Comprehend. The architecture reads company data from a DynamoDB table every minute, generates Wikipedia snippets for each company, performs sentiment analysis, extracts key phrases and entities from the snippets, and writes the results to an S3 bucket.

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

# Reference
Source code: github.com/noahgift/awslambda

# Setup and Deployment

To build this serverless architecture on AWS from scratch, follow these steps:

Step 1: Create DynamoDB table, SQS queue, S3 bucket & working environment

Open AWS DynamoDB console and create a table called "ffang" (or whatever you want). For the primary key, use "name" with type "string".

Click on the newly created table and add items with names like "Apple", "Google", etc.

Open AWS SQS console and create a standard queue called "checkDB" .

Open AWS S3 console and create a bucket called "faangsentiment".

Open AWS Cloud9 console, open the IDE of your working environment, create a new directory for this project, and cd into it.

Step 2: Create producer-side Lambda function (triggered by a CloudWatch timer)

This function reads data from the DynamoDB table and puts messages into the SQS queue.

2.1. Create a SAM application

In the Cloud9 IDE, click on "AWS", then right-click on "Lambda", and choose to "Create Lambda SAM Application".

For settings of the SAM app, select Python 3.7/3.8 as the runtime, select "AWS SAM Hello World" as the SAM application template, choose the directory to store the files, and name your app "checkDB".

In the Cloud9 IDE, click on "Environment" and check the file hierarchy. Find your Lambda function's folder and open the sub-folder "hello-world".

Replace "app.py" with the file checkDB/hello_world/app.py from this repository.

Modify app.py, change the name of the DynamoDB table and the name of the SQS queue.

Replace "requirements.txt" with the file checkDB/hello_world/requirements.txt from this repository.

2.2. Build and deploy

Build the application using SAM:

```css
sam build --use-container
Deploy the application using SAM guided deployment:
```

``` css
sam deploy --guided
```