# Acme Corp: Team 1
## Deliverables
- [ ] Documentation of configurations and defined policies 

- [ ] Systems architecture diagrams

- [ ] Network architecture diagrams

- [ ] All setups should be verified to be working properly to be considered complete.

- [ ] If you are going to use any services that incur a cost you must report what that service will cost daily/monthly.

## OU
- [ ] Manage multiple organizations units to separate divisions within the business

- [ ] Ensure that all organizations cannot disable CloudTrail logging in any organization via Service Control Policy

## IAM
- [ ] All organizations should have best practices applied for password security and console access policies

- [ ] Appropriate groups should be created with multiple users created for various groups

## Networks - VPC
- [ ] All environments should be configured to support multi availability zone distribution.

- [ ] NAT Gateway temporarily to patch systems or download software

## Documents - S3 with replication
- [ ] Create a secure location to store documents

- [ ] Documents should be ensured to be stored in multiple regions for reliable backups and guaranteed accessibility

- [ ] Ensure that files are stored securely

## Websites and Apps

**Marketing**
- [ ] The marketing department needs a simple static website that can be accessed quickly from anywhere in the world - **S3 with replication**

- [ ] Wordpress in a Docker container for the marketing staff to use

- [ ] Enable WAF for the Wordpress application and configure it to block all traffic containing possible XSS and SQLi attempts

**Developers**
- [ ] The developers will need a MySQL database installed on a small EC2

- [ ] Dokuwiki in a Docker container for the developers to use

**Finance / Legal**
- [ ] The legal department needs a Wordpress installation to manage corporate intranet assets

## Servers - EC2
- [ ] Servers should be accessible via bastion over SSH but not publicly accessible via SSH w/ the exception of bastion(s)
### SSM
- [ ] Servers should be able to be patched any time with Systems Manager
### Config
- [ ] Servers should store their standard configuration in Config
### User Data
- [ ] All servers should use a User Data script upon startup to install and configure an agent to send custom CloudWatch events for CPU monitoring

- [ ] All servers that host applications should use a User Data script upon startup to send Docker logs to CloudWatch

## Databases
- [ ] Databases should be on private subnets and never be available from the internet

- [ ] Wordpress should use a small RDS with proper security controls

## Logs
- [ ] Ensure that all logs are tamper proof from all organizations to ensure non-repudiation by sending all Cloudtrail logs to a single organization

- [ ] Ensure that logs are readily accessible for a period of 30 days and create an archival strategy/implementation that logs are stored by the organization for 90 days and then deleted

- [ ] Auditors should have read-only access for all logs across the organization

- [ ] All applications should have a valid logging solution

## Monitoring
### Cloudwatch, SNS, Lambda
- [ ] Operations should get alerts whenever an EC2 changes goes into a stopped state and when the EC2 is remediated

- [ ] Operations should get alerts whenever and EC2 is terminated

- [ ] Operations should get alerts whenever an EC2 is started and doesn't comply to standard configuration, the server should also be terminated

- [ ] Create a dashboard in each account to monitor key system metrics and network traffic
### GuardDuty
- [ ] Enable [GuardDuty](https://aws.amazon.com/guardduty/) to monitor baseline activity and anomalies across the system
### Inspector
- [ ] Enable [Inspector](https://aws.amazon.com/inspector/) and configure to run nightly scans of all of your applications and networks

## SNS
- [ ] Send an alert whenever Cloudtrail controls are tampered with

- [ ] Send an alert whenever CloudWatch controls are tampered with

- [ ] Send an alert whenever someone logs in with the root user account for any organization
