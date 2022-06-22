# Stop-RDS-Using-Lambda

Stopping RDS instance that has no Database Connection after a period of time  when an alarm is triggered.


Managing RDS services in the AWS environment can sometimes be tedious. During the testing period a developer can spin up an RDS instance and forget to stop it. This might be after terminating the EC2 instances or other endpoints connecting to the RDS. The RDS sits idle hence racking up on costs.

The aim of the above solution is to automate stopping RDS instances that have been idle for a period with no connections made to it. A CloudWatch Alarm is created to monitor the DB Connections. If the alarm goes into alarm state it triggers an EventBridge Rule. The EventBridge invokes a lambda function that obtains the DBIdentifier from the Event and stops the RDS with the alarm.

The RDS instance should bee tagged with Key “AutoStop” and Value “True” 

**Steps to Follow**
1.	Create a CloudWatch alarm for the Database Connection metric to monitor the Sum of DB Connections made in a period. If the value is less than 1 over the provided period it triggers it to Alarm state.
2.	Create an IAM policy that will be used to stop the RDS. The policy is in the code (iampolicy.json) . 
3.	Create a Lambda Role and assign the policy created above and AWSLambdaBasicExecutionRole.
4.	Create the stop Lambda Function and assign the Lambda Role created (stoprds.py)
5.	Create an EventBridge Rule with the rule type being Rule with an Event Pattern. On the Event Pattern chose the service as CloudWatch and Event Type as CloudWatch State Change. In the Json event pattern, input (eventbridge.json) . The target will be the stop Lambda function created.
