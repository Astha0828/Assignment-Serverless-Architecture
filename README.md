import boto3

def lambda_handler(event, context):
    ec2 = boto3.client('ec2')
    action = event.get('action', '').lower()

    filters = [{'Name': 'tag:AutoStartStop', 'Values': ['true']}]
    instances = ec2.describe_instances(Filters=filters)

    instance_ids = []
    for reservation in instances['Reservations']:
        for instance in reservation['Instances']:
            instance_ids.append(instance['InstanceId'])

    if not instance_ids:
        return {"message": "No matching instances found"}

    if action == 'start':
        ec2.start_instances(InstanceIds=instance_ids)
        return {"message": f"Started: {instance_ids}"}
    elif action == 'stop':
        ec2.stop_instances(InstanceIds=instance_ids)
        return {"message": f"Stopped: {instance_ids}"}
    else:
        return {"error": "Invalid action"}

{
  "action": "start"
}
{
  "action": "stop"
}
# EC2 Instance Auto Manager using AWS Lambda

This AWS Lambda function automatically starts or stops EC2 instances based on a specific tag (`AutoStartStop=true`).

## How it works

- Scans all EC2 instances with tag `AutoStartStop=true`
- Uses the input `action` from event (either `start` or `stop`)
- Manages instances accordingly

## Prerequisites

- IAM role with EC2 permissions
- Python 3.x
- boto3 (AWS SDK)

## Sample Event (event.json)

```json
{
  "action": "stop"
}
