# cf deployment instructions

Copy template from DEV env. Skip if you pushed changes from this repo

```bash
aws s3 cp s3://notification-system-templates/ notification-system.yaml
```

Copy into dedicated AWS account

```bash
aws s3 cp notification-system.yaml s3://$BUCKETNAME/
```

Create a new change set (please adjust settings to proper RxFunction or alerting email endpoint)

```bash
aws cloudformation create-change-set --stack-name $STACKNAME 
--template-url https://$BUCKETNAME.$AWSREGION.amazonaws.com/notification-system.yaml 
--change-set-name alerting-and-monitoring 
--parameters ParameterKey=CodeBucket,ParameterValue=jem-code-persistence ParameterKey=Version,ParameterValue=0.0.25
ParameterKey=RxArn,ParameterValue=arn:aws:lambda:eu-west-1:075098069349:function:bl-stack-persistence-RxFunction-1JZD03GNFDHKJ
ParameterKey=apiGatewayStageName,ParameterValue=api ParameterKey=DeployTransferService,ParameterValue=false ParameterKey=FirstDeploy,ParameterValue=false
ParameterKey=MonitoringEmail,ParameterValue="$ALERTINGEMAIL" 
--capabilities CAPABILITY_IAM
```

Create change set takes a while. Wait about 1-2 minutes and trigger last command

```bash
aws cloudformation execute-change-set --change-set-name alerting-and-monitoring --stack-name $STACKNAME
```

Done. Have fun!
