# Team 1, Acme Corp.

It is assumed that the Systems Operations Control team will analyze all requirements and create the appropriate configurations and document the outcome of their analysis and deployment.

Requirements for deliverables from this project include:

[]  * Documentation of configurations and defined policies **ALL OF US**

[]  * Systems architecture diagrams - **ALL OF US**

[]  * Network architecture diagrams - **John**

[] **NOTE:** All setups should be verified to be working properly to be considered complete.

[] **NOTE:** If you are going to use any services that incur a cost you must report what that service will cost daily/monthly.

  * COSTS
    * EIP for nat (4 hours 19 cents)
    * S3 for cloudtrail
    * GuardDuty - 30-day free trial
    * Inspector - 30-day free trial
    * S3 with replication
    * Config 

## Organizations - (OU)
The company will need to manage multiple organizations units to separate divisions within the business.

  * Ensure that all organizations cannot disable CloudTrail logging in any organization via Service Control Policy

  * Development
    * [x] The development environment should only have access to development related resources
    * [x] Prevent users within the Development environment from disabling Config or changing any of it's rules - **only SysOps have config access (alf)**

## Users/Groups/Roles - (IAM)

* [x] All organizations should have best practices applied for password security and console access policies - **Auditors do not have programmatic access**
* [x] Appropriate groups should be created with multiple users created for various groups

## Networks - (VPC)

[x] All environments should be configured to support multi availability zone distribution.

  * [x] Developers - **todo: chart**
  * Marketing
  * Finance
  * Research & Development

## Summary of Tasks (with exception of monitoring and SNS)

### (Tentative) Leads, VPCs, subnets and AWS services


* Developers
## IAM
MFA to be done soon
![security status](security_status.png)

Only SysOps, NetworkAdmins, and DBAdmins have programmatic access. Auditors only have console access
![groups](iam_groups.png)


  * public subnet -- http(s) outbound, SSH inbound with EC2 for bastion host with internet gateway and temporarily a NAT gateway
  
  * CONFIG file in host machine for bastion host setup
```
Host team1-acme-dev-public
   Hostname xx.xx.xx.xx
   User ec2-user
   Port 22
   ForwardAgent yes
   IdentityFile ~/.ssh/team1-acme-dev-key.pem

Host team1-acme-dev-private
   User ec2-user
   ProxyCommand ssh team1-acme-dev-public nc 10.4.2.162 22
   ForwardAgent yes
   IdentityFile ~/.ssh/team1-acme-dev-key.pem
```
  * **NAT gateway attached to public instance**
![nat gateway](nat_gateway.png)

  * **Private Route Table with reference to NAT gateway**
![route table](nat_gateway_in_rtb.png)

  * bastion worked
![bastion worked](bastion_worked.png)

Developers need a secure location to store file uploads from various applications, these files should support versioning. Block public access

![dev bucket](dev_bucket_versioning.png)

with replication rule to create redundancy

![replication](s3_replication_rule.png)

  * private subnet -- dokuwiki docker and mysql, EC2 required for updates
  SSH inbound, mysql inbound

  * dokuwiki for developer internal use

```
sudo yum install docker
sudo systemctl start docker
sudo docker images
```
  * awslogs service installation
```
sudo yum install awslogs
sudo service awslogs start

sudo docker run --restart always -d -p 80:80 --log-driver=awslogs --log-opt awslogs-region=us-west-2 --log-opt awslogs-group=myNewDockerLogGroup --log-opt awslogs-create-group=true mprasil/dokuwiki

elinks http://localhost/install.php
  ```
~~account: nemo, pwd:~~
accessible only from within internal network
  ![dokuwiki for dev](dokuwiki_from_publicSG_to_privateEC2.png)

  * MySQL for internal use(mariadb) only within internal network
  ![mysql for dev](mysql_for_internal_use.png)
  * cloudwatch for docker
```  
sudo yum install -y awslogs
sudo systemctl start awslogsd
sudo systemctl enable awslogsd.service
```
    * created a cloudwatch agent
    with the following policies:
```
{
    "Effect": "Allow",
    "Action": [
        "cloudwatch:PutMetricData",
        "ec2:DescribeTags",
        "logs:PutLogEvents",
        "logs:DescribeLogStreams",
        "logs:DescribeLogGroups",
        "logs:CreateLogStream",
        "logs:CreateLogGroup"
    ],
    "Resource": "*"
}
```

![docker cloudwatch](docker_cloudwatch.png)

