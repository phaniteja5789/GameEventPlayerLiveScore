# GameEventPlayerLiveScore
Player Score Details Live Update

**Player Live Game Score Update with AWS SERVICES**

<img width="830" alt="image" src="https://github.com/phaniteja5789/GameEventPlayerLiveScore/assets/36558484/891945c7-10d7-403d-afae-7b243a090b0c">

**Problem Statement**

The user will be hitting an HTTP API with player scores. Based on the request type player details need to be stored in the dynamodb and processed accordingly and send an email notification to the user 

**AWS SERVICES USED **: IAM Role, AWS LAMBDA, AWS DynamoDB, AWS API gateway, AWS CloudShell, AWS SNS, AWS CloudWatch

**Packages Used**
JSON, Requests, Boto3, AWS CLI

**Application Stages**

**Stage-1**

    1.) Created an IAM Role with AWS SNS, AWS DynamoDB Access
    2.) Created 2 Lambda Functions with names FetchGameDetailsFunction and ProcessGameDetailsFunction and attach the created roles to the Lambda Functions
    3.) Change the Default Lambda Execution to 10 mins
    4.) Created an HTTP API using API Gateway and attached **ANY** as Request Method and added Resource Path as **"FetchGameDetailsFunction"**
    5.) Created an SNS Topic with the name "LiveGameScore" and added an email Subscriber as the subscription to the SNS Topic and Configured the email

The Stage 1 has been developed using the AWS Management Console 

**Stage-2**

    1.) From the Application Code send the request to the HTTP API with player details in the response body
    2.) Trigger has been added to the FetchGameDetailsFunction Lambda from the API Gateway
    3.) Create a DynamoDB Table as "Player" in order to store the player details in the FetchGameDetailsFunction using BOTO3 API
    **4.) Streams have been enabled in the "Player" table**
    5.) Created trigger for the "ProcessGameDetailsFunction" from the Dynamodb streams
    6.) Created the Event Source Mapping from the ProcessGameDetailsFunction and to the DynamodbStreamARN
    7.) Based on the request method perform the below actions
      7.1.) If the Request Method is **POST** then Insert the Player Detail into the "Player" Table
      7.2.) If the Request Method is **PUT** then Check whether the Player is present in the "Player" Table.
          7.2.1) If the Player is present in the Table. Then Update the Player Score with the data that has been sent as in the Request Body
          7.2.2) If the Player is not present in the Table. Then Insert the Player into the Table
      7.3.) If the Request Method is **DELETE** then Check whether the Player is present in the "Player" Table
          7.3.1.) If the Player is present in the Table. Then Delete the player from the Table
          7.3.2.) If the Player is not present in the Table. Then do nothing

Stage 2 has been developed using BOTO3 API and Requests Module

Invoking the HTTP API code is present in SourceCode.ipynb

The Lambda and DynamoDB code is present in the FetchGameDetailsFunction.py

**Stage-3**

    **1.) Since Streams has been enabled to the Table. As soon as Modification happens to the Table the ProcessGameDetailsFunction will be Triggered**
    2.) Get the details based on the Stream Details
    3.) Publish the Message to the SNS Topic which was created during Stage 1
    4.) The Subscriber will get the message details with the Live Score

            
Stage 3 has been developed using BOTO3 API

Publishing the Message to the SNS Topic

Code is present in the ProcessGameDetailsFunction.py


