# How AWS services use AWS KMS<a name="service-integration"></a>

Many AWS services use AWS KMS to support encryption of your data\. When an AWS service is integrated with AWS KMS, you can use the customer master keys \(CMKs\) in your account to protect the data that the service receives, stores, or manages for you\. For the complete list of AWS services that are integrated with AWS KMS, see [AWS Service Integration](https://aws.amazon.com/kms/features/#AWS_Service_Integration)\.

The following topics discuss in detail how particular services use AWS KMS, including the CMKs they support, how they manage data keys, the permissions they require, and how to track each service's use of the CMKs in your account\.

**Important**  
[AWS services that are integrated with AWS KMS](https://aws.amazon.com/kms/features/#AWS_Service_Integration) use symmetric CMKs to encrypt your data\. These services do not support encryption with asymmetric CMKs\. For help determining whether a CMK is symmetric or asymmetric, see [Identifying symmetric and asymmetric CMKs](find-symm-asymm.md)\.

**Topics**
+ [AWS CloudTrail](services-cloudtrail.md)
+ [Amazon DynamoDB](services-dynamodb.md)
+ [Amazon Elastic Block Store \(Amazon EBS\)](services-ebs.md)
+ [Amazon Elastic Transcoder](services-et.md)
+ [Amazon EMR](services-emr.md)
+ [AWS Nitro Enclaves](services-nitro-enclaves.md)
+ [Amazon Redshift](services-redshift.md)
+ [Amazon Relational Database Service \(Amazon RDS\)](services-rds.md)
+ [AWS Secrets Manager](services-secrets-manager.md)
+ [Amazon Simple Email Service \(Amazon SES\)](services-ses.md)
+ [Amazon Simple Storage Service \(Amazon S3\)](services-s3.md)
+ [AWS Systems Manager Parameter Store](services-parameter-store.md)
+ [Amazon WorkMail](services-wm.md)
+ [WorkSpaces](services-workspaces.md)