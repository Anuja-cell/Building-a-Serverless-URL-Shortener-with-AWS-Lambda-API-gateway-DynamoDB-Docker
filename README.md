# Building-a-Serverless-URL-Shortener-with-AWS-Lambda-API-gateway-DynamoDB-Docker
- No infrastructure overhead - Auto-scalable &amp; fault-tolerant - Cost-efficient - Easy deployment with Docker - Serverless simplifies infrastructure.
  
# AWS Serverless URL Shortener

This is a simple URL shortening service built using AWS Lambda, API Gateway and DynamoDB.

## Features
- Shorten long URLs into shorter, shareable links
- Store shortened URLs in DynamoDB
- Retrieve and redirect using API Gateway and Lambda

## Architecture
- **Lambda**: Handles URL shortening and retrieval.
- **DynamoDB**: Stores mapping between short and long URLs.
- **API Gateway**: Exposes endpoints for shortening and redirecting URLs.

## Prerequisites
- AWS CLI configured with necessary permissions
- Terraform installed
- IAM permissions to manage Lambda, API Gateway & DynamoDB.

## Setup Instructions

1. **Clone the repository:**
   ```sh
   git clone https://github.com/Anuja-cell/Building-a-Serverless-URL-Shortener-with-AWS-Lambda-API-gateway-DynamoDB-Docker.git
   cd url-shortner
   ```

2. **Deploy Lambda function:**
   ```sh
   lambda-function1.py
   lambda-function2.py
   aws lambda update-function-code --function-name url_shortner 
   ```

3. **Test API Gateway Endpoint:**
   ```sh
   curl -X POST https://your-api-gateway-url -d '{"long_url": "https://example.com"}'
   ```

## Common Errors & Fixes

### 1. **ResourceNotFoundException: DynamoDB Table Not Found**
#### **Error Message:**
```
An error occurred (ResourceNotFoundException) when calling the PutItem operation: Requested resource not found
```
#### **Cause:**
- The table name in your Lambda function code **does not match** the actual DynamoDB table name.

#### **Fix:**
- Verify the table name in the AWS Console under DynamoDB.
- Update the table reference in `lambda_function1.py`:
  ```python
  dynamodb = boto3.resource('dynamodb')
  table = dynamodb.Table('url_shortner')  # Ensure this matches the actual table name
  ```
- Redeploy the Lambda function.

### 2. **AccessDeniedException: Lambda Lacks Permission to Access DynamoDB**
#### **Error Message:**
```
User is not authorized to perform: dynamodb:GetItem on resource: arn:aws:dynamodb...
```
#### **Cause:**
- The Lambda function lacks the necessary IAM permissions to interact with DynamoDB.

#### **Fix:**
- Update the Lambda function's IAM role policy to include necessary permissions:
  ```json
  {
    "Effect": "Allow",
    "Action": [
      "dynamodb:PutItem",
      "dynamodb:GetItem",
      "dynamodb:Scan",
      "dynamodb:UpdateItem",
      "dynamodb:DeleteItem",
      "dynamodb:DescribeTable"
    ],
    "Resource": "arn:aws:dynamodb:<region>:<account_number>:table/url_shortner"
  }
  ```
- Attach this policy to the Lambda execution role in the IAM Console.

### 3. **Invalid API Gateway URL or Method**
#### **Error Message:**
```
403 Forbidden or 404 Not Found
```
#### **Cause:**
- The API Gateway endpoint is incorrect.
- The Lambda function is not properly integrated.

#### **Fix:**
- Verify the **Invoke URL** in the API Gateway console.
- Ensure the **Lambda function is correctly linked** to the API Gateway resource.
- Deploy the API Gateway changes:
  ```sh
  aws apigateway create-deployment --rest-api-id YOUR_API_ID --stage-name prod
  ```

## Conclusion
This serverless URL shortener provides an efficient way to shorten and manage URLs using AWS services. By following the setup instructions and troubleshooting common errors, you can successfully deploy and run this project.
