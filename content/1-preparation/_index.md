---
title : "Preparation"
date :  2025-02-11
weight : 1 
chapter : false
pre : " <b> 1. </b> "
---
To start building SAM-based applications, we must first install the SAM CLI, install the AWS credentials, and initialize a simple SAM application.

1. Install ``sam`` CLI.
{{% notice note %}}
You can refer to: <https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/serverless-sam-cli-install.html>
{{% /notice %}}

2. Setup AWS credentials.
{{% notice note %}}
If you have already installed AWS credentials from previous posts, you can skip this step.
{{% /notice %}}
    - Open [IAM console](https://us-east-1.console.aws.amazon.com/iamv2/home?region=us-east-1#/home)
    - Click **User** on the left menu.
    - Click **Create user** button.
      ![IAMConsole](/images/temp/1/1.png?width=90pc)
    - At **Step 1: Specify user details** page.
      - Enter user name, such as: `sam-admin`.
      - Check option **Provide user access to the AWS Management Console**.
      - Select **I want to create an IAM user**.
      - Select **Custom password**, then enter your password.
        ![IAMConsole](/images/temp/1/2.png?width=90pc)
      - Uncheck the **User must create a new password at next sign-in**.
      - Click **Next**.
        ![IAMConsole](/images/temp/1/3.png?width=90pc)
    - At **Step 2: Set permissions** page.
      - Select **Attach policies directly**.
      - Select **AdministratorAccess** policy to the user has full access to the services.
      - Click **Next**.
        ![IAMConsole](/images/temp/1/4.png?width=90pc)
    - At **Step 3: Review and create** page.
      - Review configuration, and click **Create user**.
        ![IAMConsole](/images/temp/1/5.png?width=90pc)
    - At **Step 4: Review and create** page, click **Return to users list**.
    - At **sam-admin** page.
      - Click **Create access key**.
        ![IAMConsole](/images/temp/1/6.png?width=90pc)
    - At **Step 1: Access key best practices & alternatives**.
      - For **Use case**, select **Command Line Interface(CLI)**.
        ![IAMConsole](/images/temp/1/7.png?width=90pc)
      - Scroll down to the bottom, check the **I understand the above recommendation ...**.
      - Click **Next**.
        ![IAMConsole](/images/temp/1/8.png?width=90pc)
    - At **Step 2: Set description tag**.
      - Click **Create access key**.
        ![IAMConsole](/images/temp/1/9.png?width=90pc)
    - At **Step 3: Retrieve access keys**.
      - Click **Download .csv file**.
      - Then, click **Done**.
        ![IAMConsole](/images/temp/1/10.png?width=90pc)
    - Run the command using the terminal on your device.

        ```bash
        aws configure
        ```

      - Enter the information corresponding to the columns in the credential file you downloaded.
      - **AWS Access key ID**: Enter access key ID.
      - **AWS Secret access key**: Enter secret access key.
      - **Default region name**: Enter region closest to you.
      - **Default output format**: Can be overlooked.

3. Then, create a sample ``sam`` project.
    - Run the below commands at the directory where you want to deploy the application.

      ```bash
      #Step 1 - Download a sample application
      sam init
      ```

    - Then select the options:

      ```bash
      Which template source would you like to use?
        1 - AWS Quick Start Templates
        2 - Custom Template Location
      Choice: 1

      Choose an AWS Quick Start application template
        1 - Hello World Example
        2 - Multi-step workflow
        3 - Serverless API
        4 - Scheduled task
        5 - Standalone function
        6 - Data processing
        7 - Infrastructure event management
        8 - Machine Learning
      Template: 1

      Use the most popular runtime and package type? (Python and zip) [y/N]: y

      Would you like to enable X-Ray tracing on the function(s) in your application?  [y/N]: N

      Project name [sam-app]:  fcj-book-shop
      ```

      ![IAMConsole](/images/temp/1/11.png?width=90pc)
You have created a sample SAM project. Next , we will edit that project according to our application architecture.
