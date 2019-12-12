## Systems Operations Control team
* James - HQ
* Jen - Marketing
* Jo - Legal / Finance
* John - Research & Development
* Alf - Development

## Networks - (VPC)

All environments should be configured to support multi availability zone distribution.

  * Developers
  * Marketing
  * Finance
  * Research & Development

### OUs from James, with a tentative mapping to VPCs from Alf

* Parent HQ
* R&D with its own VPC
* Prod - nominally and hierarchically
  * Marketing VPC
  * Finance / Legal VPC
* Dev with its own VPC

### Private IPs
* HQ 10.0.0.0/16
* Fin/leg 10.1.0.0/16
* marketing 10.2.0.0/16
* RD 10.3.0.0/16
* Dev 10.4.0.0/16

## Summary of Tasks (with exception of monitoring and SNS)

### (Tentative) Leads, VPCs, subnets and AWS services

* HQ - **CTO James**
  * OU
  * S3 for [centralized Cloudtrail](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-receive-logs-from-multiple-accounts.html)
  * IAM

* Developers - **admin Alf**
  * public subnet -- http outbound
  and EC2 for bastion host
  * private subnet -- dokuwiki docker and mysql, EC2 required for updates
  * cloudwatch for docker
  * private S3 with replication
  * S3 bucket for cloudtrail (with lifecycle) - give S3 ARN to James

* Research & Development - **admin John**
  * public subnet -- http outbound
  and EC2 for bastion host and nat gateway
  * private subnet -- EC2 for updates nat in route table
  * S3 bucket for cloudtrail (with lifecycle) - give S3 ARN to James

* Marketing - **admin Jen**
  * public subnet -- http outbound
  and EC2 for bastion host
  * private subnet - wordpress docker and EC2 for updates
  * cloudwatch for docker
  * marketing public static site S3 with replication - this will require EIP
  * marketing private S3 with replication
  * S3 bucket for cloudtrail (with lifecycle) - give S3 ARN to James

* Finance - **admin Jo**
  * public subnet -- http outboud and EC2 for bastion host
  * ~~finance private~~
  * legal private - for wordpress and EC2 for updates
  * cloudwatch for docker
  * legal S3 with replication
  * S3 bucket for cloudtrail (with lifecycle) - give S3 ARN to James

* Production - no specific AWS requirements. no AWS VPC infrastructure. There might be some on-premise infrastructure but not specified yet.