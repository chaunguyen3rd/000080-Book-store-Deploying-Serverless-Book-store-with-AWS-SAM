---
title : "Test APIs by Postman"
date :  "`r Sys.Date()`" 
weight : 5
chapter : false
pre : " <b> 5. </b> "
---
In this step, we will test operation of the APIs using [Postman](https://www.postman.com/downloads/) tool.

#### Test the listing API
1. Click **+** to add a new tab.
    - Select **GET** method.
    - Enter URL of the listing API that recorded from the previous step.
    - Click **Send**.
![TestListAPI](/000080-Book-store-Deploying-Serverless-Book-store-with-AWS-SAM/images/temp/1/85.png?width=90pc)
The returned result is the entire data of the **Books** table that has been processed.

#### Test the writing API
1. Similarly create a new tab.
    - Select **POST** method.
    - Enter URL of the writing API that recorded from the previous step.
    - Choose **Body**.
    - Choose **form-data**.
    - Fill **Key** and **Value** as below. Change type of Key **image** to **File** as image below. And with Value of Key **image**, choose your local file path.
      - id: 5
      - name: Amazon Web Services in Action 2nd Edition
      - author: Andreas Wittig
      - price: 59.99
      - category: IT
      - description: Amazon Web Services in Action, Second Edition is a comprehensive introduction to computing, storing, and networking in the AWS cloud. You'll find clear, relevant coverage of all the essential AWS services you to know, emphasizing best practices for security, high availability and scalability
      - image: aws-logo.png
    - Click **Send**. Wait a moment, see the results returned **Successfully created item**.
![TestListAPI](/000080-Book-store-Deploying-Serverless-Book-store-with-AWS-SAM/images/temp/1/86.png?width=90pc)

2. Open **Books** table in DynamoDB console to check data
    - Before call the write API.
  ![TestListAPI](/000080-Book-store-Deploying-Serverless-Book-store-with-AWS-SAM/images/temp/1/87.png?width=90pc)
    - After call the write API.
  ![TestListAPI](/000080-Book-store-Deploying-Serverless-Book-store-with-AWS-SAM/images/temp/1/88.png?width=90pc)

#### Test the deleting API
1. Similarly create a new tab.
    - Select **DELETE** method.
    - Enter URL of the writing API that recorded from the previous step. Change **{id}** to book_id you want to delete, such as **5**.
    - Click **Send**. Wait a moment, see the results returned **Successfully delete item!**.
![TestListAPI](/000080-Book-store-Deploying-Serverless-Book-store-with-AWS-SAM/images/temp/1/89.png?width=90pc)

2. Open **Books** table in DynamoDB console to check data.
![TestListAPI](/000080-Book-store-Deploying-Serverless-Book-store-with-AWS-SAM/images/temp/1/87.png?width=90pc)

3. Open **book-image-resize-stores-by-myself** bucket to check object. The **aws-logo.jpg** is deleted.
![TestListAPI](/000080-Book-store-Deploying-Serverless-Book-store-with-AWS-SAM/images/temp/1/90.png?width=90pc)