---
title : "Create DELETE API"
date :  "`r Sys.Date()`" 
weight : 3
chapter : false
pre : " <b> 4.3. </b> "
---
1. Open **template.yaml** file in **fcj-book-shop** folder
2. Add the following script at the end of the file that create the DELETE method
      ```
                /books/{id}:
                  delete:
                    responses:
                      "200":
                        description: 200 response
                        headers:
                          Access-Control-Allow-Origin:
                            type: string
                    x-amazon-apigateway-integration:
                      uri:
                        Fn::Sub: "arn:aws:apigateway:${AWS::Region}:lambda:path/2015-03-31/functions/${BookDelete.Arn}/invocations"
                      responses:
                        default:
                          statusCode: 200
                          responseParameters:
                            method.response.header.Access-Control-Allow-Origin: "'*'"
                      passthroughBehavior: when_no_match
                      httpMethod: POST #always POST
                      type: aws_proxy
      ```
      ![CreatePostAPI](/images/1/67.png?&width=90pc)

      - Add the following script at the end of the **BookDelete** function
      ```
            Events:
              DeleteBook:
                Type: Api
                Properties:
                  Path: /books/{id}
                  Method: delete
                  RestApiId:
                    Ref: BookApi
      ```
   ![CreatePostAPI](/images/1/68.png?&width=90pc)

2. Run the following command to deploy SAM
      ```
      sam build
      sam deploy --guided
      ```
      ![CreatePostAPI](/images/1/72.png?&width=90pc)
{{% notice note %}}
Enter "y" if asked "BookDelete may not have authorization defined, Is this okay? [y/N]: "
{{% /notice %}}

3. Open **book_delete** function console
    - Click **API Gateway**
      ![CreatePostAPI](/images/1/69.png?&width=90pc)

4. Show API Gateway being interacted with this function
    - Click this API Gateway

5. Display the resources and DELETE method
![CreatePostAPI](/images/1/70.png?&width=90pc)

6. Click **Stages** on the left menu
    - Click **staging**
    - Click **DELTE**
    - Record the **InvokeURL** of DELETE method
![CreatePostAPI](/images/1/71.png?&width=90pc)

7. Add the following script at the end of the **template.yaml** file to API can support Binary Media Types files
      ```
            BinaryMediaTypes: 
              - multipart~1form-data
      ```
8. Run the following command to deploy SAM
      ```
      sam build
      sam deploy --guided
      ```
      ![CreatePostAPI](/images/1/73.png?&width=90pc)

9. Back to API console
    - Select **Settings** on the left menu
    - Scroll down, check if **multipart/form-data** has been added under **Binary Meida Types**
![CreatePostAPI](/images/1/74.png?&width=90pc)