### Legal wordpress

  * RDS MySql for Wordpress Legal
![wordpress RDS](wp_rds_mysql_legal.png)

  * Wordpress Legal
![wordpress for legal](wp_private_legal.png)

  * signature groups
![rds sg](rds_legal_sg.png)
![ec2 sg](ec2_legal_sg.png)




  * S3 bucket for cloudtrail (with lifecycle) - give S3 ARN to James
    * lifecycle rule
    ![lifecycle cloudtrail](lifecycle_cloudtrail.png)

  * Server patching with Systems Manager (SSM)
    * created a role with policy **AmazonSSMManagedInstanceCore** and attached to EC2 instances to be patched and then set up patch manager in Systems Manager

    * patch description
![patch description](patch_description.png)

    * patch task
![patch task](patch_task.png)

    * patch history
![patch history](patch_history.png)


  * standard configuration in Config
    * public
![public ec2](config_ec2_public_snapshot_history.png)
    * private
![private ec2](config_snapshot_1_38.png)




    * https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/US_AlarmAtThresholdEC2.html

    * configure cloud watch agent: make sure that there's an IAM role attached to EC2 that will allow cloudwatch, ex: CloudWatchAgentServerRole
    * do the following in EC2 instance command line:

```
wget https://s3.amazonaws.com/amazoncloudwatch-agent/amazon_linux/amd64/latest/amazon-cloudwatch-agent.rpm
sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-config-wizard
sudo rpm -U ./amazon-cloudwatch-agent.rpm
sudo aws configure --profile AmazonCloudWatchAgent
sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl -a fetch-config -m ec2 -s
```
![cpu monitoring](cpu_monitoring.png)

![cpu util graph](cpu_utilization_graph.png)


  * [x] All servers that host applications should use a User Data script upon startup to send Docker logs to CloudWatch. https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/install-CloudWatch-Agent-commandline-fleet.html

    * Make sure that awslogs and docker services are installed
    * [user data](https://docs.docker.com/config/containers/logging/awslogs/):
```
Content-Type: multipart/mixed; boundary="//"
MIME-Version: 1.0

--//
Content-Type: text/cloud-config; charset="us-ascii"
MIME-Version: 1.0
Content-Transfer-Encoding: 7bit
Content-Disposition: attachment; filename="cloud-config.txt"

#cloud-config
cloud_final_modules:
- [scripts-user, always]

--//
Content-Type: text/x-shellscript; charset="us-ascii"
MIME-Version: 1.0
Content-Transfer-Encoding: 7bit
Content-Disposition: attachment; filename="userdata.txt"

#!/bin/bash
sudo service awslogs start
sudo service docker start
sudo docker run --restart always -d -p 80:80 --log-driver=awslogs --log-opt awslogs-region=us-west-2 --log-opt awslogs-group=myNewDockerLogGroup --log-opt awslogs-create-group=true --name mywikipad mprasil/dokuwiki:latest
--//
```

## Monitoring - Lambda and Cloudwatch, low priority

  * [x] Operations should get alerts whenever an EC2 changes goes into a stopped state and when the EC2 is remediated - L & C

![start EC2](startec2_lambda.png)

**Start EC2 code**
```
import json
import boto3

ec2 = boto3.resource('ec2')

def lambda_handler(event, context):
    id = event["detail"]["instance-id"] #get instance id from event
    
    instance = ec2.Instance(id)
    instance.start()

    return {
        'statusCode': 200,
        'body': 'Instance ID:' + json.dumps(instance.instance_id + ' started')
    }
```

**Cloudwatch rule**
![EC2 stop rule](cloudwatch_ec2_stop_rule.png)

  * [x] Operations should get alerts whenever and EC2 is terminated - L & C

![EC2 terminated rule](cloudwatch_ec2_terminate_rule.png)

  * [0] Operations should get alerts whenever an EC2 is started and doesn't comply to standard configuration, the server should also be terminated

  * dashboard to monitor key system metrics and network traffic
![dashboard cloudwatch](dashboard_keymetrics.png)


  * Inspector
![inspector](inspector_enabled.png)

  * GuardDuty
![guardduty enabled](guardduty_enabled.png)



  * Alerts - SNS
    * Rules
![rules](cloud_alerts.png)
    * email alerts
![emails](cloud_tamper_email.png)

### monitoring the root  
Send an alert whenever someone logs in with the root user account for any organization

![cloudwatch alert root activity](cloudwatch_root_alarm.png)

SNS notification
![email notification](notification_root_activity.png)