```
aws ec2 describe-instances --filters Name=tag-key,Values=Name --query "Reservations[*].Instances[*].{Instance:InstanceId,AZ:Placement.AvailabilityZone,Name:Tags[?Key=='Name']|[0].Value,Type:InstanceType}" --output json --profile=specapp-stg

  

aws ec2 describe-volumes --output json --query "Volumes[*].{InstanceId:Attachments[0].InstanceId,Type:VolumeType,Size:Size,Iops:Iops,State:State}" --profile=specapp-stg --output table > specapp-stg-volumes

  

aws ec2 describe-nat-gateways --output table --filter Name=tag-key,Values=Name --query "NatGateways[*].{Id:NatGatewayId,VpcId:VpcId}" --profile=specapp-stg > specapp-stg-nat-gateways

  

aws rds describe-db-instances --query "DBInstances[*].{ID:DBInstanceIdentifier,Class:DBInstanceClass,AZ:AvailabilityZone}" --profile=specapp-stg --output table > specapp-stg-rds-instances-table
```

