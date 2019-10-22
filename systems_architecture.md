Team 1, Acme Corp. Developer Systems checklist

## EC2

EC2 instance with Amazon Linux 2 for public (just bastion)
* restrictive security group
EC2 instance with Amazon Linux 2 for private for MySQL
* inbound 3306, outbound http(s) 80, 443
EC2 instance with Amazon Linux for private Dokuwiki
* inbound http

## S3
S3 for cloudtrail with lifecycle
* S3 for logging purposes, auditors read-only

S3 for file uploads with versioning and replication
* internal use only

## Apps
Docker - service to run docker apps

Awslogs - service to allow Docker app logging

Dokuwiki in an EC2
* internal use only

MySQL in an EC2
* internal use only

SSM to patch EC2
* patch all instances

Legal Wordpress in EC2
* internal within Legal network

RDS
* MySQL for legal Wordpress

## Cloudwatch
Cloudwatch events for CPU monitoring and EC2 instances monitoring

## SNS topics

## Lambda Function
* used for EC2 instance monitoring

## Cloudwatch dashboard

## GuardDuty
* real-time monitoring of instances

## Inspector
* monitor logs