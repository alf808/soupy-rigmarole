# Acme Corp: Team 1
## Deliverables
- [x] Documentation of configurations and defined policies 

- [x] Systems architecture diagrams

- [x] Network architecture diagrams

- [x] All setups should be verified to be working properly to be considered complete.

- [x] If you are going to use any services that incur a cost you must report what that service will cost daily/monthly.

## OU
- [ ] Manage multiple organizations units to separate divisions within the business

- [ ] Ensure that all organizations cannot disable CloudTrail logging in any organization via Service Control Policy

**Development**

- [x] The development environment should only have access to development related resources
- [x] Prevent users within the Development environment from disabling Config or changing any of it's rules - **only SysOps have config access**

## IAM
- [x] All organizations should have best practices applied for password security and console access policies - **Auditors do not have programmatic access**

- [x] Appropriate groups should be created with multiple users created for various groups

## Networks - VPC
- [x] All environments should be configured to support multi availability zone distribution. **TODO: chart**

- [x] NAT Gateway temporarily to patch systems or download software

## Documents - S3 with replication
- [x] Developers need a secure location to store file uploads from various applications, these files should support versioning

- [x] Documents should be ensured to be stored in multiple regions for reliable backups and guaranteed accessibility

- [x] Ensure that files are stored securely

## Websites and Apps

**Marketing**
- [ ] The marketing department needs a simple static website that can be accessed quickly from anywhere in the world - **S3 with replication**

- [ ] Wordpress in a Docker container for the marketing staff to use

- [ ] Enable WAF for the Wordpress application and configure it to block all traffic containing possible XSS and SQLi attempts

**Developers**
- [x] The developers will need a MySQL database installed on a small EC2

- [x] Dokuwiki in a Docker container for the developers to use

**Finance / Legal**
- [x] The legal department needs a Wordpress installation to manage corporate intranet assets

## Servers - EC2
- [x] Servers should be accessible via bastion over SSH but not publicly accessible via SSH w/ the exception of bastion(s)

**SSM**
- [x] Servers should be able to be patched any time with Systems Manager

**Config**
- [x] Servers should store their standard configuration in Config

**User Data**
- [x] All servers should use a User Data script upon startup to install and configure an agent to send custom CloudWatch events for CPU monitoring

- [x] All servers that host applications should use a User Data script upon startup to send Docker logs to CloudWatch

## Databases
- [x] Databases should be on private subnets and never be available from the internet

- [x] Wordpress should use a small RDS with proper security controls

## Logs
- [x] Ensure that all logs are tamper proof from all organizations to ensure non-repudiation by sending all Cloudtrail logs to a single organization - HQ

- [x] Ensure that logs are readily accessible for a period of 30 days and create an archival strategy/implementation that logs are stored by the organization for 90 days and then deleted

- [x] Auditors should have read-only access for all logs across the organization

- [x] All applications should have a valid logging solution

## Monitoring
**Cloudwatch, SNS, Lambda**
- [x] Operations should get alerts whenever an EC2 changes goes into a stopped state and when the EC2 is remediated

- [x] Operations should get alerts whenever and EC2 is terminated

- [ ] Operations should get alerts whenever an EC2 is started and doesn't comply to standard configuration, the server should also be terminated

- [x] Create a dashboard in each account to monitor key system metrics and network traffic

**GuardDuty**
- [x] Enable [GuardDuty](https://aws.amazon.com/guardduty/) to monitor baseline activity and anomalies across the system

**Inspector**
- [x] Enable [Inspector](https://aws.amazon.com/inspector/) and configure to run nightly scans of all of your applications and networks

## Alerts - SNS
- [x] Send an alert whenever Cloudtrail controls are tampered with

- [x] Send an alert whenever CloudWatch controls are tampered with

- [x] Send an alert whenever someone logs in with the root user account for any organization
