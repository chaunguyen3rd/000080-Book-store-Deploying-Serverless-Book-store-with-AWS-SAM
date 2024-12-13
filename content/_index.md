---
title : "Serverless - Deploying applications with SAM"
date :  "`r Sys.Date()`" 
weight : 1 
chapter : false
---
# Serverless - Deploying applications with SAM

#### Overview

In the first two articles of this series, we know how to build a simple serverless web application using the AWS console. To build the serverless application faster, AWS provides users with the AWS Serverless Application Model (SAM) service, which is an open-source framework for building a serverless applications. It provides shorthand syntax to express functions, APIs, databases, and event source mappings. You define the application you want with just a few lines per resource and model it using YAML. During deployment, SAM transforms and expands the SAM syntax into AWS CloudFormation syntax. Then, CloudFormation provides the resources to the user.

In this workshop 3, we will rebuild the web application in workshop 2 with AWS SAM.

The architecture of the application we will build:

![WebArchitect](/000080-Book-store-Deploying-Serverless-Book-store-with-AWS-SAM/images/serverless-architect-diagram.png?featherlight=false&width=50pc)

#### Content

 1. [Preparation](1-preparation/)
 2. [Front-end deployment](2-front-end-deployment/)
 3. [Deploy Lambda function](3-deploy-lambda-function/)
 4. [Config API Gateway](4-config-api-gw/)
 5. [Test API by Postman](5-test-api-by-postman/)
 6. [Test API with front-end](6-test-front-end/)
 7. [Cleanup](7-cleanup)