﻿# AutomatingVolumeSnapshotWithBotoInAWSLambda

 Steps to Complete the Challenge:
Create an IAM Role for Lambda:

Go to the IAM console and create a new role with the following policies: AWSLambdaBasicExecutionRole and AmazonEC2ReadOnlyAccess.

Create the Lambda Function:

Go to the Lambda console and create a new Lambda function.
Choose Python 3.x as the runtime.
Assign the IAM role you created to the Lambda function.
Copy and paste the script below into the Lambda function's code editor.
Set Up a CloudWatch Event Rule:

This rule will trigger the Lambda function on a scheduled basis (e.g., daily).
Go to the CloudWatch console, create a new rule, and set the schedule (e.g., rate(1 day)).
Add the Lambda function as the target of this rule.

Here is the Code: 

import boto3
import datetime

def lambda_handler(event, context):
    ec2_client = boto3.client('ec2', region_name='us-east-1')  # Replace with your region
    instance_id = 'i-0abcd1234efgh5678'  # Replace with your EC2 instance ID

    # Describe the instance to get volume information
    response = ec2_client.describe_instances(InstanceIds=[instance_id])
    volume_id = response['Reservations'][0]['Instances'][0]['BlockDeviceMappings'][0]['Ebs']['VolumeId']
    
    # Create a snapshot of the volume
    snapshot_description = f"Snapshot of {volume_id} taken on {datetime.datetime.now().strftime('%Y-%m-%d %H:%M:%S')}"
    snapshot_response = ec2_client.create_snapshot(
        VolumeId=volume_id,
        Description=snapshot_description
    )
    
    snapshot_id = snapshot_response['SnapshotId']
    print(f"Snapshot {snapshot_id} of volume {volume_id} created successfully")

    return {
        'statusCode': 200,
        'body': f"Snapshot {snapshot_id} of volume {volume_id} created successfully"
    }

