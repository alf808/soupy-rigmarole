# Team 1, Acme Corp.

You must architect, design and create the Enterprise infrastructure for Acme Corp. The provided requirements are high level needs as identified by various stakeholders but should not be considered complete in detail. 

It is assumed that the Systems Operations Control team will analyze all requirements and create the appropriate configurations and document the outcome of their analysis and deployment.

Requirements for deliverables from this project include:

[]  * Documentation of configurations and defined policies **ALL OF US**

[]  * Systems architecture diagrams - **ALL OF US**

[]  * Network architecture diagrams - **John**

[] **NOTE:** All setups should be verified to be working properly to be considered complete.

[] **NOTE:** If you are going to use any services that incur a cost you must report what that service will cost daily/monthly. **James**


## Organizations - James (OU)
The company will need to manage multiple organizations units to separate divisions within the business.

  * Ensure that all organizations cannot disable CloudTrail logging in any organization via Service Control Policy
  * Production
  * Development
    * [x] The development environment should only have access to development related resources
    * [x] Prevent users within the Development environment from disabling Config or changing any of it's rules - **only SysOps have config access (alf)**
  * Legal
  * Research & Development
    * The research environment should never allow servers to be accessible from the public internet via Internet Gateway, connecting to a NAT Gateway temporarily to patch systems or download software is acceptable

## Users/Groups/Roles - (IAM)

* [x] All organizations should have best practices applied for password security and console access policies - **only group SysOps have access to config (alf)**
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

  * private S3 with replication

    * **TODO**

  * S3 bucket for cloudtrail (with lifecycle) - give S3 ARN to James
    * lifecycle rule
    ![lifecycle cloudtrail](lifecycle_cloudtrail.png)
  * COSTS
    * EIP for nat (4 hours 19 cents)
    * S3 for cloudtrail:
    ```
    arn:aws:s3:::team1-acme-dev-cloudtrail/AWSLogs/427723548567/*
    ```
    * GuardDuty - 30-day free trial
    * Inspector - 30-day free trial
    * S3 with replication
    * Config 

  * Server patching with Systems Manager (SSM)
    * created a role with policy **AmazonSSMManagedInstanceCore** and attached to EC2 instances to be patched and then set up patch manager in Systems Manager

    * patch description
![patch description](patch_description.png)

    * patch task
![patch task](patch_task.png)

    * patch history
![patch history](patch_history.png)

  * dashboard to monitor key system metrics and network traffic
![dashboard cloudwatch](dashboard_keymetrics.png)

  * standard configuration in Config
    * public
![public ec2](config_ec2_public_snapshot_history.png)
    * private
![private ec2](config_snapshot_1_38.png)

  * Inspector
![inspector](inspector_enabled.png)

  * GuardDuty
![guardduty enabled](guardduty_enabled.png)
  * Alerts - SNS
    * Rules
![rules](cloud_alerts.png)
    * email alerts
![emails](cloud_tamper_email.png)

Developers need a secure location to store file uploads from various applications, these files should support versioning

![dev bucket](dev_bucket_versioning.png)

with replication rule to create redundancy

![replication](s3_replication_rule.png)

### monitoring the root  
Send an alert whenever someone logs in with the root user account for any organization

![cloudwatch alert root activity](cloudwatch_root_alarm.png)

SNS notification
![email notification](notification_root_activity.png)

## Documents - (S3 with Replication)

  * [x] Documents should be ensured to be stored in multiple regions for reliable backups and guaranteed accessibility
  * Legal needs a secure location to store legal documents that are not accessible by the public or any other users outside of their group
  * Marketing needs a secure location to store creative and digital assets
  * [x] Developers need a secure location to store file uploads from various applications, these files should support versioning 
  * Ensure that files are stored securely

## Websites - Jen and Jo

  * The marketing department needs a simple static website that can be accessed quickly from anywhere in the world - **S3 -- one public for website and one private for internal assets**
  * The legal department needs a Wordpress installation to manage corporate intranet assets - **EC2 or Docker**

## Servers - Alf (EC2)

  * [x] Servers should be accessible via bastion over SSH but not publicly accessible via SSH w/ the exception of bastion(s)

    * [bastion lecture 1](https://youtu.be/B5qx2QQ3UaY?t=3113)
    * [bastion lecture 2](https://youtu.be/QURN-nJJZj4?t=2756)
  * [x] Servers should be able to be patched any time with Systems Manager
  * [x] Servers should store their standard configuration in Config
  * [0] All servers should use a User Data script upon startup to install and configure an agent to send custom CloudWatch events for CPU monitoring

      * [cloud-init](https://aws.amazon.com/premiumsupport/knowledge-center/execute-user-data-ec2/)
      * [cloud watch user data](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/QuickStartEC2Instance.html)

  * [0] All servers that host applications should use a User Data script upon startup to send Docker logs to CloudWatch **(hard mode)**
    * [cloud-init](https://aws.amazon.com/premiumsupport/knowledge-center/execute-user-data-ec2/)
    * [user data](https://docs.docker.com/config/containers/logging/awslogs/)

## Applications - Jen and Alf

  * [x] Dokuwiki in a Docker container for the developers to use - **Alf**
  * Wordpress in a Docker container for the marketing staff to use - **Jen**
  * [x] The developers will need a MySQL database installed on a small EC2 - **Alf**
  * Enable WAF for the Wordpress application and configure it to block all traffic containing possible XSS and SQLi attempts - **Jen**

## Databases - depends on which database

  * [x] Databases should be on private subnets and never be available from the internet
  * Wordpress should use a small RDS with proper security controls

## Logs

  * Ensure that all logs are tamper proof from all organizations to ensure non-repudiation by sending all Cloudtrail logs to a single organization ([centralized Cloudtrail documentation](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-receive-logs-from-multiple-accounts.html)) - **James**

  * [x] Ensure that logs are readily accessible for a period of 30 days and create an archival strategy/implementation that logs are stored by the organization for 90 days and then deleted (cloudtrail in each division with S3 Lifecycle set up) - **All admins**
  [lifecycle lecture](https://youtu.be/mVdgMx_vgv8?t=3860)
  * [x] Auditors should have read-only access for all logs across the organization - **James and Admins**

  * [x] All applications should have a valid logging solution - **Jen, Jo, Alf**

    [flow logs lecture](https://youtu.be/B5qx2QQ3UaY?t=5461)

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


  * [x] Create a dashboard in each account to monitor key system metrics and network traffic - Cloudwatch
  * [x] Enable [GuardDuty](https://aws.amazon.com/guardduty/) to monitor baseline activity and anomolies across the system
  * [x] Enable [Inspector](https://aws.amazon.com/inspector/) and configure to run nightly scans of all of your applications and networks

## Alerts - SNS, group, low priority

  * [x] Send an alert whenever Cloudtrail controls are tampered with
  * [x] Send an alert whenever CloudWatch controls are tampered with
  * [x] Send an alert whenever someone logs in with the root user account for any organization