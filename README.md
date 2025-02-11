#AWS Services Used:
AWS Lambda → For processing requests.
Amazon API Gateway → To handle API calls.
Amazon DynamoDB → To store blood donor details.
Amazon S3 → To store donor documents or images.
AWS Cognito → For user authentication.
AWS SNS → To send emergency alerts to users.
Amazon CloudFront → For content delivery.
AWS IAM → For secure access control.

#Steps

Used for Deployment

import json
import boto3

dynamodb = boto3.resource('dynamodb')
table = dynamodb.Table('BloodBank')

def lambda_handler(event, context):
    blood_type = event.get("blood_type", "")
    
   
    response = table.scan(
        FilterExpression="bloodType = :b",
        ExpressionAttributeValues={":b": blood_type}
    )

    donors = response.get('Items', [])
    
    return {
        "statusCode": 200,
        "body": json.dumps({"available_donors": donors})
    }



1. Deploy AWS Lambda Function
Go to AWS Lambda Console → Create a new function.
Select "Author from Scratch" → Give a name (e.g., BloodBankHandler).
Choose Python 3.9 as the runtime.
Add Execution Role with permission to access DynamoDB.
Deploy the above Python script.



2. Connect Lambda to API Gateway
Go to Amazon API Gateway.
Create a new API → Select REST API.
Create a GET method and link it to the Lambda function.
Deploy API and get the Invoke URL.


3. Connect AWS SNS for Blood Alerts
Go to Amazon SNS.
Create a new topic → BloodAlerts.
Subscribe Users (Phone Numbers).
Use Lambda to Publish Alerts:

sns = boto3.client('sns')
sns.publish(TopicArn="arn:aws:sns:your-region:89574628:BloodAlerts",
            Message="Urgent! Blood needed for a patient.",
            Subject="Emergency Blood Request")



