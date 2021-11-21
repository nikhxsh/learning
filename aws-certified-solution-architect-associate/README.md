## Table of Contents  
- [AWS regions and Availability zones](#aws-regions-and-availability-zones)
  * [Regions](#regions)
  * [Availability Zones](#availability-zones)
  * [Local Zones](#local-zones)
  * [Edge Network Locations](#edge-network-locations)
  * [Wavelength](#aws-wavelength)
  * [Outposts](#aws-outposts)
- [IAM (Identity and Access Management)](#iam-identity-and-access-management)
  * [Users](#users)
  * [Groups](#-groups)
  * [Policies](#policies)
  * [Roles](#roles)
  * [Tools](#tools)
  * [Best practices](#best-practices)
  * [Q&N](#iam-qa)
- [EC2 (Elastic Compute Cloud)](#ec2-elastic-compute-cloud)
  * [Features](#features)
  * [Instances](#instances)
  * [AMI (Amazon Machine Image)](#ami-amazon-machine-image)
  * [Networking](#networking)
  * [Infrastructure security](#infrastructure-security)
  * [EC2 Storage](#ec2-storage)
  * [Notes](#notes)
  * [Dev](#dev)
  * [Q&N](#ec2-qa)
- [Scalability and High Availability](#scalability-and-high-availability)
  * [Scalability](#scalability)
  * [High Availability](#high-availability)
  * [Load Balancer](#load-balancer)
  * [Q&N](#scalability--availability-qa)
- [AWS Storage Services](#aws-storage-services)
  * [Relative Database Service](#relative-database-service)
  * [Aurora](#aurora)
  * [ElastiCache](#elasticache)
  * [S3 (Simple Storage Service)](#s3-simple-storage-service)
  * [Athena](#athena)
  * [RedShift](#redShift)
  * [Neptune](#neptune)
  * [ElasticSearch](#elasticSearch)
  * [Snow Family](#snow-family)
  * [S3 File Gateway](#s3-file-gateway)
  * [Q&N](#storage-services-qa)
- [Route 53](#route-53)
  * [Q&N](#route-53-qa)
- [CloudFront & AWS Global Accelerator](#cloudfront--aws-global-accelerator)
  * [CloudFront](#cloudfront)
  * [Global Accelerator](#global-accelerator)
  * [Q&N](#cloudfront--aws-global-accelerator-qn)
- [Decoupling applications](#decoupling-applications)
  * [SQS](#sqs)
  * [SNS (Simple Notification Service)](#sns-simple-notification-service)
  * [Kinesis](#kinesis)
  * [Data ordering for Kinesis vs SQS FIFO](#data-ordering-for-kinesis-vs-sqs-fifo)
  * [MQ](#mq)
  * [Q&N](#decoupling-applications-qa)
- [Container on AWS](#container-on-aws)
  * [Docker](#docker)
  * [ECS (Elastic Container Service)](#ecs-elastic-container-service)
  * [Fargate](#fargate)
  * [EKS (Elastic Kubernetes Service)](#eks-elastic-kubernetes-service)
  * [ECR (Elastic Container Registry)](#ecr-elastic-container-registry)
- [Serverless in AWS](#serverless-in-aws)
  * [Lambda](#lambda)
  * [DynamoDB](#dynamodb)
  * [API Gateway](#api-gateway)
  * [Cognito](#cognito)
  * [Glue](#glue)
  * [Q&N](#serverless-qa)
- [Deployment](#deployment)
  * [Elastic Beanstalk](#elastic-beanstalk)
- [Solution Architecture Discussions](#solution-architecture-discussions)
  * [Classic](#classic)
  * [Serverless](#serverless)
  * [Q&N](#case-studies-qa)
- [AWS Development](#aws-development)
  * [Q&N](#aws-development-qa)

## AWS regions and Availability zones
### Regions
- AWS has the concept of a Region, which is a physical location around the world where we cluster data centers
- Each AWS Region consists of multiple, isolated, and physically separate AZs within a geographic area
- AWS maintains multiple geographic regions like us-east1, us-west-2 etc.
- Most AWS services are region scoped
- How to choose Region?
	- Compliance with data governance and legality 
	- Proximity to Customers
	- Available services within region - new service not available in every region
	- Pricing which varies from region to region    
### Availability Zones
- Each region has many AZs (Usually 3, min 2, max 6) 
- Example:
	- ap-southeast-2a
	- ap-southeast-2b
	- ap-southeast-2c
- Each AZ is one or more discreet data centers with redundant power, network and connectivity
- Each AZs separated from each other to isolate from disaster
	- Ability to operate production applications and databases that are more highly available, fault tolerant, and scalable than would be possible from a single data center.
	- Each AZs connected with high bandwidth, low latency network
	- All traffic between AZs is encrypted
### Local Zones	
- Place compute, storage, database, and other select AWS services closer to end-users
- You can easily run highly-demanding applications that require single-digit millisecond latencies
### Edge Network Locations
- 216 Point of Presence (205 edge locations & 11 Regional cache) in 84 cities across countries
- Content delivered with lower latency
### Wavelength
- Enables developers to build applications that deliver single-digit millisecond latencies to mobile devices and end-users such as game and live video streaming, machine learning inference at the edge, and augmented and virtual reality
- Application traffic can reach application servers running in Wavelength Zones without leaving the mobile provider’s network
### Outposts
 - Bring native AWS services, infrastructure, and operating models to virtually any data center, co-location space, or on-premises facility
 - AWS Outposts is designed for connected environments and can be used to support workloads that need to remain on-premises due to low latency or local data processing needs   

## [IAM (Identity and Access Management)](https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction.html)
 - Global Service i.e. not scoped to regions
 - Helps you securely control access to AWS resources
 - Use IAM to control who is authenticated (signed in) and authorized (has permissions) to use resources
 - Root account created by default, shouldn't be used or shared
 - You manage access in AWS by creating policies and attaching them to IAM identities (users, groups of users, or roles) or AWS resources
### [Users](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users.html)
 - People within your organization
 - Can belong to multiple groups
 - Can inherit multiple policies
 - IAM users are not separate accounts; they are users within your account
 - An IAM user with administrator permissions is not the same thing as the AWS account [root user](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_root-user.html)
 - Use the ARN when you need to uniquely identify the user across all of AWS 
    E.g. arn:aws:iam::`account-ID-without-hyphens`:user/nik
### [Groups](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_groups.html)
 - Group is a collection of IAM users
 - Let you specify permissions for multiple users
 - Group is a way to attach policies to multiple users at one time
 - Groups can't be nested i.e. they can contain only users, not other user groups
### [Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies.html)
 - A policy is an object in AWS that, when associated with an identity or resource, defines their permissions
 - Users and groups can be assigned JSON documents called policies
 - IAM policies define permissions for an action of the users
 - Apply least privileged principle (don't give permission more than user needs)
 - Supports six types of policies
	 - Identity-based policies 
		 - Grant permissions to an identity (users, groups to which users belong, or roles)
		 - Identity-based policies grant permissions to an identity.
	 - Resource-based policies
		 - Inline policies to resources
		 - Resource-based policies grant permissions to the principal that is specified in the policy
	 - Permissions boundaries
		 - Use a managed policy as the permissions boundary for an IAM entity
		 - Defines the maximum permissions that the identity-based policies can grant to an entity, but does not grant permissions
	 - Organizations SCPs
		 - Define the maximum permissions for account members of an organization or organizational unit (OU). 
		 - SCPs limit permissions that identity-based policies or resource-based policies grant to entities (users or roles) within the account, but do not grant permissions.
	 - ACLs
		 - Control which principals in other accounts can access the resource to which the ACL is attached. 
		 - Only policy type that does not use the JSON policy document structure ACLs are cross-account permissions policies			    that grant permissions to the specified principal. 
		 - ACLs cannot grant permissions to entities within the same account.
	 - Session policies
		 - Pass advanced session policies when you use the AWS CLI or AWS API to assume a role or a federated user
		 - Session policies limit permissions for a created session, but do not grant permissions
### [Roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles.html)
 - An IAM Role is an identity that you can create in your account that has specific permissions
 - It is an AWS identity with permission policies that determine what the identity can and cannot do in AWS
 - When you assume a role, It provides you with temporary security credentials for your role session
 - You can use roles to delegate access to users, applications, or services that don't normally have access to your AWS    resources e.g. Sometimes you want to give AWS access to users who already have identities defined outside of AWS, such as in your corporate directory. Or, you might want to grant access to your account to third parties so that they can perform an audit on your resources
 - Common roles
	 - EC2 Instance role
	 - Lambda function role
	 - Roles for CloudFormation
### [STS (Security Token Service)](https://docs.aws.amazon.com/STS/latest/APIReference/welcome.html)
- Allows to grant IAM or Federated users a limited and temporary access to AWS resources
- Token is valid for up to one hour and then must be refreshed
- available as a global service, and all AWS STS requests go to a single endpoint at `https://sts.amazonaws.com`
- [AssumeRole](https://docs.aws.amazon.com/STS/latest/APIReference/API_AssumeRole.html) 
	- Returns a set of temporary security credentials that you can use to access AWS resources that you might not normally have access to
	- *Within your own account* - for enhanced security (IAM _user_ A and IAM role B where IAM role B confers some higher permissions, e.g. the ability to read sensitive logs in an S3 bucket. The value of having to assume role B versus simply giving user A access to the bucket is that what IAM user credentials are long-term, while IAM role/STS credentials are short-term. Exposure of the STS credentials is lower risk. You can also require MFA when assuming role B whereas it might typically be onerous for the IAM user to supply MFA for everyday usage)
	- *Cross Account Access* - assume role in target account to perform action there, find example [here](https://blog.container-solutions.com/how-to-create-cross-account-user-roles-for-aws-with-terraform)
- [AssumeRoleWithSAML](https://docs.aws.amazon.com/STS/latest/APIReference/API_AssumeRoleWithSAML.html)
	- Returns a set of temporary security credentials for users who have been authenticated via a SAML authentication response
- [AssumeRoleWithWebIdentity](https://docs.aws.amazon.com/STS/latest/APIReference/API_AssumeRoleWithWebIdentity.html)
	- Returns a set of temporary security credentials for users who have been authenticated in a mobile or web application with a web identity provider. 
	- Example providers include Amazon Cognito, Login with Amazon, Facebook, Google, or any OpenID Connect-compatible identity provider
	- AWS does not recommend to use above identity providers but using *Cognito* instead
- [GetSessionToken](https://docs.aws.amazon.com/STS/latest/APIReference/API_GetSessionToken.html)
	- Returns a set of temporary credentials for an AWS account or IAM user
	- MFA-enabled IAM users would need to call `GetSessionToken` and submit an MFA code that is associated with their MFA device
	- Using the temporary security credentials that are returned from the call, IAM users can then make programmatic calls to API operations that require MFA authentication
- [Using STS to Assume a Role](https://aws.amazon.com/blogs/security/how-to-use-a-single-iam-user-to-easily-access-all-your-accounts-by-using-the-aws-cli/)
	- Define IAM role within your account or cross-account
	- Define which principle can access this IAM role
	- Use STS to retrieve credentials and impersonate the IAM role you have access to (AssumeRole API) 
	- Temporary credentials can be valid between 15 min to 1 hour

### [Identity federation](https://aws.amazon.com/identity/federation/)
- Identity federation is a system of trust between two parties for the purpose of authenticating users and conveying information needed to authorize their access to resources
- Les users outside of AWS to assume temporary role for accessing AWS resources
- These users assume identity provided access role
- Using federation, you don't need to create IAM users (user management s outside of AWS)
- Flavors
	- [SAML 2.0 Federation](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_providers_saml.html)
		- To integrate AD/ADFS with AWS
		- Provides access o AWS console or CLI (using temp creds)
		- No need to create an IAM users for each of your employees
		- Using SAML-based federation for API access to AWS ![enter image description here](https://docs.aws.amazon.com/IAM/latest/UserGuide/images/saml-based-federation.diagram.png)
		- SAML-enabled single sign-on ![enter image description here](https://docs.aws.amazon.com/IAM/latest/UserGuide/images/saml-based-sso-to-console.diagram.png)
		- AWS Federated Authentication with Active Directory Federation Services ([AD FS](https://aws.amazon.com/blogs/security/aws-federated-authentication-with-active-directory-federation-services-ad-fs/)) ![enter image description here](https://d1.awsstatic.com/security-center/SecurityBlog/federated_auth_with_adfs_25.dc86ecbfbbf80af3f553e7374d2a55ad1afb7016.png)
		- Needs to setup a trust between IAM and SAML (both ways)
		- SAML 2.0 enable web-based, cross domain SSO
		- Uses the STS API:`AssumeRoleWithSAML`
		- Federation through SAML is *the old way of doing SSO* and *Amazon SSO* is new managed and simpler way
	- [Custom Identity Broker](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_common-scenarios_federated-users.html)
		- Use only if identity provider not compatible with SAML 2.0
		- Identity broker must determine the appropriate IAM policy
		- Uses the STS API: `AssumeRole` or `GetFederationToken` ![enter image description here](https://docs.aws.amazon.com/IAM/latest/UserGuide/images/enterprise-authentication-with-identity-broker-application.diagram.png) 
	- [Web Identity Federation without Amazon Cognito](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/WIF.html)
		- Not recommended by AWS and use Cognito instead
		- If you are writing an application targeted at large numbers of users, you can optionally use this for authentication and authorization
		- It removes the need for creating individual IAM users. Instead, users can sign in to an identity provider and then obtain temporary security credentials from AWS Security Token Service (AWS STS)
		- supports the identity providers *Amazon, Facebook & Google* ![enter image description here](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/wif-overview.png)
	- Web Identity Federation with Amazon Cognito 
		- Provides direct access to AWS resources from the client side (mobile & web app)
		- Allows for anonymous users, data sync, MFA
		- We don't want to create IAM users for our app users 
		- We can log in to *federated identity provider* or remain anonymous. So we will get temporary AWS creds back from the *federated identity pool* which come with pre-defined IAM policy stating their permissions
		- E.g. provide temporary access to write to S3 bucket suing Facebook login ![enter image description here](https://d2908q01vomqb2.cloudfront.net/1b6453892473a467d07372d45eb05abc2031647a/2017/06/18/CognitoDiagram.png)
		- [Common Amazon Cognito Scenarios](https://docs.aws.amazon.com/cognito/latest/developerguide/cognito-scenarios.html)
	- [Single Sign-On](https://aws.amazon.com/blogs/security/introducing-aws-single-sign-on/)
		- Centrally manage Single Sign-On to access **multiple accounts** and **3rd party business applications**
		- Integrated with AWS Organizations
		- Integration with on-premise AD
		- Centralized permission manager
		- Centralized  Auditing ![enter image description here](https://d2908q01vomqb2.cloudfront.net/22d200f8670dbdb3e253a90eee5098477c95c23d/2017/12/07/AM_AWSSSOUseCases_120717a.png)
	- Non-SAML with AWS Microsoft AD
	
### [Directory Service](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/what_is.html)
- Provides multiple ways to use Microsoft Active Directory (AD) with other AWS services
- Directories store information about users, groups, and devices
- Administrators use them to manage access to information and resources
- [MS Active Directory](https://searchwindowsserver.techtarget.com/definition/Active-Directory)
	- Found on any windows server with AD Domain service
	- Database of objects: User Accounts, Computers, Printers, File Shares, Security groups
	- Centralized security management, create account, assign permissions
	- Object are organized in **trees**
	- A group of tress is **forest** ![enter image description here](https://images.slideplayer.com/30/9527340/slides/slide_2.jpg)
	- Directory services
		- [AWS Managed Microsoft AD](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/directory_microsoft_ad.html)
			- Create your own AD in AWS, manage users locally, supports MFA
			- Establish "Trust" connections with your on-premise AD
			- [Example](https://aws.amazon.com/blogs/security/enable-office-365-with-aws-managed-microsoft-ad-without-user-password-synchronization/)
		- [Active Directory Connector](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/directory_ad_connector.html)
			- Directory gateway (proxy) to redirect to on-premise AD
			- Users are managed on the on-premise AD
			- [Example](https://aws.amazon.com/blogs/security/how-to-connect-your-on-premises-active-directory-to-aws-using-ad-connector/)
		- [Simple Active Directory](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/directory_simple_ad.html)
			- AD-compatible managed directory on AWS
			- Cannot be joined with on-premise AD
		- [Which to choose](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/what_is.html#cognito)

### [Organizations](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_introduction.html)
- Global service
- Allows to manage multiple AWS accounts
- The main account is the master account - you can't change it
- Other accounts are member accounts
- Member accounts can only be part of one organization
- Consolidated Billing across all accounts - single payment method
- Pricing benefits from aggregated usage (volume discounts for EC2)
- API availble to automate AWS account creation ![enter image description here](https://docs.aws.amazon.com/organizations/latest/userguide/images/AccountOuDiagram.png)
- Multi Account Strategies
	- Create accounts 
		- per department
		- per cost center
		- per dev/test/prd
		- based on regulatory restriction (using SCP)
		- for better resource isolation (e.g. VPC)
		- to have separate per-account service limits
		- for isolated account for logging
	- One account Multi VPC
	- Use tagging standards for billing purpose
	- Enable CloudTrail on all account, send logs to central S3 account
	- Establish Cross Account Roles for Admin purposes
- Organizational units
	- Group accounts together to administer as a single unit
	- You can attach a policy-based control to an OU, and all accounts within the OU automatically inherit the policy
	- You can create multiple OUs within a single organization, and you can create OUs within other OUs
	- Each OU can contain multiple accounts, and you can move accounts from one OU to another
	- OU names must be unique within a parent OU or root ![enter image description here](https://miro.medium.com/max/624/1*MaXq4s60cUxlSe-ycmL-KA.png)
- [Service Control Policies](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_policies_scps.html)
	- Whitelist or blacklist IAM actions
	- Applied at the OU or Account level
	- Does not apply to the Master Account
	- Applied to all the **Users** and **Roles** of the account, including **Root**
	- Does not affect service-linked roles (service linked roles enable other AWS services to integrate with AWS *Organizations* and can't be restricted by SCPs)
	- SCP must have an explicit Allow (does not allow anything by default)
	- Use cases
		- Restrict access to certain services 
		- Enforce PCI compliance by explicitly disabling services
	- SCP Hierarchy![enter image description here](https://d2908q01vomqb2.cloudfront.net/22d200f8670dbdb3e253a90eee5098477c95c23d/2018/03/28/service-control-policies-04.png)

### [IAM Conditions](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_condition.html)
- The `Condition` element (or `Condition`  _block_) lets you specify conditions for when a policy is in effect
- E.g. restrict the client IP from which the API calls are being made
	```json
	{
	    "Version": "2012-10-17",
	    "Statement": {
	        "Effect": "Deny",
	        "Action": "*",
	        "Resource": "*",
	        "Condition": {
	            "NotIpAddress": {
	                "aws:SourceIp": [
	                    "192.0.2.0/24",
	                    "203.0.113.0/24"
	                ]
	            },
	            "Bool": {"aws:ViaAWSService": "false"}
	        }
	    }
	}
	```
- Find example of conditional policies [here](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_examples.html)

### [IAM for S3](https://docs.aws.amazon.com/config/latest/developerguide/s3-bucket-policy.html)
- Bucket level permissions
	- `ListBucket` permission applies to `arn:aws:s3::test`
	- E.g. 
		```json
		{
		  "Version": "2012-10-17",
		  "Statement": [
		    {
		      "Sid": "AWSConfigBucketPermissionsCheck",
		      "Effect": "Allow",
		      "Action": ["s3:ListBucket"],
		      "Resource": ["arn:aws:s3:::test"],
		      "Condition": { 
		        "StringEquals": {
		          "AWS:SourceAccount": "{sourceAccountID}"
		        }
		      }
		    }
		  ]
	    }
		```
- Object level permission
	- `GetObject`, `PutObject` and `DeleteObject` applies to `arn:aws:s3::test/*`
	- E.g. 
		```json
		{
		  "Version": "2012-10-17",
		  "Statement": [
		    {
		      "Sid": "AWSConfigBucketPermissionsCheck",
		      "Effect": "Allow",
		      "Action": [
			      "s3:GetObject",
			      "s3:PutObject".
			      "s3:DeleteObject"
			   ],
		      "Resource": ["arn:aws:s3:::test/*"],
		      "Condition": { 
		        "StringEquals": {
		          "AWS:SourceAccount": "{sourceAccountID}"
		        }
		      }
		    }
		  ]
	    }
		```

### IAM Roles vs Resource based policy
- When you assume a role (user, application or service), you give up your original permissions and take the permission assigned to the role
- When using a resource based policy, the principle doesn't have to give up his permissions. 
	- E.g. A user in Account A needs to scan a DynamoDB table in Account A and dump it in an S3 bucket in Account B
	- Supported by - S3 buckets, SNS topics, SQS

### [IAM Permission Boundaries](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_boundaries.html)
- Permissions boundaries are IAM restrictions that define the maximum allowed permissions for an IAM entity available within your AWS account
- It enables you to delegate work to your trusted employees to perform tasks on your behalf within a specific boundary of permissions 
![enter image description here](https://docs.aws.amazon.com/IAM/latest/UserGuide/images/EffectivePermissions-scp-boundary-id.png)
- Check policy evaluation logic [here](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_evaluation-logic.html)

### [Resource Access Manager](https://docs.aws.amazon.com/ram/latest/userguide/what-is.html)
- Helps you securely share your resources across AWS accounts, within your organization or organizational units (OUs) in AWS Organizations, and with IAM roles and IAM users for supported resource types
- You can use RAM to share transit gateways, subnets, AWS License Manager license configurations, Amazon Route 53 Resolver rules, and more [resource types](https://docs.aws.amazon.com/ram/latest/userguide/shareable.html)
![enter image description here](https://raw.githubusercontent.com/nikxsh/aws/master/diagrams/Resource-access-manager.png) 

###  Tools
 - Credentials Report (Account Level) that lists all your account's users and status of various credentials
 - Access Advisor (User level) shows the service permissions granted to the user and when those accessed
 
###  Best practices
 - Do not use root account
 - One AWS user = One Physical user
 - Assign users to groups and assign permission to groups
 - Create strong password policy
 - Use and enforce MFA
 - Create Roles for giving special permissions
 - Use Access keys for programmatic access (CLI/SDK)
 - Audit permissions of your account using Credentials Report
 - Never share IAM users and Access keys

### IAM Q&A
Q: You are getting started with AWS and your manager wants things to remain simple yet secure. He wants the management of engineers to be easy, and not re-invent the wheel every time someone joins your company. What will you do?
> I'll create multiple IAM users and groups, and assign policies to groups. New users will be 
  added to groups

Q: What is a proper definition of IAM Roles?
> An IAM entity that defines a set of permissions for making AWS service requests, that will be used by AWS services (Some AWS service will need to perform actions on your behalf. To do so, you assign permissions to AWS services with IAM Roles.)

Q: Which of the following is an IAM Security Tool?
> IAM Credentials Report (IAM Credentials report lists all your account's users and the status of their various credentials. The other IAM Security Tool is IAM Access Advisor. It shows the service permissions granted to a user and when those services were last accessed.)

Q: Which answer is INCORRECT regarding IAM Users?
> IAM Users access AWS with the root account credentials (IAM Users access AWS using a username and a password.)

Q: Which of the following is an IAM best practice?
> Do not use root account (You only want to use the root account to create your first IAM user, and for a few account and service management tasks. For every day and administration tasks, use an IAM user with permissions.)

Q: What are IAM Policies?
> JSON documents to define Users, Groups or Roles' permissions (An IAM policy is an entity that, when attached to an identity or resource, defines their permissions.)

Q: Which principle should you apply regarding IAM Permissions?
> Grant least privilege (Don't give more permissions than the user needs.) 

Q: What should you do to increase your root account security?
> Enable MFA (You want to enable MFA in order to add a layer of security, so even if your password is stolen, lost or hacked your account is not compromised.)

Q: You have a mobile application and would like to give your users access to their own personal space in the S3 bucket. How do you achieve that?
> Use Amazon Cognito Identity Federation (used to federate mobile user accounts and provide them with their own IAM permissions, so they can be able to access their own personal space in the S3 bucket)

Q: You have an on-premises identity provider that does not support SAML 2.0, and you want to give your on-premises users access to your resources in the AWS Accounts that you manage. What should you do?
> Use a Custom Identity Broker to authenticate your users and get temporary credentials from AWS STS using `AssumeRole` or `GetFederationToken` STS API calls

Q: You have strong regulatory requirements to only allow fully internally audited AWS services in production. You still want to allow your teams to experiment in a development environment while services are being audited. How can you best set this up?
> Create an AWS Organization and create two Prod and Dev OUs, then Apply an SCP on the Prod OU

Q: You have an on-premises Microsoft Active Directory setup and you would like to provide access for your on-premises AD users to the multiple AWS accounts you have. The solution should be scalable for adding accounts in the future. What do you recommend?
> Setup AWS Single Sign-On

Q: You are managing the AWS account for your company, and you want to give one of the developers access to read files from an S3 bucket. You have updated the bucket policy to this, but he still can't access the files in the bucket. What is the problem?
```json
{
    "Version": "2012-10-17",
    "Statement": [
	 {
        "Sid": "AllowsRead",
        "Effect": "Allow",
        "Principal": {
            "AWS": "arn:aws:iam::123456789012:user/Dave"
        },
        "Action": "s3:GetObject",
        "Resource": "arn:aws:s3:::static-files-bucket-xxx"
     }
   ]
}
```
> You should change the resource to `arn:aws:s3:::static-files-bucket-xxx/*` , because this is an object-level permission

Q: Which AWS Directory Service allows you to proxy requests to your on-premises Microsoft Active Directory?
> AD Connector

Q: Which AWS service allows you to share AWS resources in your AWS Account with other AWS Accounts?
> Resource Access Manager (helps you securely share your AWS resources within your organization or organizational units (OUs) in AWS Organizations and with AWS Accounts. You can also share resources with IAM Roles and IAM Users)

Q: You have 5 AWS Accounts that you manage using AWS Organizations. You want to restrict access to certain AWS services in each account. How should you do that?
> Using AWS Organizations SCP

## Monitoring & Audit
### [CloudWatch](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/cloudwatch_architecture.html)
- Amazon CloudWatch is basically a metrics repository
- An AWS service—such as Amazon EC2—puts metrics into the repository, and you retrieve statistics based on those metrics
- If you put your own custom metrics into the repository, you can retrieve statistics on these metrics as well![enter image description here](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/images/CW-Overview.png)
#### Metrics
- [Concepts](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/cloudwatch_concepts.html)
	- *Metric* is a variable to monitor e.g. CPUUtilization, NetworkLoad etc.
	- Metric belongs to *namespaces*
	- A *namespace* is a container for CloudWatch *metrics*
	- *Dimension* is an attribute of a *metric* (instance id, env etc.)
	- A *dimension* is a name/value pair that is part of the identity of a metric
	- You can assign up to 10 *dimensions* to a *metric*
	- Up to 10 *dimensions* per *metric*
	- Metrics have *timestamps*, each metric data point must be associated with a time stamp
- Metrics retention
	- Data points with a period of less than 60 seconds are available for 3 hours. These data points are high-resolution custom metrics
	- Data points with a period of 60 seconds (1 minute) are available for 15 days
	- Data points with a period of 300 seconds (5 minute) are available for 63 days
	- Data points with a period of 3600 seconds (1 hour) are available for 455 days (15 months)
- Can create CloudWatch dashboards of metrics
- EC2 Detailed monitoring
	- EC2 instance metrics have metrics "every 5 mins"
	- With detailed monitoring (for a cost), you get data "every t mins"
	- Use it if you want to scale faster for your ASG
	- Free AWS tier allows us to have 10 detailed monitoring metrics
- Custom
	- Possibility to define and send your own metrics e.g. memory usage, disk space etc.
	-  Use API call *PutMetricData*
	- Ability to use dimensions to segmented metrics
	- Metric resolution (StorageResolution API parameters)
		- Standard 60 secs
		- HR 1/5/10/30 sec (Higher cost)
		- Accepts metric data points 2 weeks in the past and 2 hours in the future
- Dashboards
	- Can setup custom dashboards for quick access to metrics and alarms
	- Global, can includes graphs from different AWS account and regions
	- You can change time zone and range
	- Can setup auto refresh (10s, 1m, 2m, 5m, 15m)
	- Can be shared with people who don't have an AWS account (public, email address, 3rd party SSO provider through Cognito)
	- 3 dashboards (up to 50 metrics)  are free
	- $3 per dashboard per month afterwards
#### CloudWatch logs
- Monitor, store, and access your log files from EC2 instances, CloudTrail, Route 53, and other sources
- Centralize the logs from all of your systems, applications, and AWS services that you use
- **Features**
	- Query your log data
	- Monitor logs from Amazon EC2 instances
	- Monitor AWS CloudTrail logged events
	- Log retention
	- Archive log data
	- Log Route 53 DNS queries
- [Concepts](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/CloudWatchLogsConcepts.html)
	- A *log event* is a record of some activity recorded by the application or resource being monitored
	- *Log groups* define groups of log streams, usually represents application name
	- A *log stream* is a sequence of log events that share the same source, basically instances within application, log files or containers
	- You can use *metric filters* to extract metric observations from ingested events and transform them to data points in a CloudWatch metric
- Can define log expiration policies (never expires, 30 days etc.)
- Can be send logs to
	- S3 (export)
		- Can take up to 12 hours to become available for exports
		- API call is *CreateExportTask*
		- Not near real time, use *log subscription* instead
	- Kinesis Data Streams
	- Kinesis Data Firehose
	- Lambda
	- ElasticSearch
- Logs subscription
	- Real-time feed of log events from CloudWatch Logs
	- Deliverers to other services such as an Amazon Kinesis stream, an Amazon Kinesis Data Firehose stream, or AWS Lambda for custom processing, analysis, or loading to other systems	![enter image description here](https://miro.medium.com/max/1838/1*RaSZJd2av4wo2monG6nyZg.png)
	- You can use a subscription filter with *Kinesis, Lambda, or Kinesis Data Firehose*
	- Logs that are sent to a receiving service through a *subscription filter* are *Base64* encoded and compressed with the *gzip* format
- **Sources** 
	- SDK, CloudWatch Logs Agents, CloudWatch Unified Agent
	- Elastic Beanstalk - collection of logs from application
	- ECS - collection from containers
	- AWS Lambda - collection from function logs
	- VPC flow logs
	- API Gateway
	- CloudTrail based on filter
	- Route53 - log DNS queries
- *Metric Filter* can be used to trigger CloudWatch alarms
- CloudWatch *Logs insights* can be used to query logs and add queries to dashboards
#### **Agent**
- By default, no logs from your EC2 machine will go to CloudWatch
- You need to run a *agent* on EC2 to push the log files you want
- Make sure IAM permission given to *agent* to push logs to CloudWatch
- Can be setup on-premise too
- *Logs Agent*
	- Old version of the agent
	- Can only sends to *CloudWatch Logs*
- *Unified Agent*
	- Collect additional system level metrics such as RAM, processed etc. 
	- Collect logs to sends to *CloudWatch Logs*
	- Centralized configuration using SSM parameter store
	- Metrics
		- Collected directly on your Linux sever / ec2 instance
		- CPU status
		- RAM
		- Disk metrics
		- Netstat
		- Processes
		- Swap space
#### Alarms
- Used to trigger notification for any metrics![enter image description here](https://dmhnzl5mp9mj6.cloudfront.net/security_awsblog/images/notifications_illustration_p.png)
- Has various options i.e. sampling, %, max, min, etc.
- *States*
	- OK
	- INSUFFCIENT_DATA
	- ALARM
- *Period*
	- Length of time in secs to evaluate the metric
	- High resolution custom metrics - 10 sec, 30 sec or multiple of 60 sec
- *Targets*
	- EC2 - Stop, Terminates, reboot or recover an EC2 instance
	- Auto Scaling - Triggering Auto scaling action i.e. scale in / scale out
	- SNS - Send notification to SNS
- EC2 instance recovery
	- Status check - check EC2 VM
	- System check - check underlaying hardware
	- Created alarm will monitor EC2 instance which will look for metric (e.g. *StatusCheckFailed_System*) and if this alarm get triggered then EC2 instance recovery will be started (with same private, public, elastic IP, metadata and placement group)
	- Send alert to SNS topic about recovery
#### CloudWatch  Events
- [CloudWatch  Events](https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/WhatIsCloudWatchEvents.html) delivers a near real-time stream of system events that describe changes in AWS resources
- Using simple rules that you can quickly set up, you can match events and route them to one or more target functions or streams
- Becomes aware of operational changes as they occur and takes corrective action as necessary, by sending messages to respond to the environment, activating functions, making changes, and capturing state information
- Scheduled or Cron (e.g. create an event every 4 hours)
- JSON payload is created from the event and passed to a *target*
### [EventBridge](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-what-is.html)
- Next evolution of CloudWatch Events. builds upon and extends CloudWatch Events
- It uses the same service API and endpoint, and same underlaying service infrastructure
- Is a serverless event bus service that you can use to connect your applications with data from a variety of sources
- EventBridge receives an _[event](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-events.html)_, an indicator of a change in environment, and applies a [rule](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-rules.html) to route the event to a [target](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-targets.html)
- Rules match events to targets based on either the structure of the event, called an _[event pattern](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-event-patterns.html)_, or on a schedule. For example, when an Amazon EC2 instance changes from pending to running, you can have a rule that sends the event to a Lambda function
- All events that come to EventBridge are associated with an [event bus](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-event-bus.html). Rules are tied to a single event bus, so they can only be applied to events on that event bus
- Your account has a default event bus which receives events from AWS services, and you can create custom event buses to send or receive events from a different account or Region
- EventBridge _[API destinations](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-api-destinations.html)_ are HTTP endpoints that you can set as the target of a rule. By using API destinations, you can use REST API calls to route events between AWS services, integrated SaaS applications, and your applications outside of AWS
- You can _[archive](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-archive-event.html)_, or save, events and then [replay](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-replay-archived-event.html) them at a later time from the archive
- Supports [identity-based](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-use-identity-based.html) and [resource-based](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-use-resource-based.html) policies to control access to EventBridge
- Types of bus
	- _Default event bus_ - Generated by AWS services (CloudWatch Events)
	- _Partner event bus_ - Receive events from SaaS service or applications (Auth0, Zendesk etc.)
	- _custom event bus_ - For your own applications
- Event buses can be accessed by other AWS accounts
- _Schema Registry_
	-  A schema defines the structure of _event_ that are sent to EventBridge
	- Schema Registry allows you to generate code for your application that will know in advance how data is structured in the event bus
	- Can be versioned
- Over the time, *CloudWatch Events* name will be replaced with *EventBridge* (same thing but added capabilities)
### [CloudTrail](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-user-guide.html)
- Provide governance, compliance and audit for your AWS account
- Enabled by default
- Gets an history of events or API calls made within your account by Console, SDK, CLI and services
- Can put logs from CloudTrail into CloudWatch Logs or S3
- A trail can be applied to all regions or single region
- If resource is deleted in AWS, investigate CloudTrail![enter image description here](https://miro.medium.com/max/1400/1*ejnlSrZ4eT1_BZPzT0WycA.png)
- _Events_
	-  Management events
	   - Operations that are performed on resources
	   - E.g. Configuring security (*IAM AttachRolePolicy*)
	   - By default, trails are configured to log management events
	   - Can separate *Read Events* from *Write Events*
	-  Data Events 
		- By default, data events are not logged due to high data volume
		- e.g. S3 operations
	- *Insights events*
		-  Detects unusual activities like inaccurate resource provisioning, hitting service limits and bursts of AWS IAM actions
		- Analyzes normal management events to create baseline and then continuously analyze write events to detect unusual patterns
- *Retention*
	- Stored for 90 days
	- To keep beyond that period, log them to S3 and use Athena

### [AWS Config](https://docs.aws.amazon.com/config/latest/developerguide/WhatIsConfig.html)
- Helps with recording compliance and audit in your AWS account
- Helps record configuration and changes over time like
	- Is there unrestricted SSH access to my SG
	- Do my buckets have any public access
	- How has my ALB configuration changed over time?
- You can receive alerts (SNS notification) for any changes
- AWS config is per region service
- Can be aggregated across region and accounts
- **Config Rules**
	- Can make custom config rules (must be defined in Lambda)
		- E.g. evaluate if each EBS disk is of type gp2
	- Rules can be evaluated / triggered
		- For each config change
		- And / Or at regular time intervals
	- **It does not prevent actions from happening**
	- We can automate remediation of non-compliant resource using SSM Automation Documents
	- You can set Remediation Retries if the resource is still non-compliant  after Auto remediation 
	- Use *EventBridge* to trigger notification when resource is non-compliant, has abilities to send configuration changes and compliance state notifications to SNS
- **Config Resources**
	- View compliance of a resource over time
	- View configuration of a resource over time
	- View CloudTrail API calls of a resource over time
- No free tier, $0.003 per configuration item recorded per region and $0.001 per config rule evaluation per region
### Case Study
- For ELB
	- CloudWatch
		- Monitors incoming connection metrics
		- Visualize errors codes as a % over time
		- Makes a dashboard to get an idea of your load balancer performance
	- CloudTrail
		- Track who made any changes to the load balancer with API calls
	- Config
		- Track security group rules for the Load Balancer
		- Track configuration changes for the Load Balancer
		- Ensure an SSL certificate always assigned to the Load Balancer (Compliance)
### Q&A
Q: You have a couple of EC2 instances in which you would like their Standard CloudWatch Metrics to be collected every 1 minute. What should you do??
> Enable detailed monitoring (This is a paid offering and is disabled by default. When enabled, the EC2 instance's metrics are available in 1-minute periods)

Q: High-Resolution Custom Metrics can have a minimum resolution of
> 1 Second

Q: You have an RDS DB instance that's configured to push its database logs to CloudWatch. You want to create a CloudWatch alarm if there's an `**Error**` found in the logs. How would you do that?
> Create a CloudWatch Logs Metric Filter that filter the logs for the keyword, then create a CloudWatch Alarm based on that Metric Filter

Q: You have an application hosted on a fleet of EC2 instances managed by an Auto Scaling Group that you configured its minimum capacity to 2. Also, you have created a CloudWatch Alarm that is configured to scale in your ASG when CPU Utilization is below 60%. Currently, your application runs on 2 EC2 instances and has low traffic and the CloudWatch Alarm is in the **ALARM** state. What will happen?
> The CloudWatch Alarm will remain in ALARM state but never decrease the number of EC2 instances in the ASG (The number of EC2 instances in an ASG can not go below the minimum capacity, even if the CloudWatch alarm would in theory trigger an EC2 instance termination)

Q: How would you monitor your EC2 instance memory usage in CloudWatch?
> Use the Unified CloudWatch Agent to push memory usage as a custom metric to CloudWatch

Q: A CloudWatch Alarm set on a High-Resolution Custom Metric can be triggered as often as
> 10 Seconds (If you set an alarm on a high-resolution metric, you can specify a high-resolution alarm with a period of 10 seconds or 30 seconds, or you can set a regular alarm with a period of any multiple of 60 seconds)

Q: You have made a configuration change and would like to evaluate the impact of it on the performance of your application. Which AWS service should you use?
> Amazon CloudWatch (is a monitoring service that allows you to monitor your applications, respond to system-wide performance changes, optimize resource utilization, and get a unified view of operational health. It is used to monitor your applications' performance and metrics)

Q: Someone has terminated an EC2 instance in your AWS account last week, which was hosting a critical database that contains sensitive data. Which AWS service helps you find who did that and when?
> AWS CloudTrail (allows you to log, continuously monitor, and retain account activity related to actions across your AWS infrastructure. It provides the event history of your AWS account activity, audit API calls made through the AWS Management Console, AWS SDKs, AWS CLI. So, the EC2 instance termination API call will appear here. You can use CloudTrail to detect unusual activity in your AWS accounts)

Q: You have CloudTrail enabled for your AWS Account in all AWS Regions. What should you use to detect unusual activity in your AWS Account?
> CloudTrail Insights

Q: One of your teammates terminated an EC2 instance 4 months ago which has critical data. You don't know who made this so you are going to review all API calls within this period using CloudTrail. You already have CloudTrail set up and configured to send logs to the S3 bucket. What should you do to find out who made this?
> Analyze CloudTrail logs in S3 bucket using Amazon Athena

Q: You are running a website on a fleet of EC2 instances with OS that has a known vulnerability on port 84. You want to continuously monitor your EC2 instances if they have port 84 exposed. How should you do this?
> Setup Config Rules

Q: You would like to evaluate the compliance of your resource's configurations over time. Which AWS service will you choose?
> AWS Config

Q: Someone changed the configuration of a resource and made it non-compliant. Which AWS service can you use to find out who made the change?
> CloudTrail 

Q: You have enabled AWS Config to monitor Security Groups if there's unrestricted SSH access to any of your EC2 instances. Which AWS Config feature can you use to automatically re-configure your Security Groups to their correct state?
> AWS Config Remediation

Q: You are running a critical website on a set of EC2 instances with a tightened Security Group that has restricted SSH access. You have enabled AWS Config in your AWS Region and you want to be notified via email when someone modified your EC2 instances' Security Group. Which AWS Config feature helps you do this?
> AWS Config Notifications

## [EC2 (Elastic Compute Cloud)](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/concepts.html)
- Provides scalable computing capacity in the Amazon Web Services (AWS) Cloud
- Infrastructure as a Service (IaaS)
- Main capabilities are
	- Renting Virtual Machines (EC2)
	- Storing data on virtual drives (EBS)
	- Distributing load across machines (ELB)
	- Scaling the service using an auto-scaling group (ASG)
- You can use Amazon EC2 to launch as many or as few virtual servers as you need, configure security and networking, and    manage storage
- EC2 enables you to scale up or down to handle changes in requirements or spikes in popularity, reducing your need to    forecast traffic
### Features
 - Virtual computing environments, known as  _instances_
 - Preconfigured templates for your instances, known as _Amazon Machine Images (AMIs)_,
 - Secure login information for your instances using _key pairs_
 - Storage volumes for temporary data that's deleted when you stop, hibernate, or terminate your instance, known as	    _instance store volumes_
 - Persistent storage volumes like Elastic Block Store (EBS)
 - A firewall
 - Static IPv4 addresses for dynamic cloud computing, known as  _Elastic IP addresses_
 - Virtual networks you can create that are logically isolated from the rest of the AWS Cloud
###  [Instances](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/Instances.html)
- An instance is a virtual server in the cloud
- Its configuration at launch is a copy of the AMI that you specified when you launched the instance
- Select an instance type based on the amount of memory and computing power that you need for the application or software that you plan to run on the instance
- [Instance types](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-types.html)
	- Instance types comprise varying combinations of CPU, memory, storage, and networking capacity and give you the flexibility to choose the appropriate mix of resources for your applications. 
	- Each instance type includes one or more instance sizes, allowing you to scale your resources to the requirements of your target workload.
	- Overview
		- `m5.2xlarge`
		- m:instance class
		- 5: generation
		- 2xlarge: size within the instance class
		- Naming
			- R: applications that needs a lot of RAM (In-memory cache)
			- C: applications that needs good CPU (compute/db)
			- M: applications that are balanced (web-app)
			- I: applications that needs good local I/O (instance storage - db)
			- G: applications that needs a GPU (video rendering/ machine learning)			 
	- Types
		- [Instance Comparison](https://instances.vantage.sh/)
		- [General Purpose](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/general-purpose-instances.html)
			- Diversity of workloads such as web-servers or code repos
			- Balance between Compute, Memory and Networking
			- Use cases
				- M5 and M5a - balance of compute, memory, and networking resources
					- Small and midsize databases
					- Data processing tasks that require additional memory
					- Caching fleets
					- Backend servers for enterprise applications
					-  E.g. `m5.large, m5a.16xlarge`
				- M5zn - extremely high single-thread performance, high throughput, and low latency networking
					- Gaming
					- High performance computing
					-  E.g. `m5zn.large`
				- M6g and M6gd - balanced compute, memory, and networking
					- Application servers
					- Microservices
					- Gaming servers
					- Midsize data stores
					- E.g. `m6gd.medium`
			- [Burstable performance instances](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/burstable-performance-instances.html)
				- The T instance family provides a baseline CPU performance with the ability to burst above the baseline at any time for as long as required
				- The EC2 burstable instances consist of T4g, T3a and T3 instance types, and the previous generation T2 instance types
				- T instances are not supported on a Dedicated Host
				- Unlimited mode
					- Can sustain high CPU utilization for any period of time whenever required
					- The hourly instance price automatically covers all CPU usage spikes if the average CPU utilization of the instance is at or below the baseline over a rolling 24-hour period or the instance lifetime, whichever is shorter
					- T4g, T3a and T3 instances launch as `unlimited` by default
				- Standard mode
					- Suited to workloads with an average CPU utilization that is consistently below the baseline CPU utilization of the instance
					- To burst above the baseline, the instance spends credits that it has accrued in its CPU credit balance
					- If the instance is running low on accrued credits, CPU utilization is gradually lowered to the baseline level, so that the instance does not experience a sharp performance drop-off when its accrued CPU credit balance is depleted
				- Use cases
					- Websites and web applications
					- Code repositories
					- Development, build, test, and staging environments
					- Microservices
				- E.g. `t4g.nano, t2.medium, t3.2xlarge`
		- [Compute optimized](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/compute-optimized-instances.html)
			- Optimized for compute-intensive tasks that require high performance
			- Use cases
				- Batch processing workloads
				- Media transcoding
				- High-performance web servers
				- High-performance computing (HPC)
				- Scientific modeling
				- Dedicated gaming servers and ad serving engines
				- Machine learning inference and other compute-intensive applications
			- E.g. `C5 and C5d instances (c5.large, c5.metal, c5d.24xlarge, c5d.metal)`
		- [Memory optimized](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/memory-optimized-instances.html)
			- Optimized for fast performance for workloads with large data set in memory
			- Use cases
				- High-performance, relational (MySQL) and NoSQL (MongoDB, Cassandra) databases
				- Distributed web scale cache stores (Memcached and Redis)
				- In-memory, optimized data storage formats and analytics for business intelligence (for example, SAP HANA)
				- Real-time processing of big unstructured data (financial services, Hadoop/Spark clusters)
				- High-performance computing (HPC) and Electronic Design Automation (EDA) applications.
			- E.g. `R5, R5a, R5b, and R5n instances(r5.large, r5a.24xlarge, r5b.2xlarge, r5n.metal)`
		- [Storage optimized](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/storage-optimized-instances.html)
			- Optimized for storage-intensive tasks that require high, sequential reads and write access to large data set on local storage
			- Use cases
				- High frequency online transaction processing systems
				- Relational & NoSQL databases
				- Cache for in-memory
				- Data warehouse applications
				- Distributed files systems
			- E.g. `D2 instances (d2.xlarge, d2.8xlarge)`
-  [Instance purchasing options](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-purchasing-options.html)
	- On-demand
		- You pay for compute capacity by the second with no long-term commitments
			- Linux - billing per second, after first minute
			- All other OS billing per hour
		- You pay for only when its running
		- Recommended for short-term and un-interrupted workloads
		- Highest costs but no upfront payment
	- Reserved instances 
		- Minimum 1 or 3 year 
		- Up to 72-75% discount compare to on-demand but upfront payment
		- Long workloads
		- Recommended for steady state usage application like sql-server
		- Reserved Instances provide you with significant savings on your Amazon EC2 costs compared to On-Demand Instance pricing
		- Convertible Reserved
			- Enables you to _exchange_ one or more Convertible Reserved Instances for another Convertible Reserved Instance with a different configuration, including instance family, operating system, and tenancy
			- Long workloads with flexible instances i.e. can change instance type
			- Upto 54% discount
			- There are no limits to how many times you perform an exchange
			- Cannot be sold in the Reserved Instance Marketplace
		- Scheduled reserved
			- Launch within time window you reserve
			- When you required a fraction of day/week/month
			- Commitment over 1-3 years
			- E.g. every Thursday between 3 to 6 pm
	- Spot instances
		- Enable you to request unused EC2 instances at steep discounts 
		- 90% discount compare to on-demand (most cost efficient)
		- Define max spot price and get instance while current spont price < max
		- Spot Instances are a cost-effective choice if you can be flexible about when your applications run and if your applications can be interrupted. For example, Spot Instances are well-suited for data analysis, batch jobs, background processing, and optional tasks
		- Not great for critical jobs or databases
		- Spot Instance request defines Maximum price per hour, desired number of instances, launch specification, request type  (one time/persistent), valid from to until 
		- _Instance that you own can "lose" at any point of time if your max price is less than the current spot price_
		- [Spot block](https://aws.amazon.com/blogs/aws/new-ec2-spot-blocks-for-defined-duration-workloads/)
			- Block spot instance during specified frame (1-6 hours) without interruptions  (In rare situation instance may get reclaimed) 
			- Pricing is based on the requested duration and the available capacity, and is typically 30% to 45% less than On-Demand, with an additional 5% off during non-peak hours for the region.
		- Spot fleets
			- Spot fleet simplifies bidding by allowing you to select the number of vCPUs you need, and automatically launching the lowest priced Spot instances to request and maintain your desired capacity.
			- A Spot Fleet is set of Spot Instances and optionally On-Demand Instances that is launched based on criteria that you specify
			- Automatically request spot instances with lowest price
			- Can have multiple launch pools so that the fleet can choose
			- Stops launching instances when reaching capacity or max
			- Extra saving
		- Good for workloads which are resilient to failures
		- Use cases
			- Batch jobs
			- Data analysis
			- Image processing
			- Any distributed workloads
			- workloads with flexible times
	- [Dedicated hosts](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/dedicated-hosts-overview.html)
		- Physical dedicated EC2 server
		- Full control of instance placement
		- Visibility into underlying sockets, cores
		- Allocated for 3 years
		- More Expensive
		- Useful for software with complicated licensing model (BYOL - Bring Your Own Licence) or companies with strong regulatory and compliance needs
		- no other customer will share your hardware
	- [Dedicated instances](https://aws.amazon.com/ec2/pricing/dedicated-instances/)
		- Run in a VPC on hardware that's dedicated to a single customer
		- Enable you to use your existing server-bound software licenses like Windows Server and address corporate compliance and regulatory requirement
		- May share hardware with other instances in a same account
		- No control over instance placement 
		- Per instance billing
- [Instance lifecycle](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-instance-lifecycle.html)
	- [EC2 Hibernate](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/Hibernate.html)
		- Enable hibernation while instance launch
		- When you stop instance the data on disk (EBS) keep intact in the next start
		- When you terminate EBS volume (root set-up to be destroyed)
		- When you start OS boots up and ec2 used data script is run then application starts and can take time
		- When you hibernate then 
			- In-memory (RAM) state is preserved i.e. RAM state is written to a file in the root EBS volume
			- The root EBS volume must be encrypted
			- Useful for long running processing or service that take time to initialize
			- Instance RAM must be less than 150 gb
			- Supported by Amazon linux 2, linux AMI Ubantu and windows
			- Can not be hibernated for more than 60 days	
###  [AMI (Amazon Machine Image)](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-instances-and-amis.html)
 - Template that contains a software configuration (for example, an operating system, an application server, and	    applications)
 - From an AMI, you launch an _instance_, which is a copy of the AMI running as a virtual server in the cloud
 	- An instance is a virtual server in the cloud
	- Your instances keep running until you stop, hibernate, or terminate them, or until they fail
	- AWS account has a limit on the number of instances that you can have running
	- The root device for your instance contains the image used to boot the instance. 
	- The root device is either an EBS volume or an instance store volume
- You can launch multiple instances of an AMI     
- Built for specific region only (and can be copied across regions)
- By default its private and locked for your account/region
- You can make them public OR sell them on the AMI marketplace
- Marketplace AMIs
	- AMI someone else made (selling purpose)
- Custom AMIs
	- Faster boot time
	- Pre-installed packages needed
	- Comes with configuration
	- Control maintenance
	- Control over network
	- Active directory integration
- Public AMIs
	- Can use already tested and optimized AMI for other people rent from other
	- Storage (live in S3)
	- Pricing - lives on S3 so charged for space in takes on S3 
	- To copy an AMI that was shared with you from another account, the owner of the source AMI must grant you read permission for the storage that backs the AMI, either the associated EBS snapshot (EBS backend EMI) or associated S3 bucket (instance store backed AMI)
	- You cant copy encrypted AMI that shared with you instead can copy underlaying snapshot and en ket shared with you and can register it as new AMIs
	- Can't copy AMI with an associated billing product code. To copy launch an EC2 instance from your account using shared AMI then create an AMI from instance
   	- Free tier Amazon linux 2, t2.micro
   	- Subnet: In what AZ you want instance
   	- Key-Pair (Pem) file ()
   	- Connect ec2 using SSH (Mac/Linux), Putty(Window), EC2 Connect
   	- Golden AMI
	   	-  Install your application, os dependencies etc. beforehand and launch your EC2 instance from it
   	- After you deregister an AMI, you can't use it to launch new instances, Existing instances launched from the AMI are not affected
###  [Networking](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-networking.html)
- Virtual private cloud (VPC) enables you to launch AWS resources, such as Amazon EC2 instances, into a virtual network dedicated to your AWS account,
- Private vs public IP (V4)
	- IPv6 is hexadecimal string, newer and solves problems for the IoT
	- IPv4 allows 3.7 B addresses in the public place
	- Public IP mean
		- Machine can be identified on internet(WWW)
		- Must be unique and can be geolocated
	- Private IP
		- Identified by private network
		- Unique across the private network
		- 2 different private network can have same IP
		- Machine connect to WWW using internet gateway or proxy
		- Specified ranges used only as private IP
	- Elastic IP
		- When you restart EC2 instance it can change its public IP so to have fixed public IP you need elastic IP (you own it until you delete)
		- You can attach to one instance at a time
		- You can mask failure of an instance by remapping the address to another instance but you can only have 5 Elastic IP per account (You can ask AWS to increase that limit)
		- Try avoid using EIP, instead use random public IP and register a DNS name to it or load balancer
- [Elastic network interfaces](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-eni.html)
	- Logical component in VPC that represent a virtual network card
	- Gives EC2 instance access to network
	- Each ENI has 
		- Private IPv4
		- One/more secondary IPv4/V6
		- One EIP per private IPv4
		- One public IPv4
		- One/more SG
		- A MAC address
	- Can create ENI independently attach them on the fly (move them) on EC2 instances for failover
	- Bound to specific AZ
- [Placement group](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/placement-groups.html)
	- You can use _placement groups_ to influence the placement of a group of _interdependent_ instances to meet the needs of your workload. 
	- Depending on the type of workload, you can create a placement group using one of the following placement strategies
	- Cluster 
		- Cluster instances into low latency group in a single AZ
		- Same rack (hardware) single AZ
		- Pros
			- Great network (10 Gbps)
		- Cons
			- If rack fails, all instances fails at the same time
		- Use cases 
			- Big data job to complete faster
			- App that need extremely low latency and high network throughput
	- Spread
		- Spread instances across underlying hardware (max 7 instances/AZ) for critical applications
		- Each EC2 instance in different AZ
		- Pros
			- Reduced risk of simultaneous failure as EC2 instance in different hardware
			- Can span across AZs
		- Cons
			- Limited to 7 instances
		- Use cases 
			- Application that needs to maximize high availability
			- Critical application where each instance must be isolated from failure from each other
	- Partition
		- Spread instances across many different partitions within AZ (max 7 partitions per AZ)
		- Scales to 100s of EC2 instances per group (Hadoop, cassandra, kafka)
		- Instance does not share racks with the instances in the other partition
		- Partition failure won't affect other partitions
		- For HDFS, HBase, cassandra, kafka			
###  [Infrastructure security](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/infrastructure-security.html)	
- As a managed service, Amazon EC2 is protected by the AWS global network security procedures
- **IAM management**
	- Use IAM to allow other users, services, and applications to use your Amazon EC2 resources without sharing your security credentials
	- By using IAM with Amazon EC2, you can control whether users in your organization can perform a task using specific Amazon EC2 API actions and whether they can use specific AWS resources
- **Key pairs**
	- A key pair, consisting of a public key and a private key, is a set of security credentials that you use to prove your identity when connecting to an EC2 instance
	- Amazon EC2 stores the public key on your instance
	- When your instance boots for the first time, the public key that you specified at launch is placed on your Linux instance in an entry within `~/.ssh/authorized_keys` 
	- When you connect to your Linux instance using SSH, to log in you must specify the private key that corresponds to the public key
	- The keys that Amazon EC2 uses are 2048-bit SSH-2 RSA keys. You can have up to 5,000 key pairs per Region
- **Security Groups**
	- Control inbound/outbound traffic using ports
	- Add rules using different types  (Eg SSH - TCP - 22 - Custom - 0.0.0.0/0 - SSH allowed 
	  from anywhere) it allows to connect using ssh
	- Firewall to EC2 instance
	- They regulate access to ports, authorised IP ranges (IPv4/V6), control inbound network
	  (other to the instance), outbound network(from instance to other)
	- Can be attached to multiple instances
	- Locked down to a region or VPC combination
	- Lives outside the ec2
	- Good to maintain separate group for SSH access
	- Timeout > probable SG issue
	- Connection refused > application error
	- By default all inbound traffic is blocked
	- By default all outbound traffic is authorised
	- 2 different EC2 can connect by authorising each other's SG in inbound rules		
###  [EC2 Storage](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/Storage.html)
- Amazon EC2 provides you with flexible, cost effective, and easy-to-use data storage options for your instances
- [Amazon Elastic Block Store](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AmazonEBS.html)
	- Is a network drive you can attach to your instance while they run
	- It allows your instance to persist data even after termination
	- Can only be mounted to one instance at a time
	- Bound to specific AZ
	- Think of them as a "network USB stick"
	- **EBS Volume**
		- Its a network drive (not physical one)
			- It uses the network to communicate the instance, means bit of latency
			- Can be detached from one EC2 instance and attached to another one quickly
		- Its locked to an AZ 
			- An EBS volume in us-east-1a a can't be attached to us-east-1b
			- To move a volume across, you first need to snapshot it
		- Has provisioned capacity (Size in GBs, & IOPS)
			- Billed for all the provisioned capacity
			- Can increase the capacity of the drive over time
		- Delete on Termination attribute
			- Controls the EBS behaviour when an instance terminates
				- By default, the root EBS volume is deleted (attribute enabled)
				- By default, any other attached EBS volume is not deleted (attribute enabled)
			- Use case is to preserve root volume when instance is terminated
		- EBS Snapshot
			- Backup of EBS volume at a point in time
			- Not need to detach volume to do snapshot
			- Can copy snapshots across AZ or region
		- EBS volumes are network drives with good but limited performance
			- If you need a high-performance hardware disk, use local EC2 instance store
			- Better I/O performance
			- Good throughput
			- But EC2 Instance Store lose their storage if they're stopped
		- **Volume Types**
			- EBS volumes are characterized in Size | Throughput | IOPS (i/o per sec)
			- General Purpose SSD
				- General purpose SSD volume
				- Balances price and performance  for wide variety of workloads
				- Cost effective storage with low latency
				- Used for System boot volumes, virtual desktops, dev and test environments
				- 1GiB to 16TiB
				- gp3
					- New generation
					- Baseline of 3000 IOPS and throughput of 125 MiB/s
					- Can increase IOPS up to 16000 and throughput up to 1000 MiB/s
				- gp2
					- Small volumes can burst IOPS to 3000
					- Size of the volumes and IOPS are linked, max IOPS is 16000
					- 3 IOPS per GB, means at 5334 GB we are at the max IOPS
			- Provisioned IOPS (PIOPS) SSD
				- Highest-performance SSD volume
				- For mission critical, low latency or high throughput workloads
				- Great database workloads
				- For application that need more than 16000 IOPS
				- io1/io2 
					- 4GiB -16 TiB
					- Max PIOPS 64000 for Nitro EC2 & 32000 for other
					- Can increase PIOPS independently from storage size
					- io2 have more durability and more IOPS per GiB (at same price as io1)
				- io2 block express
					- Sub-millisecond latency
					- Max PIOPS 256000 with an IOPS:GiB ratio 100:1
			- Hard Disk Drives (HDD)
				- Can not be a boot volume
				- 125 MiB to 16 TiB
				-  st1 (Throughput Optimized HDD)
					- Low cost HDD volume
					- Designed for frequently accessed, throughput intensive workloads
					- Max throughput 500 MiB/s - max IOPS 500
				- sc1 (Cold HDD)
					- Lowest cost HDD volume 
					- Designed for less frequently accessed workloads
					- Max throughput 250 MiB/s - max IOPS 250
			- Only gp2/gp3 and io1/io2 can be used as boot volumes
		- EBS Multi-Attach
			- io1/io2 family
			- Attach same EBS volume to multiple EC2 instances in the same AZ
			- Each instance has full read & write permissions to the volume
			- Use case
				- Achieve higher application availability in clustered Linux application
				- Application must manage concurrent write operation
			- Must use a file system that's cluster aware
		- EBS Encryption
			- When you create an encrypted EBS volume
				- Data at rest is encrypted inside the volume
				- All data in flight moving between the instance and the volume is encrypted
				- All snapshots are encrypted
				- All volumes created form the snapshot
			- Encryption and decryption are handled transparently
			- Has minimal impact on latency
			- Leverage keys form KMS
			- Copying an unencrypted snapshot allows encryption
				- Create an EBS snapshot from volume
				- Encrypt the EBS snapshot (using copy)
				- Create new EBS volume from snapshot (the volume will also be encrypted)
				- Now attach the encrypted volume to the original instance
		- [EBS RAID Options](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/raid-config.html)
			- Want to increase IOPS
			- Want to mirror your EBS volumes
			- Would mount volumes in parallel in RAID settings
			- RAID is possible as long as your OS supports it
			- RAID 0 (Increase performance)
				- Combining 2 or more volumes and letting total disk space and I/O
				- But if one disk fails, all the data is failed
				- Use case
					- Application that needs a lot of IOPS and doesn't needs fault-tolerance
					- A database that has replication already built-in
				- We can have very big disk with lot of IOPS
				- E.g. Two 5GiB EBS io1 volumes with 4000 provisioned IOPS will create 1000 GiB RAID 0 array with available bandwidth of 8000 IOPS and 1000 MiB/s of throughput
			- RAID 1 (Increase fault tolerance)
				- Mirroring a volume to another
				- If one disk fails, or logical volume is still working
				- Have to send the data to two EBS volume at the same time 
				- Use cases
					- Application that need increase volume fault tolerance
					- Application where you need to service disks
					- E.g. Two 500 GiB EBS io1 volumes with 4000 provisioned IOPS will create 500 GiB RAID 1 array with available bandwidth of 4000 IOPS and 500 MiB/s of throughput
- [Amazon Elastic Files System](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AmazonEFS.html)
	- Shared network file system
	- Managed NFS (Network file system) that can be mounted on many EC2
	- EFS works with EC2 instances in multi-AZ
	- Highly available, scalable, expensive (3 times gp2), pay per use
	- Use cases
		- Content management
		- Web serving
		- Data sharing
	- Uses NFSv4.1 protocol
	- Use SG to control access
	- Compatible with Linux based AMI
	- Encryption at rest using KMS
	- Scales automatically, no capacity planning
	- EFS Scale
		- 1000s of concurrent NFS clients, 10 GiB/s throughput
		- Grow to Petabyte-scale network file system, automatically
	- Performance mode
		- General purpose
			- Ideal for latency sensitive use case like Web serving environments or Content management Delivery
		- Max I/O
			- Scale to higher levels of aggregate throughput and operation per second
			- higher latency, throughput, highly parallel for big data, media processing
	- Throughput mode
		- Bursting 
			- Throughput scales with the file system size
			- 1TB=50MiB/s + burst of up to 100 MiB/s
		- Provisioned 
			- Throughput fixed at specified amount
			- Set your throughput regardless of storage size e.g. I GiB for 1TB storage
	- Storage Tiers
		- Lifecycle management feature
		- Move file after N days
		- Standard - for frequently accessed files
		- Infrequent access (EFS-IA) - cost to retrieve files, lower price to store
- [Instance Store](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/InstanceStorage.html)
	- Many instances can access storage from disks that are physically attached to the host computer
	- This disk storage is referred to as _instance store_
	- An _instance store_ provides temporary block-level storage for your instance
	- This storage consists of a preconfigured and pre-attached block of disk storage on the same physical server that hosts the EC2 instance for which the block provides storage.
	- The amount of the disk storage provided varies by EC2 instance type
	- The data on an instance store volume persists only during the life of the associated instance; if you stop, hibernate, or terminate an instance, any data on instance store volumes is lost
	- Maximum amount of I/O
	- Instance store is ideal for 
		- Temporary storage of information that changes frequently, such as buffers, caches, scratch data, and other temporary content
		- For data that is replicated across a fleet of instances, such as a load-balanced pool of web servers
- EFS vs EBS
	- EBS
		- Can be attached to only one instance at a time
		- Locked at the AZ level
		- gp2: IO increase if the disk size increase
		- io1 can increase IO independently
		- Has snapshot options that can be restored to another AZ
		- If root EBS volumes of instance get terminated by default if the EC2 instances gets terminated (you can disable this feature)
	- EFS
		- Can be mounted on 100s of instances across AZ
		- Shares website files
		- Only for Linux Instances
		- Hight price point than EBS
		- Can leverage EFS-IA for cost saving
- Advanced Concepts
	- EC2 Nitro
		- Underlying platform for next gen EC2 instances
		- New virtualization tech
		- Allows for better performance
			- Better networking options (enhanced networking, HPC, IPv6)
			- Higher Speed EBS (64000 EBS IOPS for Nitro, max 32000 for non-Nitro)
		- Better underlying security
		- Instance types using Nitro are A1, C5, G4, I3en, M5a etc. and Bare metal
	- vCPU
		- Multiple thread can run on one CPU
		- Each thread represent Virtual CPU
		- Example: `m5.2xlarge`
			- 4 CPU
			- 2 threads per CPU 
			- So 8 vCPUs
		- EC2 instance come with combination of RAM and vCPU
		- Some case you may want to changes vCPU options
			- Decrease CPU cores (Need high RAM and low number of CPU) to decrease licensing costs
			- Decrease threads per core (disable multithreading to have 1 thread per CPU) for high performace computing workloads
		- Only specified during instance launch
	- Capacity reservation
		- Ensure you have EC2 capacity when needed
		- Manual or planned end-date for the reservation
		- No need for 1-3 years commitment
		- Specify
			- The AZ in which to reserve the capacity
			- The number of instances for which to reserve the capacity
			- The instance attribute including instance type, tenancy etc.
		- Combined with Reserved Instances and Savings Plans to do cost savings
### Notes
- Billed by the second, t2.micro is free tier
- SSH on Linux/Mac, Putty on window		
- SSH on port 22
- SG can reference other SG instead of IP ranges
###  Dev
- Provide credentials to EC2 from IAM role only
- EC2 user data	
	- Bootstrap instance using user data script which run once at first start
	- Used to automate boot tasks such as installing updates/ software, download files etc.
	- Check if apache installed
	- Create machine > advance details > user data
			  #!/bin/bash
			  .
			  .
			  user commands
	- Review and Launch	
- SSH (Secure Shell - The SSH protocol uses encryption to secure the connection between a client and a server)  Port 22 over www
   	- connect to public ip and port 22, allowed by SG
   	- chmod 0400 pemfile.pem (protects it by making it read only and only for the owner,  
   		   protect a file against accidental overwriting)
   		   Note: For window, go to property of pem file > security > add yourself owner and 
   		   remove rest > apply and save
   	- ssh -i pemfile.pem ec2-user@publicIp
   	- whoami
   	- exit
- Putty
	- If SSH not supported
	- Import pem files to Putty key generator and save private key
	- open Putty program, add host name and port, link private key (SSH> Auth > PPK) and open instance		
- EC2 instance connect
	- From browser connect option select EC2 instance connect
	- Enter user name
	- Click on connect
	- Make sure to have port 22 open through inbound rules
- When you have stopped your EC2 instance and then started it again today. When you do so, 
	  the public IP of your EC2 instance will change	
- Launch Apache server
	   //Get public Ip
	   //login to EC@ instance
		yum update -y
		yum install httpd.x86_64
		systemctl start httpd.service
		systemctl enable httpd.service
		curl localhost:80
		//can not access IP through browser unless (HTTP - TCP - 80) in inboud rules
		echo "Hello World from $(host -f)" > /var/www/html/index.html
		//check browser	
- EC2 Instance Metadata
	- Allows EC2 instance to "learn about themselves" without using IAM Role for that purpose
	- The URL is http://169.254.169.254/latest/meta-data (Worked only from instances)
	- Can retrieve the IAM Role name from the metadata, can not retrieve IAM policy		
		- curl http://169.254.169.254
		- curl http://169.254.169.254/latest
			
			> meta-data
				user-data
				
	     - http://169.254.169.254/latest/meta-data
		     >  ... All metadata ...
		     
### EC2 Q&A  	
Q: You are launching an EC2 instance in us-east-1 using this Python script snippet: (we will see SDK in a later section, for now just look at the code reference ImageId) ec2.create_instances(ImageId='ami-b23a5e7', MinCount=1, MaxCount=1) It works well, so you decide to deploy your script in us-west-1 as well. There, the script does not work and fails with "ami not found" error. What's the problem?
> AMI is region locked and the same ID cannot be used across regions
	
Q: You would like to deploy a database technology and the vendor license bills you based on the  physical cores and underlying network socket visibility. Which EC2 launch modes allow you to get visibility into them?
> Dedicated Hosts
	
Q: You are launching an application on EC2 and the whole process of installing the application  takes about 30 minutes. You would like to minimize the total time for your instance to boot up and be operational to serve traffic. What do you recommend?
> Create an AMI after installing apps and launch from the AMI (Creating an AMI after installing the applications allows you to start more EC2 instances directly from that AMI, hence bypassing the need to install the application (as it's already installed))
	
Q: You are running a critical workload of three hours per week, on Monday. As a solutions architect, which EC2 Instance Launch Type should you choose to maximize the cost savings  while ensuring the application stability?
> Scheduled Reserved Instances
	
Q: You would like to make sure your EC2 instances have the highest performance while talking to  each other as you're performing big data analysis. Which placement group should you choose?
> Cluster (Cluster placement groups places your instances next to each other giving you high performance computing and networking)
	
Q: You plan on running an open-source MongoDB database year-round on EC2. Which instance launch mode should you choose?
> Reserved Instances (This will allow you to save cost as you know in advance that the instance will be a up for a full year)
	
Q: You built and published an AMI in the ap-southeast-2 region, and your colleague in us-east-1 region cannot see it
> An AMI created for a region can only seen in that region

Q: Which EC2 Purchasing Option can provide the biggest discount, but is not suitable for critical jobs or databases?
> Spot Instances (Spot Instances are good for short workloads, but are less reliable.)

Q: Which network security tool can you use to control traffic in and out of EC2 Instances?
> Security Groups (Security Groups operate at instance level and can control traffic.)	

Q: How long can you reserve an EC2 Reserved Instance?
> 1 year or 3 years terms are available for EC2 Reserved Instances

Q: A company would like to deploy a high-performance computing (HPC) application on EC2. Which EC2 instance type should it choose?
> Compute Optimized (Compute Optimized EC2 instances are great for compute-intensive workloads requiring high performance processors, such as batch processing, media transcoding, high performance web servers, high performance computing, scientific modelling & machine learning, and dedicated gaming servers)

Q: Which EC2 Purchasing Option should you use for an application you plan on running on a server continuously for 1 year?
> Reserved Instances (Reserved Instances are good for long workloads. You can reserve instances for 1 or 3 years)

Q: Your instance in us-east-1a just got terminated, and the attached EBS volume is now available. Your colleague tells you he can't seem to attach it to your instance in us-east-1b.
> EBS Volumes are AZ locked (EBS Volumes are created for a specific AZ. It is possible to migrate them between different AZ through backup and restore)

Q: You have provisioned an 8TB gp2 EBS volume and you are running out of IOPS. What is NOT a way to increase performance?
> Increase the EBS volume size (EBS IOPS peaks at 16,000 IOPS. or equivalent 5334 GB.)

Q: You would like to leverage EBS volumes in parallel to linearly increase performance, while accepting greater failure risks. Which RAID mode helps you in achieving that?
> RAID 0

Q: Although EBS is already a replicated solution, your company SysOps advised you to use a RAID mode that will mirror data and will allow your instance to not be affected if an EBS volume entirely fails. Which RAID mode did he recommend to you?
> RAID 1

Q: You would like to have the same data being accessible as an NFS drive across AZ on all your EC2 instances. What do you recommend?
> Mount EFS (EFS is a network file system (NFS) and allows to mount the same file system on EC2 instances that are in different AZ)

Q: You would like to have a high-performance cache for your application that mustn't be shared. You don't mind losing the cache upon termination of your instance. Which storage mechanism do you recommend as a Solution Architect?
> Instance Store (Instance Store provide the best disk performance)

Q: You are running a high-performance database that requires an IOPS of 210,000 for its underlying filesystem. What do you recommend?
> Instance Store (Is running a DB on EC2 instance store possible? It is possible to run a database on EC2. It is also possible to use instance store, but there are some considerations to have. The data will be lost if the instance is stopped, but it can be restarted without problems. One can also set up a replication mechanism on another EC2 instance with instance store to have a standby copy. One can also have back-up mechanisms. It's all up to how you want to set up your architecture to validate your requirements. In this case, it's around IOPS, and we build an architecture of replication and back up around it)

## Scalability and High Availability
### **Scalability**
- Means that application/system can handle greater loads by adapting
- Vertical 
  	- Mean increasing the size of instances (scale up/down)
  	- If your application runs on t2.micro then scaling vertically means running it on t2.large
  	- Very common for non distributed systems such as database
  	- RDS, ElastiCache are services that can scale vertically
  	- Has hardware limits
- Horizontal
  	- Increasing number of instances / systems for application (scale out/in)
  	- Implies distributed systems
  	- Very common for web and modern applications
  	- Easy to configure due to cloud offering such as EC2
  	- Achieved by Increasing number of instances (with the help of Auto Scaling Groups (ASG) and Load Balancer)	
### High Availability
- Goes hand in hand with Horizontal Scaling
- Running your application in at least 2 data centres
- Goal is to survive a data center loss
- Can be passive (for RDS Multi AZ) or can be active (for horizontal scaling)
- Achieved by running instances for same application across Multi-AZ (with the help of Auto Scaling Groups (ASG) and Load Balancer Multi-AZ)	
### [Load Balancer](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/service-load-balancing.html)
- Servers that forward internet traffic to multiple servers or backend EC2 instances
- Can expose single point of access (DNS)
- Seamlessly handle failure downstream instances
- Do regular checks on your instances
- Enforce stickiness with cookies
- High availability across zones
- Separates public traffic from private
- **ELB (Elastic load balancer)**
	- Managed Load balancer
	- Elastic Load Balancing automatically distributes your incoming traffic across multiple targets, such as EC2 instances, containers, and IP addresses, in one or more Availability Zones.
	- AWS takes cares of upgrades, maintenance, high availability
	- AWS provides only a few configuration knobs
	- Cost to setup but will be lot more effort on your end
	- Integrated with other AWS services
	- Health checks is done on a port and a route (/health is common)
 	- if the response if not 200 (OK), then instance is unhealthy
	- It can automatically scale to the vast majority of workloads
	- You can setup internal (private) and external(public) ELBs          
	- ![enter image description here](https://res.cloudinary.com/hy4kyit2a/f_auto,fl_lossy,q_70/learn/modules/aws-optimization/route-traffic-with-amazon-elastic-load-balancing/images/5e6d6d4ec8ab0c7bb984cf7624178d5a_b-7-eef-6-f-4-fdb-5-4-e-01-a-89-d-88-d-7257-bf-924.png =500x300)	  	
	- [Classic Load Balancer(CLB)](https://docs.aws.amazon.com/elasticloadbalancing/latest/classic/introduction.html)
		- Old generation, 2009, TCP (layer 4) HTTP & HTTPS (layer 7)
		- A Classic Load Balancer makes routing decisions at either the transport layer (TCP/SSL) or the application layer (HTTP/HTTPS)
		- Classic Load Balancers currently require a fixed relationship between the load balancer port and the container instance port. E.g. it is possible to map the load balancer port 80 to the container instance port 3030 and the load balancer port 4040 to the container instance port 4040. However, it is not possible to map the load balancer port 80 to port 3030 on one container instance and port 4040 on another container instance. 
		- This static mapping requires that your cluster has at least as many container instances as the desired count of a single service that uses a Classic Load Balancer
		- ![enter image description here](https://docs.aws.amazon.com/elasticloadbalancing/latest/classic/images/load_balancer.png)
		- Features
			- Health check are TCP or HTTP based
			- Fixed hostname xxx.region.elb.amazonaws.com
			- Support for EC2-Classic
			- Support for TCP and SSL listeners
			- Support for sticky sessions using application-generated cookies
	- [Application Load Balancer (ALB)](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/introduction.html)
		- New generation, HTTP & HTTPS (layer 7), support for HTTP/2 and WebSocket
		- Makes routing decisions at the application layer (HTTP/HTTPS), supports path-based routing, and can route requests to one or more ports on each container instance in your cluster
		- Support dynamic host port mapping. For example, if your task's container definition specifies port 80 for an NGINX container port, and port 0 for the host port, then the host port is dynamically chosen from the ephemeral port range of the container instance (such as 32768 to 61000 on the latest Amazon ECS-optimized AMI)
		- It monitors the health of its registered targets, and routes traffic only to the healthy targets
		- _Listener_
 		- Checks for connection requests from clients, using the protocol and port that you configure
 		- The rules that you define for a listener determine how the load balancer routes requests to its registered targets.
		- _Target group_
 		- Routes requests to one or more registered targets, such as EC2 instances, using the protocol and port number that you specify
 		- You can register a target with multiple target groups
 		- You can configure health checks on a per target group basis
			- Target groups are 
 			- EC2 instances (HTTP)
 			- ECS Tasks (HTTP)
 			- Lambda functions (HTTP request is translated into JSON event)
 			- IP address (must be private)
			- Can route to multiple target groups
			- Health check at Target group level![enter image description here](https://www.bogotobogo.com/DevOps/AWS/images/NLB-ASG/ALB-Diagram.png)![enter image description here](https://i.pinimg.com/originals/53/77/ee/5377ee17410f880ec70c9f4bf3463bc3.png)
		- Features 
			- Fixed hostname xxx.region.elb.amazonaws.com
			- Load balancing to multiple HTTP applications across machines
			- Load balancing to multiple applications on same machine (containers)
			- Support route routing, based on different target groups
 			- Path in the URL (E.g. /users (Target 1) & /posts (Target 2))
 			- Hostname in the URL (E.g. example.com (Target 1) & test.example.com (Target 2))
 			- Query string or headers in the URL (E.g. ?id=1234 (Target 1) & ?name=xyz (Target 2))
			- Great fit for Microservices and container based application (Docker & Amazon ECS)
			- Access logs contain additional information and are stored in compressed format
			- Has a port mapping feature to redirect to a dynamic port in ECS
			- Application server don't see the IP of client directly 
 			- The true IP of client is inserted in header X-Forwarded-For
 			- We can also get Port (X-Forwarded-Port) and proto (X-Forwarded-Proto)
		- In comparison, multiple CLB per application
	- [Network Load Balancer (NLB)](https://docs.aws.amazon.com/elasticloadbalancing/latest/network/introduction.html)
		- New generation, 2017 - TCP, TLS (Secure TCP) & UDP
		- Makes routing decisions at the transport layer (TCP/SSL)
		- After the it receives a connection, it selects a target from the target group for the default rule using a flow hash routing algorithm
		- It attempts to open a TCP connection to the selected target on the port specified in the listener configuration
		- It forwards the request without modifying the headers
		- A _listener_ checks for connection requests from clients, using the protocol and port that you configure, and forwards requests to a target group.
		- Each _target group_ routes requests to one or more registered targets, such as EC2 instances, using the TCP protocol and the port number that you specify
		- You can add and remove targets from your load balancer as your needs change, without disrupting the overall flow of requests to your application
		- It has **one static IP per AZ** and supports assigning Elastic IP (Helpful for IP whitelisting)![enter image description here](https://exampleloadbalancer.com/assets/with_nlbtls.png)
		- Features 
			- Support dynamic host port mapping
			- Used for extreme performance, TCP or UDP traffic
			- Handle millions of requests per second
			- Less latency ~100ms (vs 400ms for ALB)
			- Has one static IP per AZ and supports assigning EIP which helpful for  IP whitelisting
			- Not included in AWS free tier
			- Has own SG which communicate with EC2 SG
- [Gateway Load Balancer](https://docs.aws.amazon.com/elasticloadbalancing/latest/gateway/introduction.html)
	- Enable you to deploy, scale, and manage virtual appliances, such as firewalls, intrusion detection and prevention systems, and deep packet inspection systems
	- Operates at the third layer of the Open Systems Interconnection (OSI) model, the network layer
	- It listens for all IP packets across all ports and forwards traffic to the target group that's specified in the listener rule
	- It maintains stickiness of flows to a specific target appliance using 5-tuple (for TCP/UDP flows) or 3-tuple (for non-TCP/UDP flows)
	- Uses Gateway Load Balancer endpoints to securely exchange traffic across VPC boundaries
	- You deploy the Gateway Load Balancer in the same VPC as the virtual appliances
	- Traffic to and from a Gateway Load Balancer endpoint is configured using route tables
	- You must create the Gateway Load Balancer endpoint and the application servers in different subnets![enter image description here](https://miro.medium.com/max/1121/1*qNMaVza5BzgrimG2fz0gNg.png)
- **Load balancer stickiness**
	- Stickiness is that same client always get redirects to the same instance
	- Works for CLB and ALB
	- The cookie used for stickiness has an expiration date
	- To make sure user doesn't lose his session data
	- May bring imbalance to the load balancing over to the backend EC2 instances
	- Can enable at target group level
- **Cross Zone Load Balancer**
	- Each load balancer instance evenly distributes load across all registered instances in all AZ
	- Without each load balancer distributes requests evenly across the registered instances in its AZ only
		- CLB (disabled by default, no charges for inter AZ data if enabled)
		- ALB (Always on, no charges for inter AZ data)
		- NLB (disabled by default, pay charges for inter AZ data if enabled)
- **SSL (Secure Socked Layer) Certificate**
	- Allows traffic between clients and LB to be encrypted in transit
	- Issued by public authority (Symantec, GoDaddy etc.)
	- LB uses X.509 certificate
	- You can manage certificates using ACM
	- Client can use SNI (Server Name Indication) to specify the hostname they reach
	- SNI solved problem if loading multiple SSL certificates onto one web server
	- SNI works for ALB and NLB only
 	- CLB (support only one certificate, must use multiple CLB for multiple hostname with mu certificates)
	- ALB (support multiple listeners with multiple SSL certificates, uses SNI to make it work)
	- NLB (support multiple listeners with multiple SSL certificates, uses SNI to make it work)
- **Connection draining**
	- Time to complete "in-flight requests" while the instance is de-registering or unhealthy
	- Stop sending request to instance while its de-registering
	- Between 1 to 3600 secs, default is 300 secs
	- Can't be disabled (set value to 0)
	- Set to a low value if your requests are short
	- Connection draining is for CLB
	- Deregistration delay is for ALB and NLB
- [Auto scaling group](https://docs.aws.amazon.com/autoscaling/ec2/userguide/AutoScalingGroup.html)
	- Scale out (Add ec2 instance) to match increased load
	- Scale in (remove ec2 instance) to match decreased load
	- ![enter image description here](https://miro.medium.com/max/487/1*uS9J8btKCQaMOhnUXp62aA.jpeg)
	- Auto register new instance to a load balancer
	- ASG is Free
	- If having instance under ASG get terminated for whatever reason then ASG will automatically created new ones as a replacement
	- ASG can terminate instance marked as unhealthy by LB (and replace them)
	- A Launch configuration has
		- Instance type
		- EC2 User data
		- EBS volume
		- Security groups
		- SSH Key pair
		- Min/Max Size and initial capacity
		- Network + Subnet information 
		- Load balancer information  or Target Group information 
		- Scaling policies
	- Auto scaling alarms
		- Scale in/out based on CloudWatch alarms
		- Monitors metrics
		- Based on can created policies![enter image description here](https://miro.medium.com/max/1200/1*y5MJicq2QfakmlwXQhZ-ow.png)
- Scaling policies
	- Possible to define rules that are directly managed by EC2 e.g. Target Average CPU usage, number of requests per ELB
	- Target tracking scaling policy
	- Most simple and easy to set up
	- E.g. average ASG CPU to stay around 40%
	- Simple/Step scaling policy
		- When CloudWatch alarm triggered (e.g. CPU > 70%) then add 2 units
		- When CloudWatch alarm triggered (e.g. CPU < 30%) then remove 1 unit
	- Scheduled actions
		- Anticipate a scaling based on know usage patterns
		- E.g. increase min capacity to 10 at 5pm on Friday 
	- Scaling cooldowns
		- Helps to insure ASG doesn't launch or terminate instance before previous scaling  activity takes effect
		- One common use for scaling specific cooldowns is 
		- If the default cooldown period of 300 sec is too long you can reduce cost by applying scaling specific cooldown period of 180 secs to scale-in policy
		- If your application is scaling-up and down multiple times each hour, modify the ASG cool-down times and the CloudWatch Alarm Period that triggers scale-in
	- Default Termination policy
		- Find AZ which has most instances, if there are multiple then delete the one with oldest launch configuration
		- ASG tries the balance the number of imbalances across AZ by default
- [Lifecycle Hooks](https://docs.aws.amazon.com/autoscaling/ec2/userguide/AutoScalingGroupLifecycle.html)
	- Can perform extra steps (Extract info/ logging) before instance goes in service (Pending state) or before it get terminated (Terminating state)
	- ![enter image description here](https://docs.aws.amazon.com/autoscaling/ec2/userguide/images/auto_scaling_lifecycle.png)
	- ASG 
		- (Scale Out) > Pending [ Lifecycle hooks >> Pending:wait > Pending: Proceed] > InService 
		- (Scale In) > Terminating [ Lifecycle hooks >> Terminating:wait > Terminating: Proceed] > Terminated
		- Launch configuration: Legacy, must be created every time vs Launch Template: newer, can have multi-versions, parameter subsets and provision for both On-Demand and Spot instances

### Scalability & Availability Q&A
Q: Load Balancers provide a	
> Static DNS name we can used n our application

Q: You are running a website with a load balancer and 10 EC2 instances. Your users are complaining  about the fact that your website always asks them to re-authenticate when they switch pages. You  are puzzled, because it's working just fine on your machine and in the dev environment with 1  server. What could be the reason?
> The load balancer does not have stickiness enabled (stickiness ensures traffic sent to the same backend instance for a client to maintain session data)

Q: Your application is using an Application Load Balancer. It turns out your application only sees  traffic coming from private IP which are in fact your load balancer's. What should you do to  find the true IP of the clients connected to your website?
> look into the X-Forwarded-For header in the backend

Q: You quickly created an ELB and it turns out your users are complaining about the fact that  sometimes, the servers just don't work. You realise that indeed, your servers do crash from time  to time. How to protect your users from seeing these crashes?
> Enable health checks (ensures your ELB wont send traffic to unhealthy instances)

Q: You are designing a high performance application that will require millions of connections to be handled, as well as low latency. The best Load Balancer for this is
> Network Load balancer

Question 6: Application Load Balancers handle all these protocols except
> TCP (NLB support TCP)

Q: The application load balancer can route to different target groups based on all these
 except...
> Geography (correct ones are Hostname, request-path, Source IP)

Q: You are running at desired capacity of 3 and the maximum capacity of 3. You have alarms set at  60% CPU to scale out your application. Your application is now running at 80% capacity. What  will happen?
> Nothing (The capacity of your ASG cannot go over the maximum capacity you have allocated 
   during scale out events)

Q: I have an ASG and an ALB, and I setup my ASG to get health status of instances thanks to my ALB.  One instance has just been reported unhealthy. What will happen?
> The ASG will terminate the EC2 instance (Because the ASG has been configured to leverage the ALB
   health checks, unhealthy instances will be terminated)

Q: Your boss wants to scale your ASG based on the number of requests per minute your application makes  to your database.
> Create CloudWatch custom metrics and build alarm on this to scale your ASG (The metric "requests
   per minute" is not an AWS metric, hence it needs to be a custom metric)

Q: Scaling an instance from an r4.large to an r4.4xlarge is called
> Vertical Scalability

Q: Running an application on an auto scaling group that scales the number of instances in and out  is called
> Horizontal Scalability

Q: You would like to expose a fixed static IP to your end-users for compliance purposes, so they can  write firewall rules that will be stable and approved by regulators. Which Load Balancer should you use?
> Network Load Balancer (Network Load Balancers expose a public static IP, whereas an Application or
   Classic Load Balancer exposes a static DNS (URL))

Q: A web application hosted in EC2 is managed by an ASG. You are exposing this application through an Application Load Balancer. The ALB is deployed on the VPC with the following CIDR: 192.168.0.0/18.  How do you configure the EC2 instance security group to ensure only the ALB can access the port 80?
> Open up the EC2 Security on port 80 to the ALB's security group (This is the most secure way of ensuring only the ALB can access the EC2 instances. Referencing by security groups in rules is an extremely powerful rule and many questions at the exam rely on it. Make sure you fully master the concepts behind it!)

Q: Your application load balancer is hosting 3 target groups with hostnames being users.example.com,   api.external.example.com and checkout.example.com. You would like to expose HTTPS traffic for each of these hostnames. How do you configure your ALB SSL certificates to make this work?
> Use SNI (SNI (Server Name Indication) is a feature allowing you to expose multiple SSL certs if the client supports it)

Q: An ASG spawns across 2 availability zones. AZ-A has 3 EC2 instances and AZ-B has 4 EC2 instances. The ASG is about to go into a scale-in event. What will happen?
> AZ-B will terminate instance with the oldest configuration (Make sure you remember the Default 
   Termination Policy for ASG. It tries to balance across AZ first, and then delete based on the age
   of the launch configuration.)

Q: The Application Load Balancers target groups can be all of these EXCEPT..
> Network Load balancer (Correct ones are EC2 instance, IP, Lambda functions)

Q: You are running an application in 3 AZ, with an Auto Scaling Group and a Classic Load Balancer. It  seems that the traffic is not evenly distributed amongst all the backend EC2 instances, with some  AZ being overloaded. Which feature should help distribute the traffic across all the available EC2
   instances?
> Cross Zone load balancing

Q: Your Application Load Balancer (ALB) currently is routing to two target groups, each of them is  routed to based on hostname rules. You have been tasked with enabling HTTPS traffic for each hostname and have loaded the certificates onto the ALB. Which ALB feature will help it choose the
   right certificate for your clients?
> Use SNI (Server Name Indication)

Q: An application is deployed with an Application Load Balancer and an Auto Scaling Group. Currently,  the scaling of the Auto Scaling Group is done manually and you would like to define a scaling  policy that will ensure the average number of connections to your EC2 instances is averaging at around 1000. Which scaling policy should you use?
> Target Tracking

## AWS Storage Services
### Intro
- How to choose the right database based on your architecture
	- Ready Heavy, Write heavy or balanced workload? Thought put needs? Will it be change? Does it need to scale during the day?
	- How much data to store and how long? Will it grow? Average object size? How are hey accessed? 
	- Data durability? Source of truth for the data?
	- Latency requirements? Concurrent users?
	- Data Model? How will you query the data? Join? Structured? Semi-structured?
	- Strong schema? More flexibility? Reporting? Search? RBDMS? NoSQL?
	- License costs?
- Types
	- RDBMS (SQL/OLTP): RDS, Aurora - Great for joins, normalized data
	- NoSQL
		- DynamoDB (JSON) - 400kb per row
		- ElastiCache (Key-Value Pair) - High performance
		- Neptune (Graph) - No joins and SQL
	- Object Store
		- S3 - For big objects (up to 5TB)
		- Glaciers - For backups/archives
	- Data Warehouse (SQL Analytics/BI)
		- Redshift (OLAP)
		- Athena - Used to query S3
	- Search - ElasticSearch (Json - free text, unstructured searches)
	- Graphs - Neptune (Displays relationships between data)
### [Relative Database Service](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Welcome.html)
- Allows to create databases in cloud managed by AWS like Postgres, MySQL, MariaDB, Oracle, MSSQL and Aurora
- Must provision an EC2 instance & EBS volume type and size
- Supports for Read replicas and Multi AZ setup for DR
- Security through IAM, SG, KMS & SSL in transit
- Scaling capabilities (Vertical and horizontal)
- Backup/Snapshot/Point in time restore feature
- Managed and scheduled maintenance
- Monitoring through CloudWatch
- Can SSH into instances
- **Backup**
	- Automatic daily full
	- Transaction log backed every 5 mins
	- Restore to any point (oldest is 5 mins ago)
	- 7 days retentions
- **Auto Scaling**
	- increase storage dynamically
	- detect and scales automatically
	- you have to set Maximum Storage Threshold
	- Automatically modify storage if
 	- Free storage less than 10% of allocated storage
 	- Low storage last at least 5 mins
	- 6 hours have passed since last modification
	- Useful when unpredictable workloads
	- Supports all RDS databases
- **Read Replicas**
	- Helps to scales reads
	- Up to 5 RR
	- Within AZ, Cross AZ or Cross Region
	- Async, eventual consistent
	- Replicas can be promoted to own DB
	- Must update connection string to leverage RR
	- Use case > create new RDS RR with Async replication to divert additional reads so main application will be unaffected
	- RR used for Select Query only
	- If RR within same region, no charges else for cross region have to pay
- **Multi AZ for Disaster Recovery**
	- SYNC Replication
	- One DNS name - Automatic app failover
	- Increase availability
	- No manual intervention
	- Not used for scaling
	- Note: can setup read replicas as Multi AZ for Disaster Recovery
- **Single AZ to Multi AZ**
	- Zero downtime
	- Just modify setting
	- Snapshot taken from main DB > new DB restored from this snapshot in a 
		  new AZ > Synchronization established between two DBs
- **Security**
	- Can not SSH
	- *At rest* encryption
		- Possible to encrypt the master and read replicas
		- Has to be defined at launch time
		- If master not encrypted then RR can not be encrypted
	- *In-flight* encryption
		- SSL certificate to encrypt
		- To enforce
			- Postgres: rds_ssl = 1 in the parameter groups
			- MySQL: GRANT USAGE ON *.* TO 'mysqluser'@'%' REQUIRE SSL;
	- Backup will only encrypted if snapshot taken from encrypted DB but can copy a  un-encrypted snapshot as 
	   encrypted and create DB out of it
	- leverage SG (control which IP/SG can communicate with RDS), IAM Roles(work with MySQL and
	   Postgres using auth token obtained from IAM with 15 mins lifetime) and EC2 instance
- **Use Cases**
	- Store relational datasets (RDBMS/OLTP)
	- Perform SQL queries
	- Transactional inserts/update/delete is available
- **For Solution Architect**	  		  
	- *Operations* - Small downtime when failover happens such as maintenance, scaling in read replicas/ec2 instance, while restore ELB or application changes
	- *Security* - AWS responsible for OS security, we are responsible for setting up KMS, SG, IAM policies, authorizing users in DB
	- *Reliability* - Multi AZ feature, failover in case of failures
	- *Performance* - Depends on EC2 instance type, ELB volume type, ability to add Read Replicas. Storage auto-scaling & manual scaling of instances
	- *Cost* - Pay per hour based on provisioned EC2 and EBS
### [Aurora](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/CHAP_AuroraOverview.html)   
- Not open sourced
- Fully managed relational database engine with compatible API for MySQL and PostgreSQL.
- Cloud optimized (5x performance improvement over MySQL, over 3x in Postgres)
- Need to define EC2 instance type
- Automatically grows in increment of 10GB, up to 64TB
- Can Up to 15 read replicas (Only 5 in RDS), replication process is faster 
- Cost more than RDS (20%) but more efficient
- Master instance take writes and failover in less than 30 secs
- Master + up to 15 read replicas (Auto scaling)
- Support cross origin replication
- DB Cluster
	- Writer endpoint (points to master)
	- Reader endpoint (points to RR using connection load balancing)
- Backtrack: restore data at any point of time without using backups
- Security same as RDS
- We can define custom read endpoints for specific reads![enter image description here](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/AuroraArch001.png)
- **Features**
	- *Serverless*: Automated db instantiation and auto scaling, no capacity planning needed, pay per second
	- *Multi-Master*: In case you want immediate failover for write node, every node does r/w 
		  or promoting a RR as the new master
	- *Cross Region RR*: useful in disaster recovery, simple to put in place
	- *Global Database*: 1 Primary region (r/w), 5 secondary (r only) region with < 1sec replication lag, up to 16 RR per secondary region and promoting another region with RTO < 1 min ![enter image description here](https://d2908q01vomqb2.cloudfront.net/887309d048beef83ad3eabf2a79a64a389ab1c9f/2021/03/01/Screen-Shot-2021-03-01-at-17.37.29.png)
	- *ML*: prediction using SQL (SageMaker and Comprehend)		
- **Use Cases**
	- Store relational datasets (RDBMS/OLTP)
	- Perform SQL queries
	- Transactional inserts/update/delete is available
	- Less maintenance and more performance
- **For Solution Architect**	  		  
	- *Operations* - less operations, auto scaling storage
	- *Security* - AWS responsible for OS security, we are responsible for setting up KMS, SG, IAM policies, authorizing users in DB
	- *Reliability* - Multi AZ feature, highly available (more than RDS), Serverless option, Multi-Master option
	- *Performance* - 5x performance due to architectural optimization. Up to 15 read replicas (Only 5 in RDS)
	- *Cost* - Pay per hour based on provisioned EC2 and Storage Usage. Lower cost compared to Enterprise grade databases such as Oracle
### [ElastiCache](https://docs.aws.amazon.com/AmazonElastiCache/latest/red-ug/elasticache-use-cases.html)
- Managed in-memory key-value data stores in the cloud with sub-ms latency
- Must provision an EC2 instance type
- Support for Clustering (Redis) and Multi AZ, Read replicas
- Makes your application stateless
- Security through IAM, SG, LMS, Redis Auth
- Backup / Snapshot / Point in time restore
- Monitor through CloudWatch
- Reduce load off of databases for read intensive workloads
- For real-time use cases like Caching, Session Stores, Gaming, Geospatial Services, Real-Time Analytics, and Queuing
- **REDIS**
	- In memory data structure store used as database, cache and message broker
	- Multi AZ with Auto failover
	- RR to scale reads and have high availability
	- Data durability using AOF persistence
	- Backup and restore feature
- **MEMCACHED**
	- High performance, distributed memory object caching system
	- intended for use in speeding up dynamic web applications
	- Multi-node for partitioning of data (Sharding)
	- No high availability (Replication)
	- Non persistent
	- No backup and restore
	- Multi-threaded architecture
- **Security**
	- Do not support IAM authentication
	- IAM policies are only used for AWS API level security
	- Redis: Use AUTH (password/token), supports SSL in flight encryption
	- Memcached: SASL based authentication
- **Patterns**
	- Lazy loading: all read data is cached
	- Write through: Adds or update data in the cache when written to DB (np stale data)
	- Session store: store temporary session data in a cache (using TTL)
- **Use cases**
	- Frequent reads, less writes
	- Cache result of DB
	- Store session data for website
	- Eg. Gaming leaderboard: Redis sorted set guarantee both uniqueness and order, so each time a new element added, its ranked in real time and added in order
-  **For Solution Architect**
	- *Operations* - Same as RDS
	- *Security*
		- ASW responsible for OS security
		- We're responsible for setting up KMS, SG, IAM policies, users (Redis Auth), using SSL
	- *Reliability* - Clustering, Multi-AZ
	- *Performance*
		- Sub-millisecond
		- In memory
		- Read replicas for Sharding
	- *Cost* - Pay per hour based on EC2 and storage use
### [S3 (Simple Storage Service)](https://docs.aws.amazon.com/AmazonS3/latest/userguide/Welcome.html)
- Simple web services interface that you can use to store and retrieve any amount of data, at any time, from anywhere 
- Great for big objects, no so great for small objects
- Serverless, scales infinitely, max object size 5TB
- Strong consistency
- **S3 Buckets**
	- Has global unique name
	- Defined at the region level (S3 is not global service but do provide global console)
	- Naming (No uc, lc, 3-63 chars long, not an IP and start with lc or number)
	- Key is composed of prefix (my_folder) + object name (my_file.txt) = my_folder/my_file.txt
	- Full URL would be S3://my-unique-bucket/my_folder/my_file.txt
	- No concepts of directory within buckets
	- *Objects* 
		- Max size of object 5TB
		- For upload more than 5gb, must use multi-part upload
		- Metadata (list of key/value pair)
		- Tags (Unicode key/value pair, up to 10)
		- Version Id
		- Has public object URL (Has to configure it as public)
-  **S3 Versioning**
	- Version your files with multiple uploads of same file
	- Enabled at bucket level
	- Easy to role back in case of accidental delete
	- Normal delete just add delete-marker on object and hide them, you have delete versions for permenent delete
- **S3 Encryption**
	- *SSE-S3* Encrypts objects using keys handled & managed by AWS
		- Encrypted at server side 
		- Encrypted with AES-256 & S3 managed data key
		- Must set header "x-amz-server-side-encryption":"AES256" in request
	- *SSE-KMS* Encrypts objects using keys from KMS
		- Encrypted at server side
		- Encrypted using keys managed by KMS
		- Must set header "x-amz-server-side-encryption":"aws:kms" in request
		- Has user control and audit trails
		- KMS can caused performance issue as it calls KMS API GenerateDataKey while upload and Decrypt while download
	- *SSE-C* With own encryption keys
		- Encrypted at server side
		- Encrypted using keys managed by customer outside AWS
		- HTTPS must be used
		- Key must be provided in HTTP headers for every request
	- *Client side* encryption
		- Encrypted at client side
		- Customer uploads encrypted object 
	- *In transit (SSL/TLS)*
		- S3 exposed 
			- HTTP endpoint which is non-encrypted
			- HTTPS endpoint which is encryption in flight
		-  Encryption in flight also called SSL/TLS
- **Security and Bucket policies**
	-  *IAM*, API called allowed for specific user
		- An IAM principle can access an S3 object if the user IAM permissions allow it OR resource policy allows it
		  and there's not explicit deny
	- *Resource based* policies 
		- Bucket policies: bucket wide rules from S3 - allows across account
		- Object Access Control List (ACL) - Finer grain
		- Bucket Access Control List (ACL) - less common
	- *Json based* policies 
		- Resources: buckets and objects
		- Actions: Set of API to allow or deny
		- Effect: Allow/Deny
		- Principle: The account or user to apply the policy to
		- Use cases
			- Grant public access to bucket
			- Force object to be encrypted at upload
			- Grant access to another account 
	- **Block public access** by Bucket settings
		- Block public access to bucket and object granted through
			- By Access control list (any, new)
			- By Access point policies
		- These settings created to prevent data leaks 
		- If you don't want your bucket public leave this setting ON
		- Can be set at the account level
	- **Network Security**
		- Supports VPC endpoints (for instance in VPC without www internet)
		- Access logs can be stored in other bucket
		- API call logged in AWS CloudTrails
	- **User Security**
		- MFA for versioned buckets to delete objects
		- Pre-signed URLs that are valid only for limited time
- **S3 Websites**
	- S3 can host static websites and have them accessible on the www
	- URL will be bucket-name.s3-website-aws-region.amazonaws.com
	- Make sure bucket allows public reads
- **S3 CORS** (Cross Origin Resource Sharing)
	- Web Browser based mechanism does not allow request to other origins while visiting the main
	   origin unless allowed by other origin
	- If client does a cross origin request on our S3 bucket, we need to enable the correct CORS headers
	- You can allow specific origin or all (*)
- **S3 Consistency Model**
	- Strong consistent as of Dec 2020
	- After a
		- Successful write of new object (new PUT)
		- or an overwrite or delete of existing object
	- subsequent request immediately receives a latest version of the object (r/w consistency)
	- subsequent list request  immediately reflect changes (List consistency)
- [S3 MFA-Delete](https://docs.aws.amazon.com/AmazonS3/latest/userguide/MultiFactorAuthenticationDelete.html)
	- Enable versioning on bucket
	- MFA will be needed to 
		- Permanently delete an object version
		- Suspend versioning
	- MFA will not be needed to
		- Enabling versioning
		- Listing deleted versions
	- Only the bucket owner (root account) can enable/disable MFA-Delete
- **S3 Access logs**
	- For audit purpose you may want to log all access, request made to S3 etc.
	- Do not set logging bucket to be monitored bucket (app bucket), it will create logging loop and bucket size will 
	   grow exponentially
- **S3 Replication**
	- Must enable versioning
	- Bucket can be different account
	- Copying is asynchronous
	- Must give proper IAM permissions to S3
	- Cross Region Replication (CRR) for compliance, lower latency access, replication across account
	- Same Region Replication (SRR) Log aggregation, live replication between accounts
	- After activating only new object are replicated
	- For delete operations
		- Can replicate delete markers from source to target (optional setting)
		- Deletion with a version ID are not replicated
	- No chaining of replication (if bucket 1 has replication into bucket 2, which has replication on bucket 3 then objects
	  created in bucket 1 are not replicated in bucket 3)
	 - Does not replicate deletes 
- **S3 Pre-signed URL**
	- Using CLI or SDK
	- Expiration of 3600s, can change with --expires-in [Times-in-seconds] argument
	- Users given pre-signed URL inherit the permissions of the person who generate the URL for GET/PUT
	- Example
		- Allow only logged in user to download video from S3 bucket
		- Allow temporary user to upload file to precise location of bucket
- [S3 Storage classes](https://aws.amazon.com/s3/storage-classes/)
	- *S3 Standard* 
		- General purpose
		- High Durability (99.9%) of objects across Multi-AZ
		- 99.9% Availability over a given year
		- Sustain 2 concurrent facility failure
		- Use cases	
			- Big data analytics
			- Mobile and gaming application
			- Content distribution
	- *S3 Standard - Infrequent Access (IA)*  
		- When files are less frequently accessed
		- Low cost compare to Standard
		- High Durability (99.9%) of objects across Multi-AZ
		- 99.9% Availability over a given year
		- Sustain 2 concurrent facility failure
		- Use cases	
			- Data store for disaster recovery, backups
		- Minimum storage duration charge for 30 days
	-  *S3 One Zone - Infrequent Access (IA)*
		- Same as IA but data stored in a single AZ
		- Low cost compare to Standard
		- High Durability (99.9%) of objects in a single AZ, but when AZ destroyed data will be lost
		- 99.5% Availability over a given year
		- Low latency and high throughput
		- Support SSL
		- Lower cost compared to IA
		- Use cases	
			- Secondary backups of on-premise data
			- Storing data you can recreate
		- Minimum storage duration charge is 30 days
	- *Intelligent tiering* 
		- Same Low latency and high throughput as Standard 
		- Small monthly monitoring and auto-tiering fee
		- Auto move data between two access tiers based on chaining access patterns
		- High Durability (99.9%) of objects across Multi-AZ
		- 99.9% Availability over a given year
		- Resilient against events that impact an entire AZ
		- Minimum storage duration charge for  30 days
	- *Glacier*
		- Low cost object storage mean for achieving
		- Data is retained for the longer term (10s of years)
		- High Durability (99.9%)
		- Cost/storage per month ($0.004/GB) + retrieval cost
		- Each item called as "Archive" (up to 40TB)
		- Archives are stored as "Vaults"
		- 3 Retrieval options
			- Expedited (1-5m) - expensive (per GB retrieved)
			- Standard (3-5h)
			- Bulk (5-12h)
		- Minimum storage duration charge for  90 days
	- *Glacier Deep Archive* 
		- For Archive you don't need right away
		- Retrieval options
			- Standard (12h)
			- Bulk (48h)
		- Minimum storage duration charge for 180 days
	- **Lifecycle Rules** 
		- Moving objects between storage classes can be automated using lifecycle configuration
		- Transition actions - defines when object are transitioned into another storage class
			- Move objects to Standard IA after 60 days of creation
			- Move to Glacier for archiving after 6 months
		- Expiration actions - configure objects to expire (delete) after some time
			-  Access log files can be set to delete after 365 days
			- Can be used to delete old version of files (if versioning enabled)
			- Can be used to delete incomplete multi-part uploads
		- Rules can be created for a certain prefix (e.g. s3://mybucket/mp3/*)
		- Rules can be created for a certain object tags (e.g. Dept:Fianance)
- **S3 Analytics**
	- To determine when to transition objects between storage classes
	- Does not work for One Zone IA or Glacier
	- Takes 24-48h to first start
- **S3 Performance**
	- Auto scales to high request rate, latency 100-200ms
	- Can achieve at least 3500 PUT/COPY/POST/DELETE and 5500 GET/HEAD request / second / prefix in a bucket
	- No limit to the number of prefixes in a bucket
	- bucket/folder-x/folder-y/file >>prefix is /folder-x/folder-y/
	- if you spread reads across n prefixes evenly then you can achieve n*5500 request / second for GET and HEAD
	- S3 Transfer Acceleration increase speed by transferring file to an AWS edge location which will forward the data to S3
	  S3 bucket in the target region (File in USA >fast www> Edge Location >fast private dns> S3 bucket in Australia)
	- S3 Byte-Range Fetches - parallelize GETS by requesting specific byte ranges, has better resilience in case of 
	  failures, useful to speed up downloads  
- **S3 Select & Glacier Select**
	- Retrieve less data using SQL by performing server side filtering
	- Can filter by rows/columns
	- Less network transfer, less CPU cost  
- **S3 Events notification**
	- e.g. S3:ObjectCreated
	- Use case: generate thumbnails of images uploaded to s3
	- Can create as many S3 events
	- Delivers events in secs but may take mins sometime 
- **S3 Requester Pays**
	- In Standard Bucket - while download, Owner pays for both Storage and Networking Cost
	- In Requester Pays Bucket - while download, Owner pays Storage cost and Requester pays Networking Cost
	- Requester must be authenticated in AWS
	- Helpful when you want to share large datasets with other accounts
- **S3 Lock policies & Glacier Vault Lock**
	- *Glacier Vault Lock*
		- To Adopt WORM (Write once Read Many) model
		- Lock the policy for future edits (can no longer changed)
		- For Compliance and Data retention
	- *S3 Object lock*
		- Versioning must be enabled
		- To Adopt WORM (Write once Read Many) model
		- Block an object version deletion for specified time
		- *Object Retention*
			- Retention period
			- Legal hold - No expiry date
		- *Modes*
			- Governance mode - user cant overwrite or delete object version or alter lock settings unless special permission
			- Compliance mode - protected object version cant be overwritten or deleted by any user, including root user, 				   when object locked in compliance mode, retention mode can't be changed and retention period can't be shortened
- **Use cases**
	- Static files
	- Key value store for big files
	- Static Website hosting
-   **For Solution Architect**
    -   _Operations_  
	    - No operation needed
    -   _Security_
        - IAM
        - Bucket policies
        - ACL
        - Encryption (Server/Client)
        - SSL
    -   _Reliability_ 
	    - 99.9 % durability
	    - 99.9% availability
	    - Multi AZ
	    - CRR
    -   _Performance_
        - Scales to thousand of reads/writes per second
        - Transfer acceleration
        - Multi-part for big files
    -   _Cost_  
	    -  Pay per storage use, network cost and number of requests
###  [Athena](https://aws.amazon.com/premiumsupport/knowledge-center/analyze-logs-athena/)
- Fully serverless database with SQL capabilities
- Perform analytics directly against S3 files using SQL queries
- Has JDBC/ODBC driver
- Charged per Query 
- Supports CSV, JSON, ORC etc.
- Outputs results back to S3
- Secured through IAM
- Analyze data directly on S3 > use Athena 
- Use cases
	- One time SQL queries on S3
	- Business Intelligence/Analytics/Reporting
	- CloudTrails
	- VPC Flow logs/ELB logs
- **For Solution Architect**
    - _Operations_  
	    - No operation needed
    - _Security_
        - IAM + S3
    - _Reliability_ 
	    - Managed Service
	    - Use presto engine, Highly available
    - _Performance_
        - Queries scale based on data size
    - _Cost_  
	    -  Pay per query, per TB of data scanned 
###  [RedShift](https://docs.aws.amazon.com/redshift/latest/dg/welcome.html)
- Based on PostgreSQL, but it's not used for OLTP (Online Transaction Processing)
- It's OLAP (Online analytical processing) for analytics and data warehousing
- 10x better performance than other data warehoused, scales to PBs of data
- Columnar storage of data (instead of row)
- Massive Parallel Query Execution (MPP)
- Pay as you go based on the instance provisioned
- Has SQL interface for performing queries
- BI tools such as AWS QuickSight or Tableau integrates with it
- Data loaded from S3, DynamoDB, DMS and other DBs
- Leader node: for query planning, result aggregation
- Compute node: for performing the queries, send result to leader
- Redshift Spectrum: perform queries directly against S3 (no need to load)
- Backup & restore facility
- Security though VPC, IAM and KMS
- ![enter image description here](https://d1.awsstatic.com/redshift/redshift-pdp/product-page-diagram_Redshift-Data-Sharing.cfb492d92166375ec67d5e73fcfa397e75fe9ea0.png)
- **Snapshots & DR**
	- Redshift has no "Multi-AZ" mode
	- Snapshots are point-in-time backups of a cluster, stored into S3
	- Snapshots are incremental (Only what has changed is saved)
	- You can restore a snapshot into a new cluster
	- *Automated* 
		- Every 8 hours, every 5 GB or on a schedule
		- Can set retention
	- *Manual*
		- Snapshot is retained until you delete it
	- You can configure Amazon Redshift to automatically copy snapshot (automated or manual) of a cluster to another AWS region
- **Redshift Spectrum**
	- Query data that is already in S3 without loading it
	- Must have a Redshift cluster available to start the query
	- The query then submitted to thousands of Redshift Spectrum nodes
	- ![enter image description here](https://d2908q01vomqb2.cloudfront.net/b6692ea5df920cad691c20319a6fffd7a4a766b8/2017/07/18/redshift_spectrum-1.gif)
- Use cases
	- Real-time analytics
	- Combining multiple data sources
	- Business intelligence
	- Log analysis
- **For Solution Architect**
    -  _Operations_  
	    - RDS
    - _Security_
        - IAM, VPC, KMS, SSL
    - _Reliability_ 
	    - Auto healing feature
	    - Cross region snapshot copy
    - _Performance_
        - 10x performance vs other data warehousing
        - Faster queries/joins/aggregations because of indexes
    - _Cost_  
	    -  Pay per node provisioned
	    -  1/10th of the cost vs other warehouses
###  [Neptune](https://docs.aws.amazon.com/neptune/latest/userguide/intro.html)
- Fully managed graph database
- When do use
	- High relationship between data
	- Social networking
	- Knowledge graphs (Wikipedia)
- Highly available across 3 AZ, with up to 15 read replicas
- Point-in-time recovery, continue backup to Amazon S3
- Supports for KMS encryption at rest + HTTPS
- **For Solution Architect**
    -   _Operations_  
	    - RDS
    -   _Security_
        - IAM, VPC, KMS, SSL
    -   _Reliability_ 
	    - Multi AZ
	    - Clustering
    -   _Performance_
        - Best suited for graphs
        - Clustering to improve performance
    -   _Cost_  
	    -  Pay per node provisioned
###  [ElasticSearch](https://aws.amazon.com/opensearch-service/)
- Can search any field, even partially matches
- Common to use ElasticSearch as a complement to another database
- Some usage for Big Data Applications
- You can provision a cluster of instances
- Built in integration for Kinesis Data Firehose, AWS IoT, CloudWatch logs
- Comes with Kibana (visualization) & Logstash (log ingestion)
- ![enter image description here](https://d1.awsstatic.com/product-page-diagram_HIW_Amazon-OpenSearch.bccd42c9b855877a40e4eb3c55511a8aae1002a4.png)
- **For Solution Architect**
    -   _Operations_  
	    - RDS
    -   _Security_
        - Cognito, IAM, VPC, KMS, SSL
    -   _Reliability_ 
	    - Multi AZ
	    - Clustering
    -   _Performance_
        - Based on ElasticSearch project (open source)
        - Petabyte scaling
    -   _Cost_  
	    -  Pay per node provisioned
###  [Snow Family](https://docs.aws.amazon.com/snowball/)	
- Highly secure portable devices to collect and process data a the edge, and migrate data into or out of AWS
- You can use these devices to locally and cost-effectively access the storage and compute power of the AWS Cloud in places where an internet connection might not be an option
- **Data migration** devices
	- Snowcone
	- Snowball Edge
	- Snowmobile
- **Edge computing** devices
	- Snowcone
	- Snowball Edge
- **When to use Snowball devices?**
	- When migration has challenges such as
		- Limited connectivity and bandwidth
		- High network cost
		- Shared bandwidth
		- Unstable connection
	- If data migration takes more than a week to transfer over the network![enter image description here](https://d2908q01vomqb2.cloudfront.net/e1822db470e60d090affd0956d743cb0e7cdf113/2020/12/08/AWS-Snow-Family-data-migration-workflow.png)
- [Snowcone](https://docs.aws.amazon.com/snowball/latest/snowcone-guide/snowcone-what-is-snowcone.html)
	- Small portable computing anywhere, rugged & secure, withstand harsh enviroments
	- Light weight (2.1 kg)
	- Device used for edge computing, storage, and data transfer
	- 8TB of usable storage
	- Must provide your won battery
	- Can be sent back to AWS offline, or connect it to internet and use AWS DataSync to send data
	- *Use Cases*
		- Use where Snowball does not fir (space-constrained environment)
		- To collect data, process the data to gain immediate insight, and then transfer the data online to AWS
		- To distribute media, scientific, or other content from AWS storage services to your partners and customers
- [Snowball Edge](https://docs.aws.amazon.com/snowball/latest/developer-guide/whatisedge.html)
	- Device with on-board storage and compute power for select AWS capabilities
	- Can do local processing and edge-computing workloads in addition to transferring data between
	- Each Snowball Edge device can transport data at speeds faster than the internet
	- This transport is done by shipping the data in the appliances through a regional carrier
	- The appliances are rugged, complete with E Ink shipping labels
	- Alternative to moving data over network (avoid paying network fees)
	- Pay per data transfer job
	- Provide block storage and S3 compatible object storage
	- Snowball Edge devices have three options for device configurations
		- _Storage Optimized_
			- 80TB of HDD for block volume and S3 compatible object storage **for data transfer**
			- 80 TB of usable storage space, 24 vCPUs, and 32 GiB of memory for compute functionality **with EC2 compute functionality**
		- _Compute Optimized_
			- 42 TB of HDD capacity for either object storage or block storage volumes
			- Most compute functionality, with 52 vCPUs, 208 GiB of memory, and 42 TB (39.5 usable) plus 7.68 TB of dedicated NVMe (Non-Volatile Memory Express)  SSD for compute instances for block storage volumes for EC2 compute instances
		-  _Compute Optimized with GPU_
			- Identical to the Compute Optimized option, except for an installed GPU, equivalent to the one available in the P3 Amazon EC2 instance type
			- 42 TB (39.5 TB of HDD storage that can be used for a combination of Amazon S3 compatible object storage and Amazon EBS compatible block storage volumes) plus 7.68 TB of dedicated NVMe SSD for compute instances
	- *Use cases*
		- Cloud data migration
		- DC decommission
		- Disaster recovery
- Snowmobile
	- Transfers exabytes of data (1EB = 1000 PB = 10,00,000 TBs)
	- Each Snowmobiles has 100 PB capacity (use multiple in parallel)
	- High Security - temperature controlled, GPS, 24x7 video surveillance
	- Use when want to send more than 10PB of data
- **Edge Computing**
	- Process data while its being created on a edge location E.g. truck on load, a ship on the sea etc
	- These location may have limited or no internet
	- We can setup Snowball Edge / Snowcone device for edge computing
	- Use cases
		- Pre-process data
		- Machine learning at the edge
		- Transcoding media streams
	- Eventually we can ship back device to AWS
- [AWS OpsHub](https://docs.aws.amazon.com/snowball/latest/developer-guide/aws-opshub.html)
	- It is an application that you can download and install on any Windows or Mac
	- Graphical user interface you can use to manage your AWS Snowball devices
	- Enabling you to rapidly deploy edge computing workloads and simplify data migration to the cloud
	- Supports the Snowball Edge Storage Optimized and Snowball Edge Compute Optimized devices
	- Monitors devices metrics (Storage capacity, Active instances)
	- Launches compatible AWS services on your devices (EC2, DataSync, NFS)
- **Snowball into Glacier**
	- Snowball can not import to Glacier directly
	- You must import into S3 first then in combination with S3 lifecycle policy move it to Glacier
-  [Storage Gateway](https://aws.amazon.com/storagegateway/?whats-new-cards.sort-by=item.additionalFields.postDateTime&whats-new-cards.sort-order=desc)
	- Set of hybrid cloud services that gives you on-premises access to virtually unlimited cloud storage
	- Bridge Between on-premise data and cloud data in S3
	- Customers use Storage Gateway to integrate AWS Cloud storage with existing on-site workloads so they can simplify storage management and reduce costs for key hybrid cloud storage use cases
###  [S3 File Gateway](https://aws.amazon.com/storagegateway/file/s3/)
- Configured S3 buckets are accessible using SMB or NFS protocol with local caching
- Supports S3 standard, S3 IA, S3 One Zone IA
- Access using IAM roles for each gateway
- Can be mounted on may servers
- Integrated with Active Directory for user Authentication![enter image description here](https://docs.aws.amazon.com/storagegateway/latest/userguide/images/file-gateway-concepts-diagram.png)
- **Amazon FSx File Gateway**
	- [FSx for windows](https://aws.amazon.com/fsx/windows/)
		- EFS is a shared POSIX system for Linux system and Windows File Server can not use EFS
		- To solve this, *FSx for windows* is fully managed Windows file system share drive
		- Supports SMB protocol & Windows NTFS
		- Has Microsoft AD integration
		- Scale up to 10s of GB/s, millions of IOPS, 100s PB of data
		- Can be accessed from your on-premise infra
		- Can be configures to be Multi-AZ
		- Data backed up S3 daily![enter image description here](https://res.infoq.com/news/2021/05/amazon-fsx-file-gateway/en/resources/1FSx-1619859707544.png)
	- [FSx for Lustre](https://aws.amazon.com/fsx/lustre/)
		- Type of parallel distributed file system for large scale computing
		- **L** inux Cl **uster** 
		- Scales up to 100s GB/s, millions of IOPS
		- Seamless integration with S3 (**Can read S3 as file system through FSx**)
		- **Use cases**
			- Machine Learning
			- Video Processing
			- Financial modelling
		- **File System Deployment options**
			- *Scratch File System*
				- Temporary storage
				- Data is not replicated
				- High Burst
				- For short term processing
			- *Persistent File System*
				- Long-term storage
				- data is replicated
				- Replace failed files within mins
				- For long term processing, sensitive data
		- ![enter image description here](https://d2908q01vomqb2.cloudfront.net/e1822db470e60d090affd0956d743cb0e7cdf113/2020/04/24/FSx-for-Lustre-persistent-storage-diagram.png =500x500)
- [Tape Gateway](https://aws.amazon.com/storagegateway/vtl/)
	- Backup process using physical tapes
	- With Tape gateway, company use same process but, in the cloud
	- Virtual Tape Library (VTL) backed by S3 and Glacier
	- Backup data using existing tape-based processes![enter image description here](https://d1.awsstatic.com/cloud-storage/tape-gateway-diagram.4b6ca2b4e3f97d4df7988544400ae91424503248.png)
- [Volume Gateway](https://aws.amazon.com/storagegateway/volume/)
	- Block storage using iSCSI protocol backed by S3
	- Backed by EBS snapshots to restore on-premises volumes
	- *Cached Volume*-  low latency access to most recent data
	- *Stored Volume* - entire dataset is on premise, schedule backups to S3![enter image description here](https://d1.awsstatic.com/cloud-storage/volume-gateway-diagram.eedd58ab3fb8a5dcae088622b5c1595dac21a04b.png)
- *On-premised data to the cloud >> Use Storage Gateway*
		- *File access / NFS with AD >> File Gateway (Backed by S3)*
		- *Volume / Block Storage / iSCSI >> Volume Gateway (Backed by S3 with EBS snapshot)*
		- *VTL Tape/Backup with iSCSI >> Tape Gateway  (Backed by S3 and Glacier)*
		- *No on-premise virtualization>> Hardware appliances*
- AWS transfer family![AWS Transfer Family | Amazon Web Services](https://d1.awsstatic.com/cloud-storage/aws-transfer-family-s3-efs-how-it-works-diagram.6ff1dc0d717f63d4207ce4670a729aabb85d0d70.png)
 - AWS Storage options![enter image description here](https://d2908q01vomqb2.cloudfront.net/cb4e5208b4cd87268b208e49452ed6e89a68e0b8/2018/03/20/aws-storage-soutions.jpg)
![enter image description here](https://miro.medium.com/max/1838/1*02FpTqeqNH6XzcrBUqjR_w.png =600x600 )

### Storage Services Q&A
Q: My company would like to have a MySQL database internally that is going to be available even in  case of a disaster in the AWS Cloud. I should setup	
> Multi-AZ (In this question, we consider a disaster to be an entire Availability Zone going down. In
   which case Multi-AZ will help. If we want to plan against an entire region going down, backups and
   replication across regions would help.)

Q: Our RDS database struggles to keep up with the demand of the users from our website. Our million users mostly read news, and we don't post news very often. Which solution is NOT adapted to this problem?
> RDS Multi-AZ (ElastiCache and RDS Read Replicas do indeed help with scaling reads.)	

Q: We have setup read replicas on our RDS database, but our users are complaining that upon updating  their social media posts, they do not see the update right away
> Read Replicas have asynchronous replication and therefore it's likely our users will only observe
   eventual consistency

Q: Which RDS Classic (not Aurora) feature does not require us to change our SQL connection string?
> Multi-AZ (Multi AZ keeps the same connection string regardless of which database is up. Read
  Replicas imply we need to reference them individually in our application as each read replica will
  have its own DNS name)

Q: You want to ensure your Redis cluster will always be available
> Enable Multi-AZ (Multi AZ ensures high availability)

Q: Your application functions on an ASG behind an ALB. Users have to constantly log back in and you'd  rather not enable stickiness on your ALB as you fear it will overload some servers. What should you  do?
> Store session data in ElastiCache (Storing Session Data in ElastiCache is a common pattern to
   ensuring different instances can retrieve your user's state if needed.)

Q: One analytics application is currently performing its queries against your main production  database. These queries slow down the database which impacts the main user experience. What should you do to improve the situation?
> Setup Read Replica

Q: You have a requirement to use TDE (Transparent Data Encryption) on top of KMS. Which database technology does NOT support TDE on RDS?
> PostgreSQL

Q: You would like to ensure you have a database available in another region if a disaster happens to your main region. Which database do you recommend?
> Aurora Global Database (Global Databases allow you to have cross region replication, RDS does not
 support replication at cross region level)

Q: How can you enhance the security of your Redis cache to force users to enter a password?
> Use Redis Auth

Q: Your company has a production Node.js application that is using RDS MySQL 5.6 as its data backend.  A new application programmed in Java will perform some heavy analytics workload to create a  dashboard, on a regular hourly basis. You want to the final solution to minimize costs and have minimal disruption on the production application, what should you do?
> Create a Read Replica in a different AZ and run the analytics workload on the replica database

Q: You would like to create a disaster recovery strategy for your RDS PostgreSQL database so that in case of a regional outage, a database can be quickly made available for Read and Write workload in another region. The DR database must be highly available. What do you recommend?
> Create a Read Replica in a different region and enable multi-AZ on the main database

Q: You are managing a PostgreSQL database and for security reasons, you would like to ensure users are authenticated using short-lived credentials. What do you suggest doing?
> Use PostgreSQL for RDS and authenticate using a token obtained through the RDS service. 
   (In this case, IAM is leveraged to obtain the RDS service token, so this is the IAM 
   authentication use case.)

Q: Which RDS database technology does NOT support IAM authentication?
> Oracle

Q: An application is running in production, using an Aurora database as its backend. Your development team would like to run a version of the application in a scaled-down application, but still, be able to perform some heavy workload on a need-basis. Most of the time, the application will be unused. Your CIO has tasked you with helping the team while minimizing costs. What do you suggest?
> Use Aurora Serverless

Q: You're trying to upload a 25 GB file on S3 and it's not working
> Should use multipart upload for file bigger than 5GB (Multi Part Upload is also recommended as soon as the file is over 100MB)

Q: I tried creating an S3 bucket named "dev" but it didn't work. This is a new AWS Account and I have no buckets at all. What is the cause?
> Bucket names must be globally unique and "dev" is already taken

Q: You've added files in your bucket and then enabled versioning. The files you've already added will have which version?
> null

Q: Your client wants to make sure the encryption is happening in S3, but wants to fully manage the encryption keys and never store them in AWS. You recommend
> SSE-C (Here you have full control over the encryption keys, and let AWS do the encryption)

Q: Your company wants data to be encrypted in S3, and maintain control of the rotation policy for the encryption keys, but not know the encryption keys values. You recommend
> SSE-KMS (With SSE-KMS you let AWS manage the encryption keys but you have full control of the key rotation policy)

Q: Your company does not trust S3 for encryption and wants it to happen on the application. You recommend
> Client Side Encryption (With Client Side Encryption you perform the encryption yourself and send the encrypted data to AWS directly. AWS does not know your encryption keys and cannot decrypt your data.)

Q: The bucket policy allows our users to read/write files in the bucket, yet we were not able to perform a PutObject API call.
> The IAM user must have an explicit DENY in the attached IAM policy (Explicit DENY in an IAM policy will take precedence over a bucket policy permission)

Q: You have a website that loads files from another S3 bucket. When you try the URL of the files directly in your Chrome browser it works, but when the website you're visiting tries to load these files it doesn't. What's the problem?
> CORS is wrong (Cross-origin resource sharing (CORS) defines a way for client web applications that are loaded in one domain to interact with resources in a different domain. To learn more about CORS, go here: https://docs.aws.amazon.com/AmazonS3/latest/dev/cors.html)

Q: You have enabled versioning and want to be extra careful when it comes to deleting files on S3. What should you enable to prevent accidental permanent deletions?
> Enable MFA Delete (MFA Delete forces users to use MFA tokens before deleting objects. It's an extra level of security to prevent accidental deletes)

Q: You would like all your files in S3 to be encrypted by default. What is the optimal way of achieving this?
> Enable "Default Encryption" on S3

Q: You suspect some of your employees to try to access files in S3 that they don't have access to. How can you verify this is indeed the case without them noticing?
> Enable S3 Access Logs and analyze them using Athena (S3 Access Logs log all the requests made to buckets, and Athena can then be used to run serverless analytics on top of the logs files)

Q: You are looking for your entire S3 bucket to be available fully in a different region so you can perform data analysis optimally at the lowest possible cost. Which feature should you use?
> S3 cross region replication (S3 CRR is used to replicate data from an S3 bucket to another one in a different region)

Q: You are looking to provide temporary URLs to a growing list of federated users in order to allow them to perform a file upload on S3 to a specific location. What should you use?
> S3 Pre-Signed URL  (Pre-Signed URL are temporary and grant time-limited access to some actions in your S3 bucket.)

Q: How can you automate the transition of S3 objects between their different tiers?
> Use S3 Lifecycle rules

Q: Which of the following is NOT a Glacier retrieval mode?
> Instant (10s)

Q: Which of the following is a Serverless data analysis service allowing you to query data in S3?
> Athena

Q: You are looking to build an index of your files in S3, using Amazon RDS PostgreSQL. To build this index, it is necessary to read the first 250 bytes of each object in S3, which contains some metadata about the content of the file itself. There is over 100,000 files in your S3 bucket, amounting to 50TB of data. how can you build this index efficiently?
> Create an application that will traverse the S3 bucket, issue a Byte Range Fetch for the first 250 bytes, and store that information in RDS.
	
Q: You need to move hundreds of Terabytes into the cloud in S3, and after that pre-process it using many EC2 instances in order to clean the data. You have a 1 Gbit/s broadband and would like to optimize the process of moving the data and pre-processing it, in order to save time. What do you recommend?
> Use Snowball Edge (Snowball Edge is the right answer as it comes with computing capabilities and allows use to pre-process the data while it's being moved in Snowball, so we save time on the pre-processing side as well)

Q: You want to expose a virtually infinite storage for your tape backups. You want to keep the same software as today and want a iSCSI compatible interface. What do you use?
> Tape Gateway

Q: Your EC2 Windows Servers need to share some data by having a Network File System mounted, that respect the Windows security mechanisms and has integration with Active Directory. What do you recommend putting in place as an NFS?
> FSx for windows

Q: You would like to have a distributed POSIX compliant file system that will allow you to maximize the IOPS in order to perform some HPC and genomics computational research. That file system will have to scale easily to millions of IOPS. What do you recommend?
> FSx for Lustre

Q: Which database helps you store data in a relational format, with SQL language compatibility and capability of processing transactions?
> RDS

Q: Which database do you suggest to have caching capability with a Redis compatible API?
> ElastiCache  (ElastiCache can create a Redis cache or a Memcached cache)

Q: You are looking to perform OLTP, and would like to have the underlying storage with the maximum amount of replication and auto-scaling capability. What do you recommend?
> Aurora (RDS does not have auto scaling capability)

Q: As a solution architect, you plan on creating a social media website where users can be friends with each other, and like each other's posts. You plan on performing some complicated queries such as "What are the number of likes on the posts that have been posted by the friends of Mike?". What database do you suggest?
> Neptune (This is AWS' managed graph database)

Q: You would like to store big objects of 100 MB into a reliable and durable Key Value store. What do you recommend?
> S3 (S3 is indeed a key value store! (where the key is the full path of the object in the bucket))

Q: You would like to have a database which is efficient at performing analytical queries on large sets of columnar data. You would like to connect that Data Warehouse to a reporting and dashboard tool such as Amazon Quicksight. Which technology do you recommend?
> Redshift

Q: Your log data is currently stored in S3 and you would like to perform a quick analysis if possible serverless to filter the logs and find a user which may have completed an unauthorized action. Which technology do you recommend?
> Athena

Q: Your gaming website is currently running on top of DynamoDB. Users have been asking for a search feature to find other gamers by name, with partial matches if possible. Which technology do you recommend to implement that feature?
> ElasticSearch (Anytime you see "search", think ElasticSearch)


    Important ports:
    FTP: 21
    SSH: 22
    SFTP: 22 (same as SSH)
    HTTP: 80
    HTTPS: 443
    vs RDS Databases ports:
    PostgreSQL: 5432
    MySQL: 3306
    Oracle RDS: 1521
    MSSQL Server: 1433
    MariaDB: 3306 (same as MySQL)
    Aurora: 5432 (if PostgreSQL compatible) or 3306 (if MySQL compatible)


## Route 53
 - [Docs](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/Welcome.html)
 - DNS (Domain name system) is collection of rules and records which helps client to understand how to reach a server through its domain name
 - Amazon Route 53 is a highly available and scalable Domain Name System (DNS) web service. You can use Route 53 to perform three main functions in any combination: domain registration, DNS routing, and health checking.
 - In aws, more common records are
	 - A: Hostname to IPv4
	 - AAAA: hostname to IPv6
     - CNAME: hostname to hostname (only work for non root domain e.g. works for mydomain.root.com but not for root.com)
- Alias: hostname to AWS resource (free of charge and has native health check)
- Web browser makes DNS request for myapp.domain.com to Route 53, and ot will send back IP for 	application server
- Can be use to
   - resolve public domain name
   - private domain name that can be resolved by your instance in your VPCs
- Support load balancing (client load balancing)	
- Support routing policies
- No free tier
- win : nslookup mydomain.com
- TTL (Time to live) Route 53 sends IP and TTL(e.g. 300s), so browser DNS cache IP for 300s only  
	- High TTL for less traffic on DNS
	 - Low TTL for high traffic on DNS
	 - Mandatory for each DNS record
 - Health checks
	 - Default health check interval 30s
	 - 15 health checkers will check the endpoint health
	 - One request / 2s
	 - Can have HTTP/TCP/HTTPS health check (no SSL)
	 - Can integrate with CloudWatch
	 - can be linked with DNS queries
- Routing Policy
   - Simple: use when need to redirect single resource, can health check, if multiple values are returned then random one is chosen by the client
   - Weighted: % of requests goes to specific endpoints, split traffic between regions
   - Latency: redirect to server that has least latency close to us, useful when latency is a priority, evaluated in terms of user location to designated AWS region
   - Failover: If health check of primary endpoint fails then DNS request redirected to disaster recovery endpoint
   - Geolocation: Route traffic to your resource based on user location with default redirect
   - Geoproximity: Route traffic to your resource based on the geographic locations of the users, has ability to shift more on defined bias values, need Route 53 Traffic flow (advanced)
   - Multi Value: Use when traffic to multi-resources with associated health checks, up to 8 healthy records are returned for each multi-value query, not a substitute for ELB
   - Route 53 also a Registrar which manage reserved internet domain (Domain name != Registrar)

### Route 53 Q&A	
Q: You have purchased "mycoolcompany.com" on the AWS registrar and would like for it to point to 
   `lb1-1234.us-east-2.elb.amazonaws.com` . What sort of Route 53 record is **NOT POSSIBLE** to set 
   up for this?
> CNAME (The DNS protocol does not allow you to create a CNAME record for the top node of a DNS
   namespace (mycoolcompany.com), also known as the zone apex)
	  
Q: You have deployed a new Elastic Beanstalk environment and would like to direct 5% of your  production traffic to this new environment, in order to monitor for CloudWatch metrics and  ensuring no bugs exist. What type of Route 53 records allows you to do so?
> Weighted  (Weighted allows you to redirect a part of the traffic based on a weight 
   (hence a percentage). It's common to use to send a part of a traffic to a new application 
   you're deploying
   
Q: After updating a Route 53 record to point "myapp.mydomain.com" from an old Load Balancer to a new load balancer, it looks like the users are still not redirected to your new load balancer. You are wondering why...
> Becuase of TTL (DNS records have a TTL (Time to Live) in order for clients to know for how long 
   to caches these values and not overload the DNS with DNS requests. TTL should be set to strike a
   balance between how long the value should be cached vs how much pressure should go on the DNS.)
   
Q: You want your users to get the best possible user experience and that means minimizing the response time from your servers to your users. Which routing policy will help?
> Latency (Latency will evaluate the latency results and help your users get a DNS response that 
   will minimize their latency (e.g. response time))
   	
Q: You have a legal requirement that people in any country but France should not be able to access  your website. Which Route 53 record helps you in achieving this?
> Geolocation

Q: You have purchased a domain on Godaddy and would like to use it with Route 53. What do you need to change to make this work?
> Create a public hosted zone and update the 3rd party registrar NS records (Private hosted 
   zones are meant to be used for internal network queries and are not publicly accessible. 
   Public Hosted Zones are meant to be used for people requesting your website through the 
   public internet. Finally, NS records must be updated on the 3rd party registrar.)

## CloudFront & AWS Global Accelerator
### [CloudFront](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/Introduction.html)
 - It is a fast content delivery network (CDN) service that securely delivers data, videos, applications, and APIs to customers globally with low latency, high transfer speeds, all within a developer-friendly environment
 - Speeds up distribution of your static and dynamic web content, such as .html, .css, .js, and image files, to your users
 - Delivers your content through a worldwide network of data centers called edge locations (216 Point of Presence globally)
 - CloudFront speeds up the distribution of your content by routing each user request through the AWS backbone network to the edge location that can best serve your content
 - DDoS protection, integration with shield, AWS Web Application firewall
 - Can expose external HTTPS and can talk to internal HTTPS backends
 - If the content is already in the edge location with the lowest latency, CloudFront delivers it immediately
 - If you are accessing content from USA and the content is not in that edge location, CloudFront retrieves it from an origin that you've defined—such as an Amazon S3 bucket in Australia, a MediaPackage channel, or an HTTP server that you have identified as the source for the definitive version of your content
 - Files are cached for TTL (At edge locations upon request)
 - Origins
	 - S3 as an Origin
		 - For distributing files and caching them at the edge
		 - Enhancing security with CloudFront Origin Access Identity (OAI)
		 - CloudFront can be used as an ingress (to upload file to S3)
		 - We can limit the S3 bucket to be accessed only using OAI
	 - Custom Origin (HTTP)
		 - ALB (must be public and SG must allowed CloudFront requests, here backend instance can be private)
		 - EC2 Instance (must be public and SG must allowed CloudFront requests)
		 - S3 website (must enable bucket as a static S3 website)
		 - Any HTTP backend you want
	 - ![AmazonCloudFront](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/images/how-cloudfront-delivers-content.png) 
 - GeoRestriction
	 - You can restrict who can access distribution
	 - Whitelist: allow access to content only if they're in one of the countries on a list of approved countries 
	 - Blacklist: prevent access to your content if they're in one of the countries on a list of banned countries
	 - The Country determined using 3rd party Geo-IP database
	 - Use case: Copyright laws to control access to content
 - Signed URL/ Signed Cookies
	 - When you want to distribute paid shared content to premium users
	 - We can sign URL/Cookie by attaching policy with
		 - URL expiration
		 - IP ranges to access data from
		 - Trusted signer
	 - URL validity for
		 - Shared content like movie or music - make it short
		 - Private content - make it last for years
	 - Signed URL
		 - Access to individual files
		 - One signed URL per file
		 - Allow access to a path, no matter the origin
		 - Account wide key-pair, only root can manage it
		 - Can filter by IP, path, date, expiration
		 - Can leverage caching feature 
		 - S3 pre-signed URL
			 - Issue a request as the person who pre-signed the URL
			 - Used IAM key of the signing principle
			 - Limited lifetime
	 - Signed Cookies
		 - Access to multiple files
		 - One singed cookie for many files
 - Pricing
	 - Cost of data per edge location varies
	 - You can reduce the number of edge locations for cost reduction
	 - 3 price classes
		 - Class All : All regions, best performance
		 - Class 200: Most regions, but exclude expensive 
		 - Class 100: Only least expensive regions
 - Multiple Origin
	 - Route to different kind of origins based on Content type and Path pattern (e.g. /images/*)
 - Origin group
	 - To increase high-availability
	 - Has one primary and one secondary origin
	 - If primary fails the second one is used
 - Field Level Encryption
	 - Protect used sensitive data trough application stack
	 - Adds an additional layers of security along with HTTPS
	 - Encrypted at the edge close to user
###  [Global Accelerator](https://docs.aws.amazon.com/global-accelerator/latest/dg/what-is-global-accelerator.html) 
- Service in which you create _accelerators_ to improve the performance of your applications for local and global users
- Standard accelerator 
	- You can improve availability of your internet applications that are used by a global audience
	- With a standard accelerator, Global Accelerator directs traffic over the AWS global network to endpoints in the nearest Region to the client
	- Provides you with two static IP addresses that you associate with your accelerator
- Custom routing accelerator
	- You can map one or more users to a specific destination among many destinations
- Unicast vs Anycast IP
	- Unicast 
		- One server holds one IP address
	- Anycast 
		- All server holds same IP address and the client routed to the nearest one
		- AGA used Anycast IP address 
		- 2 anycast IP created for your application
		- Anycast IP send traffic directly to Edge locations and Edge locations send traffic to your application
- Works with Elastic IP, EC2 instances, ALB, NLB
- Failover less than 1 min for unhealthy, great for disaster recovery
- Use cases
	- Non-HTTP use cases like gaming(UDP), IoT(MQTT) or Voice over IP
	- HTTP use cases that require static IP addresses
	- HTTP use cases that require deterministic, fast regional failover
- ![enter image description here](https://miro.medium.com/max/1200/1*1iQaCMjicPnfR7LsFZ2hrg.png)

### CloudFront & AWS Global Accelerator Q&N

Q: Which features allows us to distribute paid content from S3 securely, globally, if the S3 bucket is secured to only exchange data with CloudFront?
> CloudFront Signed URL (CloudFront Signed URL are commonly used to distribute paid content through dynamic CloudFront Signed URL generation)

Q: You are hosting highly dynamic content in Amazon S3 in us-east-1. Recently, there has been a need to make that data available with low latency in Singapore. What do you recommend using?
> S3 CRR (S3 CRR allows you to replicate the data from one bucket in a region to another bucket in another region)

Q: How can you ensure that only users who access our website through Canada are authorized in CloudFront?
> Use CloudFront Geo Restriction

Q: You would like to provide your users access to hundreds of private files in your CloudFront distribution, which is fronting an HTTP web server behind an application load balancer. What should you use?
> CloudFront Signed Cookies (Allows you to access many files)

Q: You are creating an application that is going to expose an HTTP REST API. There is a need to provide request routing rules at the HTTP level. Due to security requirements, your application can only be exposed through the use of two static IPs. How can you create a solution that validates these requirements?
> Use Global Accelerator and an Application Load Balancer (Global Accelerator will provide us with the two static IP, and the ALB will provide use with the HTTP routing rules)

Q:What does this S3 bucket policy do?

    {
	"Version": "2012-10-17",
	"Id": "Mystery policy",
	"Statement": [
		{
			"Sid": "What could it be?",
			"Effect": "Allow",
			"Principal": {
				"CanonicalUser": "{CloudFront Origin Identity Canonical User ID}"
			},
			"Action": "s3:GetObject",
			"Resource": "arn:aws:s3:::examplebucket/*"
		}
	  ]
    }
    
> Only allows the S3 bucket content to be accessed from your CloudFront distribution origin identity

## Decoupling applications
- Deploying multiple applications will need to communicate with each other and there are two types of patterns generally used for communication
- Synchronous communications (Application to Application)
- Asynchronous OR Event based communications (Application> Queue > Application)
- As application workload increases Synchronous communications become problematic, in that case its better to break monolith and decouple your applications and scale them independently, in such case Asynchronous communication model as mentioned below is good choice
	- SQS - queue model
	- SNS - pub/sub model
	- Kinesis - real-time streaming model	
###  [SQS](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/welcome.html)
- Offers a secure, durable, and available hosted queue that lets you integrate and decouple distributed software systems and components
- Supports both standard and FIFO queues
- **Feature**
	- Unlimited throughput, unlimited number of messages in queue
	- Default retention of messages 4 days, max 14 days
	- Low latency (< 10ms)
	- 256kb per message sent
	- Can have duplicate messages (at least once delivery)
	- Can have out of order messages (best effort ordering)
- **Producer**
	- Send message to SQS using the SDK (SendMessage API)
	- The message is persisted in SQS until a consumer deletes it
	- Order will be sent in message (like OrderId) and consumer will take care of ordering
- **Consumer**
	- Poll SQS for messages (receive up to 10 messages at a time)
	- Process the message
	- Consumers can be running on EC2 instances, servers or AWS Lambda
	- Delete message using DeleteMessage API
	- *Multiple EC2 instance Consumers*
		- Receive and process messages in parallel
		- At least one delivery
		- Best effort message ordering
		- Delete message after processing
- **Scaling**
	- We can scale consumers horizontally to improve throughput of processing
	- ASG of EC2 can launch or terminate instance depending on number of messages in SQS![enter image description here](https://docs.aws.amazon.com/autoscaling/ec2/userguide/images/sqs-as-custom-metric-diagram.png)
- **Security**
	- *Encryption*
		- In-flight using HTTPS API
		- At-rest using KMS keys
		- Client-side encryption
	- *Access controls* - using IAM policies 
	- [Access policies](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-basic-examples-of-sqs-policies.html) 
		- Similar to S3 bucket policies
		- Cross Account Access - create queue account policy
		- Publish S3 Event Notifications to SQS queue - create queue account policy with source bucket condition (json)
```javascript	
// Cross Account Access
{
   "Version": "2012-10-17",
   "Id": "Queue1_Policy_UUID",
   "Statement": [{
      "Sid":"Queue1_AllActions",
      "Effect": "Allow",
      "Principal": {
         "AWS": [
            "arn:aws:iam::111122223333:role/role1",
            "arn:aws:iam::111122223333:user/username1"
         ]
      },
      "Action": "sqs:*",
      "Resource": "arn:aws:sqs:us-east-2:123456789012:queue1"
   }]
}	
// Publish S3 Event Notifications to SQS queue
{
   "Version": "2012-10-17",
   "Id": "example-ID",
   "Statement": [{
	  "Sid": "example-statement-ID",
      "Effect": "Allow",
      "Principal": {"AWS": "*" },
      "Action": "SQS:SendMessage",
      "Resource": "arn:aws:sqs:us-east-2:123456789012:queue1"
      "Condition": {
	      "ArnLike": { "aws:SourceArn":"arn:aws:s3:*:*:bucket1"},
	      "StringEquals": { "aws:SourceAcount":"<Bucket-Owner-Account-Id>" }
      }
   }]
}
```
-	**Message visibility timeout**
	-	Once message read by consumer it will invisible to other consumer (for 30 secs)
	-	That means the message has 30 secs to get processed
	-	Else it will be available after visibility timeout expired
	-	If message is not processed within visibility timeout, it will process twice
	-	Consumer can call the *ChangeMessageVisibility* API to get more time so it will be no visible soon
-	**Dead Letter Queue (DLQ)**
	-	We can set a threshold of how many times a message can go back to the queue
	-	After the *MaximumReceives* threshold is exceed, the message goes to into the DLQ
	-	Helpful for debugging
	-	Good to set retention period of 14+ days
-	![enter image description here](https://funnelgarden.com/wp-content/uploads/2020/01/AWS-SQS-Simple-Queue-Service-1024x379.png =800x300)
-	[Request Response Pattern (virtual queues)](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-temporary-queues.html#request-reply-messaging-pattern)
	-	Temporary queues help you save development time and deployment costs when using common message patterns such as _request-response_
	-	Most common use case for temporary queues is the _request-response_ messaging pattern
	-	Requester creates a _temporary queue_ for receiving each response message
	-	Temporary Queue Client lets you create and delete multiple temporary queues without making any Amazon SQS API calls
- **Delay Queues**
	- Delay message up to 15 mins
	- Default is 0 secs
	- Can set default at queue level
	- Can override default on send by using *DelaySeconds* parameter in message
- **FIFO**
	- Ordering of message is preserved
	- Limited throughput (300 msg/s without batching, 3000 msg/s with batching)
	- Exactly-once send capability
	- Message are processed in order by consumer
###  [SNS (Simple Notification Service)](https://docs.aws.amazon.com/sns/latest/dg/welcome.html)
- Sends one message to many receivers 
- Pub/Sub model
- The *"event producer"* only sends message to one SNS topic
- Many *"event receivers"* (subscriptions) as we want to listen to the SNS topic notifications
- Each subscriber to the topic will get all the messages
- Up tp 10,000,000 subscriptions per topic
- 100,000 topics limit![enter image description here](https://d1.awsstatic.com/diagrams/Product-page-diagram-Amazon-SNS_event-driven-SNS-compute@2X_.4b9c0a75aa40bda9cdb12f0176930a12da2872bf.png)
- **Subscribers can be**
	- SQS
	- HTTP/HTTPS
	- Lambda
	- Emails
	- SMS messages
	- Mobile notifications
- **Integrates with**
	- Many AWS services can send data directly to SNS for notifications
	- CloudWatch sends alarm notifications to SNS
	- Auto Scaling Groups notifications 
	- S3 bucket events
	- CloudFormation (upon state change)
- **How to publish?**
	- *Topic publish*
		- Create a topic
		- Create a subscription(s)
		- Publish to the topic
	- *Direct publish (for mobile apps SDK)*
		- Create platform application
		- Create platform endpoint
		- Publish to the platform endpoint
		- Works with Google GCM, Apple APNS, Amazon ADM etc. 
- **Security**
	- *Encryption*
		- In-flight using HTTPS API
		- At-rest using KMS keys
		- Client-side encryption
	- *Access controls* - using IAM policies to regulate access to the SNS API
	- *Access policies*
		- Similar to S3 bucket policies
		- Cross Account Access to SNS topic
		- Allowing other service (e.g. S3) to write to an SNS topic
- **SNS + SQS Fan Out**
	- Push once in SNS, receive in all SQS queues that are subscribers
	- Fully decoupled, no data loss
	- Ability to add more SQS subscribers over time
	- Make sure your SQS queue **access policy** allows for SNS to write
	-  Video transcoding fanout implementation in Lambda (Send same S3  events to many SQS queues / Lambda functions)![enter image description here](https://d2908q01vomqb2.cloudfront.net/1b6453892473a467d07372d45eb05abc2031647a/2017/07/25/messaging-fanout-for-serverless-with-sns-diagram2.png)
	- **SNS FIFO**
		- Ordering of messages in the topic
		- Similar feature as SQS FIFO
		- *Can only have SQS FIFO as subscribers*
		- Limited throughput
		- SNS FIFO + SQS FIFO - Fan out for scenario where you need *fan out + ordering + deduplication*![enter image description here](https://d2908q01vomqb2.cloudfront.net/da4b9237bacccdf19c0760cab7aec4a8359010b0/2020/07/07/sns_fifo_two_subscriptions-1260x520.png)
	- **Message Filtering**
		- JSON policy used to filter messages sent to SNS topic's subscriptions
		- If subscription doesn't have filter policy, it receive every message![enter image description here](https://event-driven-architecture.workshop.aws/4-sns/2-filtering/images/sns_arch_filtering.png?classes=border,shadow)
### Kinesis
- Collect, process and analyze streaming data in real-time
- Ingest real time data such as Application logs, Metrics, Website clickstreams, IoT telemetry data
- [Kinesis Data Streams](https://docs.aws.amazon.com/streams/latest/dev/introduction.html)
	- Collect and process large [streams](https://aws.amazon.com/streaming-data/) of data records in real time
	- A typical Kinesis Data Streams application reads data from a _data stream_ as data records
	- Streams are made up of Shards
	- Each shard allows for 1MB/s incoming and 2MB/s outgoing of data
	- Producer sends data as Records into the stream
	- Once data inserted in Kinesis, it can't be deleted
	- Data that shares same partition goes to the same shard
	- Producers - AWS SDK, Kinesis Producer Library (KPL), Kinesis Agent
	- Consumers - AWS Lambda, Kinesis Data Firehose etc.![enter image description here](https://i.pinimg.com/originals/6d/aa/21/6daa21bd6c878525a5f4c82e5a79d8ff.jpg)
	- Feature 
		- Streaming service for ingest at scale
		- Billing per shard provisioned, can have as many shard as you want
		- Real time (~ 200 ms)
		- Manage scaling (Shard splitting/merging)
		- Retention between 1 day (default) to 365 days
		- Support replay capability
- [Kinesis Data Firehose](https://docs.aws.amazon.com/firehose/latest/dev/what-is-this-service.html)
	- Service for delivering real-time [streaming data](http://aws.amazon.com/streaming-data/) to destinations such as S3, Amazon Redshift, Amazon Elasticsearch Service etc.
	- Feature 
		- Fully managed
		- Load data streams into S3/RedShift/3rd Party/Custom HTTP
		- Pay for data going through Firehose
		- Near real time (60 sec latency OR minimum 32MB data at a time)
		- Automatic scaling
		- No data storage
		- Support many data formats, conversions, transformations, compression
		- Support custom data transformations using AWS Lambda
		- Can send failed or all data to backup S3 bucket
		- Doesn't support replay capability 
		- ![enter image description here](https://i.pinimg.com/originals/83/68/c6/8368c679d78d6d1aeab7750a3da7ce59.jpg)
- [Kinesis Data Analytics](https://docs.aws.amazon.com/kinesisanalytics/latest/dev/what-is.html)
	- Analyze data streams with SQL or Apache Flink
	- Feature 
		- Fully managed, no servers to provision
		- Automatic scaling
		- Real-time analytics
		- Pay for actual consumption rate
		- Can create streams out of the real time queries
	- Use Cases
		- Time-series analytics
		- Real-time dashboards
		- Real-time metrics![enter image description here](https://d2908q01vomqb2.cloudfront.net/b6692ea5df920cad691c20319a6fffd7a4a766b8/2017/10/05/preprocessing-data-kinesis-1.gif)
- [Kinesis Video Streams](https://docs.aws.amazon.com/kinesisvideostreams/latest/dg/what-is-kinesis-video.html)
	- Use to stream live video from devices to the AWS Cloud, or build applications for real-time video processing
	- Capture, process and store video streams
### Data ordering for Kinesis vs SQS FIFO
- *Kinesis*
	- Send data using Partition Key
	- Same key will always go to the same shard (due to hashing)
- *SQS FIFO*
	- No ordering
	- If you don't use Group ID, messages are consumed in the order they send, with only one consumer
- Lets assume 100 records, 5 kinesis shard, 1 SQS FIFO
	- *Kinesis data streams*
		- 20 records per shard
		- Will have their data ordered within each shard
		- Maximum amount of consumer in parallel is 5
		- Can receive up to 5MB/s (1MB/s * Total Shards) of data
	- *SQS FIFO*
		- Will have 100 group ID
		- So can have 100 Consumers (due to 100 group ID)
		- Can have up to 300 messages per second (or 3000 if using batching)![enter image description here](https://miro.medium.com/max/1714/1*GPXkMjBqByYN3eD30FhsHw.png)
###  [MQ](https://docs.aws.amazon.com/amazon-mq/latest/developer-guide/welcome.html)
- Managed message broker service that makes it easy to migrate to a message broker in the cloud
- SQS, SNS are *cloud native* services, and they're using proprietary protocols from AWS
- Traditional applications running from on-premise may use open protocols such as MQTT, AMQP, OpenWire etc.
- When migrating to the cloud, instead of re-engineering the application to use SQL, SNS we can use MQ
- Currently, Amazon MQ supports [Apache ActiveMQ](http://activemq.apache.org/) and [RabbitMQ](https://www.rabbitmq.com/) engine types
- Does not scale as much as SQS/SNS
- Runs on dedicated machine
- Has both **queue feature and topic feature**![enter image description here](https://d1.awsstatic.com/product-marketing/Amazon-MQ/Amazon%20MQ%20HIW%20Diagram.78e380e8a97064c8f751c1569481a304644490b5.jpg)

### Decoupling applications Q&A
Q: You are preparing for the biggest day of sale of the year, where your traffic will increase by 100x. You have already setup SQS standard queue. What should you do?
> 	Do nothing, SQS scales automatically

Q: You would like messages to be processed by SQS consumers only after 5 minutes of being published to SQS. What should you do?
>	Increase the DelaySeconds parameter (Delay queues let you postpone the delivery of new messages to a queue for a number of seconds. If you create a delay queue, any messages that you send to the queue remain invisible to consumers for the duration of the delay period. The default (minimum) delay for a queue is 0 seconds. The maximum is 15 minutes)

Q: Your consumers poll 10 messages at a time and finish processing them in 1 minute. You notice that your messages are processed twice, as other consumers also receive the messages. What should you do?
> Increase the VisibilityTimeout (Immediately after a message is received, it remains in the queue. To prevent other consumers from processing the message again, Amazon SQS sets a visibility timeout, a period of time during which Amazon SQS prevents other consumers from receiving and processing the message. Increasing the timeout gives more time to the consumer to process that message and will prevent duplicate readings of the message)

Q: You'd like your messages to be processed exactly once and in order. Which do you need?
> FIFO (First-In-First-Out) queues are designed to enhance messaging between applications when the order of operations and events is critical, or where duplicates can't be tolerated. FIFO queues also provide exactly-once processing but have a limited number of transactions per second (TPS).

Q: You'd like to send a message to 3 different applications all using SQS. You should
> Use SNS + SQS Fan Out pattern (This is a common pattern as only one message is sent to SNS and then "fan out" to multiple SQS queues)

Q: You have a Kinesis stream usually receiving 5MB/s of data and sending out 8 MB/s of data. You have provisioned 6 shards. Some days, your traffic spikes up to 2 times and you get a throughput exception. You should
> Add more shards (Each shard allows for 1MB/s incoming and 2MB/s outgoing of data)

Q: You are sending a clickstream for your users navigating your website, all the way to Kinesis. It seems that the users data is not ordered in Kinesis, and the data for one individual user is spread across many shards. How to fix that problem?
> You should use a partition key that represents the identity of the user (By providing a partition key we ensure the data is ordered for our users)

Q: We'd like to perform real time analytics on streams of data. The most appropriate product will be
> Kinesis (Kinesis Analytics is the product to use, with Kinesis Streams as the underlying source of data)

Q: We'd like for our big data to be loaded near real time to S3 or Redshift. We'd like to convert the data along the way. What should we use?
> Kinesis Streams + Kinesis Firehose (This is a perfect combo of technology for loading data near real-time in S3 and Redshift)

Q: You want to send email notifications to your users. You should use
> SNS

Q: You have many microservices running on-premise and they currently communicate using a message broker that supports the MQTT protocol. You would like to migrate these applications and the message broker to the cloud without changing the application logic. Which technology allows you to get a managed message broker that supports the MQTT protocol?
> Amazon MQ (Supports JMS, NMS, AMQP, STOMP, MQTT, and WebSocket)


## Container on AWS
### Docker
- Is a software platform that allows you to build, test, and deploy applications quickly
- With Docker, you can manage your infrastructure in the same ways you manage your applications
- Provides the ability to package and run an application in a loosely isolated environment called a container, that can be run on any OS
- The isolation and security allow you to run many containers simultaneously on a given host
- Containers are lightweight and contain everything needed to run the application, so you do not need to rely on what is currently installed on the host
- **Uses a client-server architecture** ![enter image description here](https://docs.docker.com/engine/images/architecture.svg)
- Docker _client_ talks to the Docker _daemon_, which does the heavy lifting of building, running, and distributing your Docker containers
- The Docker client and daemon _can_ run on the same system, or you can connect a Docker client to a remote Docker daemon
- The Docker client and daemon communicate using a REST API, over UNIX sockets or a network interface
- Another Docker client is Docker Compose, that lets you work with applications consisting of a set of containers
	 - **daemon**
		 - daemon (`dockerd`) listens for Docker API requests and manages Docker objects such as images, containers, networks, and volumes
		 - Can also communicate with other daemons to manage Docker services
	 - **client**
		 - client (`docker`) is the primary way that many Docker users interact with Docker
		 - When you use commands such as `docker run`, the client sends these commands to `dockerd`, which carries them out
		 - The `docker` command uses the Docker API. The Docker client can communicate with more than one daemon
	 - **registries**
		 - Stores Docker images
		 - Docker Hub is a public registry that anyone can use, and Docker is configured to look for images on Docker Hub by default and you can even run your own private registry
	 - **objects**
		 - *Images*
			 - Read-only template with instructions for creating a Docker container
			 - Often, an image is _based on_ another image, with some additional customization. For example, you may build an image which is based on the `ubuntu` image, but installs the Apache web server and your application, as well as the configuration details needed to make your application run
			 - To build your own image, you create a _Dockerfile_ with a simple syntax for defining the steps needed to create the image and run it
			 - Each instruction in a Dockerfile creates a layer in the image. When you change the Dockerfile and rebuild the image, only those layers which have changed are rebuilt
		 - *Containers*
			 - Runnable instance of an image
			 - Can create, start, stop, move, or delete a container using the Docker API or CLI
			 - Can connect a container to one or more networks, attach storage to it, or even create a new image based on its current state
			 - By default, a container is relatively well isolated from other containers and its host machine
###  [ECS (Elastic Container Service) ](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/Welcome.html)
- Enables you to launch and stop your container-based applications by using simple API calls
- Your containers are defined in a task definition that you use to run individual tasks or tasks within a service
- You must provision & maintain the infrastructure (EC2 instances)
- ECS will take care of starting/stopping containers
- Has integration with ALB ![enter image description here](https://raw.githubusercontent.com/nikxsh/aws/master/diagrams/aws-ecs-cluster.png)
- [ECS Tasks](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task_definitions.html)
	- To prepare your application to run on Amazon ECS, you create a task definition. 
	- The task definition is a text file, in JSON format, that describes one or more containers, up to a maximum of ten, that form your application
	- Task definitions specify various parameters for your application such as
		- Which containers to use
		- Which launch type to use
		- Which ports should be opened for your application
		- What data volumes should be used with the containers in the task
	- [IAM Roles for ECS Tasks](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task-iam-roles.html)
		- All ECS task may perform operations on other services so they need to have IAM roles
		- ECS agent need role *EC2 Instance Profile (only used by ECS agent)* to do
			- API calls to ECS service
			- Send contained logs to CloudWatch Logs
			- Pull Docker image from ECR
			- Reference to Secret Manager or Parameter Store
		- [ECS Task Role](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task-iam-roles.html)
			- Attached to Tasks to each task can have specific role
			- Use different roles for different ECS services you run
			- Defined in Task Definition
			- E.g. ECS Task 1 has "IAM Role 1" to access S3 bucket then only that task can access S3 bucket![enter image description here](https://image.slidesharecdn.com/1715-1745securingyourcontainersonaws-170503044946/95/securing-your-containers-on-aws-25-638.jpg?cb=1493788400)
- Launch Types - can be specified when running a standalone task or creating a service to determine the infrastructure on which your tasks and services are hosted
	- *EC2 launch type*
		- Can be used to run your containerized applications on Amazon EC2 instances that you register to your Amazon ECS cluster and manage yourself
		- EC2 agents will be running on all container instances, through agents all instances get registered in ECS cluster so that in can be useful to start/stop ECS tasks![enter image description here](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/images/overview-standard.png)
		-  Load Balancing
			- We get dynamic port mapping
			- The ALB supports finding the right port on your EC2 instances
			- You must allow on the EC2 instance's SG "any port" coming from the ALB SG![enter image description here](https://i.pinimg.com/originals/f7/d5/43/f7d54362a8270c2c144bd84e47175d09.jpg)
	- *Fargate launch type*
		- Fargate service will launch a Task and not need to create EC2 instances beforehand
		- Has ENI to connect to each Fargate Tasks which also get launched within our VPC to bind these task to network IP
		- As ENI is distinct IP we should have enough private address within our VPC![enter image description here](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/images/overview-fargate.png)
		- Load Balancing
			- Each task has a unique IP
			- Ensure that ENI's SG is allows on the "task port" the SG of ALB ![enter image description here](https://i.pinimg.com/originals/d4/e6/4f/d4e64ff853f15bc039a5187223198d15.jpg)
- [ECS Data Volumes](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/using_data_volumes.html)
	- [EFS volumes](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/efs-volumes.html)
		- Works for both EC2 tasks and Fargate tasks
		- Ability to mount EFS volumes onto tasks
		- Task launched in any AZ will be able to share the same data in the EFS volume
		- Fargate + EFS = serverless + data storage without managing servers
		- Use cases
			- Persistent Multi-AZ shared storage for your containers
			- To export file system data across your fleet of container instances. That way, your tasks have access to the same persistent storage, no matter the instance on which they land
	- [Docker volumes](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/docker-volumes.html)
		- When using Docker volumes, the built-in `local` driver or a third-party volume driver can be used
		- Docker volumes are managed by Docker and a directory is created in `/var/lib/docker/volumes` on the container instance that contains the volume data
		- Use cases
			- To provide persistent data volumes for use with containers
			- To share a defined data volume at different locations on different containers on the same container instance
	- [Bind mounts](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/bind-mounts.html)
		- A file or directory on the host, such as AWS Fargate, is mounted into a container
		- Bind mounts are tied to the lifecycle of the container
		- Once all containers using a bind mount are stopped, for example when a task is stopped, the data is removed
		- Use cases
			- To provide an empty data volume to mount in one or more containers
			- To share a data volume from a source container with other containers in the same task
			- To expose a path and its contents from a Dockerfile to one or more containers
- ECS Task invoked by [Event Bridge](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-what-is.html)
	- EventBridge delivers a stream of real-time data from your applications, software as a service (SaaS) applications, and AWS services to targets such as AWS Lambda functions, HTTP invocation endpoints using API destinations, or event buses in other AWS accounts
	- EventBridge receives an _[event](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-events.html)_, an indicator of a change in environment, and applies a [rule](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-rules.html) to route the event to a [target](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-targets.html)
	- Rules match events to targets based on either the structure of the event, called an _[event pattern](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-event-patterns.html)_, or on a schedule 
	- E.g. We can invoke ECS task upon object is uploaded, using EventBridge Rule and events![enter image description here](https://i.pinimg.com/originals/0f/46/44/0f4644ef4790af0720e506b6c1dda849.jpg)
	- [ECS Scaling](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/service-auto-scaling.html)
		- Is the ability to increase or decrease the desired count of tasks in your Amazon ECS service automatically
		- Service Scaling
			- Amazon ECS publishes CloudWatch metrics with your service’s average CPU and memory usage
			- You can use these and other CloudWatch metrics to scale out your service (add more tasks) to deal with high demand at peak times, and to scale in your service (run fewer tasks) to reduce costs during periods of low utilization ![enter image description here](https://s3.amazonaws.com/chrisb/concept_diagram.jpg)
			- [Scale ECS Capacity Providers (Optional)](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/cluster-capacity-providers.html)
				- Each cluster can have one or more capacity providers and an optional default capacity provider strategy
				- The capacity provider strategy determines how the tasks are spread across the cluster's capacity providers
				- Optional and for **EC2 type only**
				- It will launch additional EC2 instances to cluster
		- ECS Rolling Updates
			- When the _rolling update_ (`ECS`) deployment type is used for your service, when a new service deployment is started the Amazon ECS service scheduler replaces the currently running tasks with new tasks
			- The _rolling update_ is controlled by the deployment configuration
			- The deployment configuration consists of the `minimumHealthyPercent` and `maximumPercent` values which are defined when the service is created, but can also be updated on an existing service
			- The `minimumHealthyPercent` represents the lower limit on the number of tasks that should be running for a service during a deployment or when a container instance is draining, as a percent of the desired number of tasks for the service
			- The `maximumPercent` represents the upper limit on the number of tasks that should be running for a service during a deployment or when a container instance is draining, as a percent of the desired number of tasks for a service
			- When updating service from v1 to v2, we can control how many tasks can be started and stopped, and in which order![enter image description here](https://raw.githubusercontent.com/nikxsh/aws/master/diagrams/aws-ecs-service-rolling-updates.png)
###  [Fargate](https://docs.aws.amazon.com/eks/latest/userguide/fargate.html)
- Serverless container platform
- Launch Docker containers on AWS
- You no longer have to provision, configure, or scale groups of virtual machines to run containers (No EC2 instances to manage), that why serverless
- AWS just runs containers for you based on the CPU/RAM you need![enter image description here](https://d1.awsstatic.com/re19/FargateonEKS/Product-Page-Diagram_Fargate@2x.a20fb2b15c2aebeda3a44dbbb0b10b82fb89aa6a.png)
###  [EKS (Elastic Kubernetes Service)](https://docs.aws.amazon.com/eks/latest/userguide/what-is-eks.html)
- Managed Kubernetes
- It is a way to launch managed Kubernetes cluster on AWS
- [Kubernetes](https://kubernetes.io/docs/concepts/overview/what-is-kubernetes/), also known as K8s, is an open-source system for automating deployment, scaling, and management of containerized applications
- Alternative to ECS, similar goal but different API (Kubernetes is open source)
- EKS supports EC2 if you want to deploy worker nodes or Fargate to deploy serverless containers
- Cloud-agnostic i.e. can be used on any cloud ![enter image description here](https://d1.awsstatic.com/partner-network/QuickStart/datasheets/amazon-eks-on-aws-architecture-diagram.64cf0e40c45ade8107daf6a3ef5e2e05134d9a4b.png)
- Use Cases
	- Organization already using Kubernetes
	- Organization wants to migrate to AWS using Kubernetes
###  [ECR (Elastic Container Registry)](https://docs.aws.amazon.com/AmazonECR/latest/userguide/what-is-ecr.html)
- An AWS managed container image registry service that is secure, scalable, and reliable
- Supports private container image repositories with resource-based permissions using AWS IAM
- *ECR registry* is provided to each AWS account; you can create image repositories in your registry and store images in them
- *Authorization token* - Your client must authenticate to Amazon ECR registries as an AWS user before it can push and pull images
- An Amazon *ECR image repository* contains your Docker images, Open Container Initiative (OCI) images, and OCI compatible artifacts
- You can control access to your repositories and the images within them with *repository policies*
- You can push and pull *container images* to your repositories
- Supports image vulnerability scanning, version, tag, image lifecycle ![enter image description here](https://d1.awsstatic.com/diagrams/product-page-diagrams/Product-Page-Diagram_Amazon-ECR.bf2e7a03447ed3aba97a70e5f4aead46a5e04547.png)
			 
			 

## Serverless in AWS
 - A [serverless architecture](https://aws.amazon.com/lambda/serverless-architectures-learn-more/) is a way to build and run applications and services without having to manage infrastructure
 - Application still runs on servers, but all the server management is done by AWS
 - Just deploy code (Function as Service FaaS)
 - Use cases
	- Build web applications and mobile backends in a faster, more agile way
	- Can use cloud services like AWS Lambda, Amazon API Gateway, and Amazon DynamoDB to implement serverless architectural patterns that reduce the operational complexity of running and managing applications ![enter image description here](https://docs.aws.amazon.com/whitepapers/latest/microservices-on-aws/images/image4.png)
 - Serverless in AWS
	- Lambda
	- DynamoDB
	- Cognito
	- API Gateway
	- S3
	- SNS & SQS
	- Kinesis Data Firehouse (Getting scaled based on throughput)
	- Aurora Serverless
	- Step Functions
	- Fargate (Serverless function in ECS)
- SAM (Serverless Application Model)
	- Framework for developing and deploying serverless applications
	- All the configuration is YAML code and you can configure
		- Lambda functions
		- DynamoDB tables
		- API Gateway
		- Cognito User Pools
	- Can help you to run Lambda, API Gateway, DynamoDB locally
	- Can use CodeDeploy to deploy Lambda functions
###  [Lambda](https://docs.aws.amazon.com/lambda/latest/dg/welcome.html)
 - Virtual Functions - service that lets you run code without provisioning or managing servers
 - Runs on demand - runs your function only when needed
 - You pay only for per request & compute time
 - Performs all of the administration of the compute resources, like
	- Server and operating system maintenance
	- Capacity provisioning
	- Automatic scaling
	- Code monitoring and logging
 - Can invoke your Lambda functions using
	- Lambda API
	- In response to events from other AWS services
 - Language Support
	- Node.js, Python, Java, C#, Golang, PowerShell, Ruby & Custom Runtime API (community supported e.g. Rust)
	- Lambda Container Image
		- Must be implement the Lambda Runtime API
		- ECS / Fargate is preferred for running arbitrary Docker images
 - Examples
	- Serverless thumbnail creation![Serverless thumbnail creation](https://blog.kakaocdn.net/dn/cLsDbd/btqZrfTcmN3/IZZdwYV5ssrNPb7KMeDXQK/img.png)
	- Serverless CRON Job
![Serverless CRON Job](https://d2908q01vomqb2.cloudfront.net/fc074d501302eb2b93e2554793fcaf50b3bf7291/2018/05/13/architecture_v2small2.png)
 - [Pricing](https://aws.amazon.com/lambda/pricing/)
	- Pay per calls
		- Free usage tier includes 1M free requests per month
		- $0.20 per 1 million requests thereafter ($0.0000002/request)
	- Pay per duration
		- Free usage tier includes 400,000 GB-seconds of compute time per month
		- == 400,000 secs if function is 1GB RAM
		- == 3,200,000 secs if function is 128 MB RAM
		- After that $1 for 600,000 GB-secs
	- Very cheap to run AWS lambda
 - Limits (per region)
	- Execution limit
		- Memory 128MB - 10 GB (64MB increments)
		- Max execution time 900 sec (15 mins)
		- Max size for environment variables 4KB
		- Disk capacity in the function container is 512 MB
		- Concurrent execution 1000 (can be increase)
	- Deployment limit
		- Max deployment size is 50 MB (Compressed)
		- Max size of uncompressed deployment (Code + Dependencies) is 250 MB
		- Can use the /tmp directory to load other files at startup
		- Max size for environment variables 4KB 
 - Lambda@Edge
	- Deploy Lambda function alongside your CloudFront CDN to
		- Build more responsive applications
		- Customized CDN content
		- Lambda deployed globally
		- Pay only for what you use
	- You can use it to change CloudFront requests and responses![enter image description here](https://docs.aws.amazon.com/lambda/latest/dg/images/cloudfront-events-that-trigger-lambda-functions.png)
	- Allows you to change Viewer request/response and Origin request/response
	- Use cases
		- Website security and privacy
		- Dynamic web application at the edge
		- Search engine optimization (SEO)
		- Intelligently Route Across origin and Data Centers
		- Bot Mitigation at the edge
		- Real-time Image Transformation
		- User Authentication and Authorization
		- User tracking and Analytics
###  [DynamoDB](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Introduction.html)
 - Fully managed NoSQL database service
 - AWS proprietary
 - **Features**
	- Highly Available with replication across 3 AZ
	- Scales massive workloads, distributed database
	- Millions of requests per seconds, trillions of row, 100s of TB of storage
	- Low latency retrieval
	- Integrated with IAM
	- Enables event driven programming with Streams
	- Encryption at rest
	- On-demand backup and point-in-time recovery (you can restore a table to any point in time during the last 35 days)
	- Delete expired items from tables automatically
	- Low cost and auto scaling capabilities
	- Amazon DMS can be used to migrate to DynamoDB from Mongo, Oracle, MySQL, S3 etc.
	- Can query only on primary key, sort key or indexes
 - [Read/Write Capacity Mode](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/HowItWorks.ReadWriteCapacityMode.html)
	- On-Demand Mode
		- No capacity planning needed (RCU/WCU)
		- Scales Automatically
		- 2.5x more expensive than provisioned mode
		- On-demand mode is a good option if any of the following are true
			- You create new tables with unknown workloads
			- You have unpredictable application traffic
			- You prefer the ease of paying for only what you use
	- Provisioned Mode
		- You specify the RCU/WCU that you require for your application
		- You can use auto scaling to adjust your table’s provisioned capacity automatically in response to traffic changes
		- Provisioned mode is a good option if any of the following are true:
			- You have predictable application traffic
			- You run applications whose traffic is consistent or ramps gradually
			- You can forecast capacity requirements to control costs
		- For provisioned mode tables, you specify throughput capacity
	- [Throughput capacity](https://aws.amazon.com/dynamodb/pricing/provisioned/)
		- Table must have provisioned read and write capacity units
		- Read Capacity Units (RCU)
			- Throughput for Reads ($0.00013 per RCU)
			- 1 RCU = 1 strongly consistent read of 4KB/s
			- 1 RCU = 2 eventual consistent read of 4KB/s
			- Items larger than 4 KB require additional RCUs
			- _Transactional_ read requests require two RCUs to perform one read per second for items up to 4 KB
				- A strongly consistent read of an 8 KB item would require two RCUs
				- An eventually consistent read of an 8 KB item would require one RCU
				- A transactional read of an 8 KB item would require four RCUs
		- Write Capacity Units (WCU)
			- Throughput for Writes ($0.00065 per WCU)
			- 1 WCU = 1 write of 1KB/s
			- Items larger than 1 KB require additional WCUs
			- _Transactional_ write requests require two WCUs to perform one write per second for items up to 1 KB. 
				- A standard write request of a 1 KB item would require one WCU
				- A standard write request of a 3 KB item would require three WCUs
				- A transactional write request of a 3 KB item would require six WCUs
		- Replicated write capacity unit (rWCU)
			- When using DynamoDB global tables
			- Data is written automatically to multiple AWS Regions of your choice
			- Each write occurs in the local Region as well as the replicated Regions
		- Streams read request unit
			- Each GetRecords API call to DynamoDB Streams is a streams read request unit
			- Each streams read request unit can return up to 1 MB of data
		- Throughput can be exceeded using "burst credits" and if "burst credits" are empty then you'll get "ProvisionThroughputException"
 - **Structure**
	- A _table_ is a collection of data. For example, see the example table called _People_ that you could use to store personal contact information about friends, family, or anyone else of interest
	- An _item_ is a group of attributes that is uniquely identifiable among all of the other items. In a _People_ table, each item represents a person
	- An _attribute_ is a fundamental data element, something that does not need to be broken down any further. For example, an item in a _People_ table contains attributes called _PersonID_, _LastName_, _FirstName_, and so on
	- ![enter image description here](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/HowItWorksPeople.png)![enter image description here](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/HowItWorksMusic.png)
	- the _People_ table
		- The primary key consists of one attribute (_PersonID_)
		- Other than the primary key, the _People_ table is schemaless
		- Most of the attributes are _scalar_, which means that they can have only one value
		- Some of the items have a nested attribute (_Address_)
	- the _Music_ table
		- The primary key for _Music_ consists of two attributes (_Artist_ and _SongTitle_)
		- One of the items has a nested attribute (_PromotionInfo_), which contains other nested attributes
	- *DynamoDB supports nested attributes up to 32 levels deep*
	- **Primary Key**
		- DynamoDB supports two different kinds of primary keys
		- **Partition key** 
			- A simple primary key, composed of one attribute known as the _partition key_ (_PersonID_)
			- The partition key of an item is also known as its _hash attribute_
			- DynamoDB uses the partition key's value as input to an internal hash function. The output from the hash function determines the partition (physical storage internal to DynamoDB) in which the item will be stored
			- In a table that has only a partition key, no two items can have the same partition key value
		- **Partition key and sort key**
			- _composite primary key_, this type of key is composed of two attributes. The first attribute is the _partition key_, and the second attribute is the _sort key_ (_Artist_ and _SongTitle_)
			- The sort key of an item is also known as its _range attribute_
			- All items with the same partition key value are stored together, in sorted order by sort key value
			- In a table that has a partition key and a sort key, it's possible for two items to have the same partition key value. However, those two items must have different sort key values
	- [Secondary Indexes](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/SecondaryIndexes.html)
		- Lets you query the data in the table using an alternate key, in addition to queries against the primary key
		- Global secondary index
			- An index with a partition key and sort key that can be different from those on the table
			- 20 global secondary indexes (default quota) per table
		- Local secondary index
			- An index that has the same partition key as the table, but a different sort key
			- 5 local secondary indexes per table![enter image description here](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/HowItWorksGenreAlbumTitle.png)
		- _GenreAlbumTitle_ index
			- _Music_ table, with a new index called _GenreAlbumTitle_
			- In the index, _Genre_ is the partition key and _AlbumTitle_ is the sort key
			- _Music_ is the base table for the _GenreAlbumTitle_ index
			- Query the data by _Genre_ and _AlbumTitle_
			- DynamoDB maintains indexes automatically. 
			- When you add, update, or delete an item in the base table, DynamoDB adds, updates, or deletes the corresponding item in any indexes that belong to that table
			- You can query the _GenreAlbumTitle_ index to find all albums of a particular genre (for example, all _Rock_ albums). You can also query the index to find all albums within a particular genre that have certain album titles (for example, all _Country_ albums with titles that start with the letter H)
 - **Streams**
	- Optional feature that captures data modification events in DynamoDB tables
	- Each event is represented by a _stream record_
	- If you enable a stream on a table, DynamoDB Streams writes a stream record whenever one of the following events occurs
		- A new item is added to the table: The stream captures an image of the entire item, including all of its attributes
		- An item is updated: The stream captures the "before" and "after" image of any attributes that were modified in the item
		- An item is deleted from the table: The stream captures an image of the entire item before it was deleted
	- Each stream record also contains the name of the table, the event timestamp, and other metadata
	- **Stream records have a lifetime of 24 hours**; after that, they are automatically removed from the stream
	- For example, consider a _Customers_ table that contains customer information for a company. Suppose that you want to send a "welcome" email to each new customer. You could enable a stream on that table, and then associate the stream with a Lambda function. The Lambda function would run whenever a new stream record appears![enter image description here](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/HowItWorksStreams.png)
 - [DAX (DynamoDB Accelerator)](https://docs.amazonaws.cn/en_us/amazondynamodb/latest/developerguide/DAX.html)
	- Seamless cache, no application re-write
	- Writes go through DAX to DynamoDB
	- Micro second latency for cached reads & queries
	- Solves Hot Key problem (too many reads)
	- 5 mins TTL for cache by default
	- Up to 10 nodes in the cluster
	- Multi AZ (3 min nodes recommended)
	- Secure (Encryption at rest with KMS, VPC, IAM etc.)![enter image description here](https://d2908q01vomqb2.cloudfront.net/887309d048beef83ad3eabf2a79a64a389ab1c9f/2018/04/19/Arch-Diagram.jpg)
 - [Transaction](https://aws.amazon.com/blogs/aws/new-amazon-dynamodb-transactions/)
	- From Nov 2018
	- Provide atomicity, consistency, isolation, and durability (ACID) across one or more tables within a single AWS account and region
	- Use cases
		- Processing financial transactions
		- Fulfilling and managing orders
		- Building multiplayer game engines
		- Coordinating actions across distributed components and services
	- Handling transactions using
		- `TransactWriteItems`, a batch operation that contains a write set, with one or more `PutItem`, `UpdateItem`, and `DeleteItem` operations
		- `TransactGetItems`, a batch operation that contains a read set, with one or more `GetItem` operations
	- Items are not locked during a transaction. DynamoDB transactions provide serializable isolation. If an item is modified outside of a transaction while the transaction is in progress, the transaction is canceled and an exception is thrown with details about which item or items caused the exception
	- Transactional operations work within one or more tables within a region, and are not supported across regions in global tables
	- There is no additional cost to enable transactions for DynamoDB tables
 - Global tables
	- Replicate your DynamoDB tables automatically across your choice of AWS Region
	- Eliminate the difficult work of replicating data between Regions and resolving update conflicts
	- There are no upfront costs or commitments for using global tables, and you pay only for the resources provisioned![enter image description here](https://d1.awsstatic.com/product-marketing/DynamoDB/DynamoDB_Global-Tables-01.dad2508b80e8b7c544fe1a94a2abd3f770b789da.png =600x400)
- Use cases
	- Serverless application development
	- Distributed serverless cache
	- SQL query language not available
	- React to changes in real time
	- Analytics
	- Create derivative tables/views
	- Insert into ElasticSearch
	- Could implement cross region replications
-   **For Solution Architect**
    -   _Operations_  
	    - No operations needed
	    - Auto scaling capabilities
	    - Serverless
    -   _Security_
        - IAM policies
        - KMS encryption
        - SSL in flight
    -   _Reliability_  
	    - Multi-AZ
	    - Backups
    -   _Performance_
        -   Single digit millisecond latency
        -   DAX for caching reads
        -   Performance doesn't degraded if your application scales
    -   _Cost_  
	    - Pay per provisioned capacity and storage usage
### [API Gateway](https://docs.aws.amazon.com/apigateway/latest/developerguide/welcome.html)
 - An API gateway is service that sits in front of an API (Application Programming Interface) and is the single-entry point for defined back-end APIs and microservices (which can be both internal and external)
 - Sitting in front of APIs, the gateway acts as protector, enforcing security and ensuring scalability and high availability
 - API Gateway acts as a "front door" for applications to access data, business logic, or functionality from your backend services, such as workloads running on Amazon Elastic Compute Cloud (Amazon EC2), code running on AWS Lambda, any web application, or real-time communication applications
 - **Features**
	 - AWS Lambda + API Gateway = No infrastructure to manage
	 - Support for stateful ([WebSocket](https://docs.aws.amazon.com/apigateway/latest/developerguide/apigateway-websocket-api.html)) and stateless ([HTTP](https://docs.aws.amazon.com/apigateway/latest/developerguide/http-api.html) and [REST](https://docs.aws.amazon.com/apigateway/latest/developerguide/apigateway-rest-api.html)) APIs
	 - Powerful, flexible [authentication](https://docs.aws.amazon.com/apigateway/latest/developerguide/apigateway-control-access-to-api.html) mechanisms, such as AWS Identity and Access Management policies, Lambda authorizer functions, and Amazon Cognito user pools
	 - [CloudTrail](https://docs.aws.amazon.com/apigateway/latest/developerguide/cloudtrail.html) logging and monitoring of API usage and API changes
	 - CloudWatch access logging and execution logging, including the ability to set alarms
	 - Integration with [AWS WAF](https://docs.aws.amazon.com/apigateway/latest/developerguide/apigateway-control-access-aws-waf.html) for protecting your APIs against common web exploits
	 - Integration with [AWS X-Ray](https://docs.aws.amazon.com/apigateway/latest/developerguide/apigateway-xray.html) for understanding and triaging performance latencies
	 - Handles API versioning
	 - Swagger/Open API import to quickly define APIs
	 - Transform and validate requests and responses
	 - Generates SDK and API specification
	 - Cache API response
 - **Architecture**![enter image description here](https://docs.aws.amazon.com/apigateway/latest/developerguide/images/Product-Page-Diagram_Amazon-API-Gateway-How-Works.png)
 -  **Use cases**
	 - *To create HTTP APIs*
		 - HTTP APIs enable you to create RESTful APIs with lower latency and lower cost than REST APIs
		 - Send requests to AWS Lambda functions or to any publicly routable HTTP endpoint
		 - For example, you can create an HTTP API that integrates with a Lambda function on the backend. When a client calls your API, API Gateway sends the request to the Lambda function and returns the function's response to the client
		 - HTTP APIs support [OpenID Connect](https://openid.net/connect/) and [OAuth 2.0](https://oauth.net/2/) authorization
		 - They come with built-in support for cross-origin resource sharing (CORS) and automatic deployments
		 - Works with Lambda and HTTP Backends
	 - *To create REST APIs*
		 - An API Gateway REST API is made up of resources (logical entity that an app can access through a resource path) and methods (request that is submitted by the user of your API)
		 - For example, `/incomes` could be the path of a resource representing the income of the app user. A resource can have one or more operations that are defined by appropriate HTTP verbs such as GET, POST, PUT, PATCH, and DELETE. A combination of a resource path and an operation identifies a method of the API. For example, a `POST /incomes` method could add an income earned by the caller, and a `GET /expenses` method could query the reported expenses incurred by the caller
		 - In API Gateway REST APIs, the frontend is encapsulated by _method requests_ and _method responses_. The API interfaces with the backend by means of _integration requests_ and _integration responses_
		 - For example, with DynamoDB as the backend, the API developer sets up the integration request to forward the incoming method request to the chosen backend. The setup includes specifications of an appropriate DynamoDB action, required IAM role and policies, and required input data transformation. The backend returns the result to API Gateway as an integration response![enter image description here](https://d2908q01vomqb2.cloudfront.net/f1f836cb4ea6efb2a0b1b99f41ad8b103eff4b59/2020/03/04/api-gateway-sagemaker-2.png)
		 - Support for generating SDKs and creating API documentation using API Gateway extensions to OpenAPI
		 - Throttling of HTTP requests
		 - Works with Lambda and HTTP, AWS Services
	 - *To create WebSocket APIs*
		 - In a WebSocket API, the client and the server can both send messages to each other at any time
		 - Backend servers can easily push data to connected users and devices, avoiding the need to implement complex polling mechanisms
		 - For example, you could build a serverless application using an API Gateway WebSocket API and AWS Lambda to send and receive messages to and from individual users or groups of users in a chat room. Or you could invoke backend services such as AWS Lambda, Amazon Kinesis, or an HTTP endpoint based on message content![enter image description here](https://d2908q01vomqb2.cloudfront.net/1b6453892473a467d07372d45eb05abc2031647a/2018/12/18/websockets-arch.png)
		 - You can use API Gateway WebSocket APIs to build secure, real-time communication applications without having to provision or manage any servers to manage connections or large-scale data exchanges
		 - Targeted use cases include real-time applications such as the following
			 - Chat applications
			 - Real-time dashboards such as stock tickers
			 - Real-time alerts and notifications
		 - API Gateway provides WebSocket API management functionality such as the following
			 - Monitoring and throttling of connections and messages
			 - Using AWS X-Ray to trace messages as they travel through the APIs to backend services
			 - Easy integration with HTTP/HTTPS endpoints
		 - Works with Lambda and HTTP, AWS Services
	 - [# Choosing between HTTP APIs and REST APIs](https://docs.aws.amazon.com/apigateway/latest/developerguide/http-api-vs-rest.html)
	 - **Endpoint type**
		 - Edge-Optimized (Default)
			 - For global clients
			 - Request are routed through the CloudFront Edge locations (improves latency)
			 - Gateway still lives in only one region
		 - Regional
			 - For clients within same region
			 - Cloud manually combine with CloudFront (more control over the caching strategies and the distribution)
		 - Private
			 - Can only be accessed from your VPC using an interface VPC endpoint (ENI)
			 - Use a resource policy to define access
	 - **Security**
		 - IAM Permissions
			 - Create an IAM policy authorization and attach to User/Role
			 - API Gateway verifies IAM permissions passed by the calling applications
			 - Leverage "Sig v4" capability where IAM credential are in headers
		 - Lambda Authorizer (Custom Authorizers)
			 - Uses Lambda to validate the token in header being passed
			 - Option to cache result of authentication
			 - Helps to use OAuth/SAML/3rd party type of authentication
			 - Lambda must return an IAM policy for the user
		 - Cognito user pools
			 - Fully Manages user lifecycle
			 - You manage your own user pool (can be Facebook, google etc.)
			 - API gateway verifies identity automatically from AWS Cognito
			 - No custom implementation required
			 - Only helps with authentication, not authorization (must implement authorization in the backend)
### [Cognito](https://docs.aws.amazon.com/cognito/latest/developerguide/what-is-amazon-cognito.html)
 - Provides authentication, authorization, and user management for your web and mobile apps
 - Your users can sign in directly with a user name and password, or through a third party such as Facebook, Amazon, Google or Apple
 - **Cognito User Pools**
	 - It is a user directory that provide sign-up and sign-in options for your app users
	 - Whether your users sign in directly or through a third party, all members of the user pool have a directory profile that you can access through an SDK
	 - Sends backs Json Web Tokens (JWT)
	 - Integrates with API Gateway
 - **Cognito Identity Pools** (Federated Identity)
	 - Identity pools enable you to grant your users access to other AWS services
	 - With an identity pool, your users can obtain temporary AWS credentials to access AWS services, such as Amazon S3 and DynamoDB
	 - Identity pools support anonymous guest users, as well as federation through third-party IdPs
	 - Integrates with Cognito User pools as an identity provider
	 - Example: provide temporary access to write to S3 bucket using Facebook login
 - **Cognito Sync** (Deprecated, current AWS AppSync)
	 - Synchronize data from device to Cognito
	 - Store preferences, configuration, state of app
	 - Cross device synchronization
	 - Offline capability (synchronization when back offline)
	 - Requires Federated Identity Pool
	 - Data stored in datasets (up to 1MB) and up to 20 datasets to Synchronize
 - [Scenarios](https://docs.aws.amazon.com/cognito/latest/developerguide/cognito-scenarios.html)
	 - *Authenticate with a User Pool*
		 - You can enable your users to authenticate with a user pool
		 - Your app users can sign in either directly through a user pool, or federate through a third-party identity provider (IdP)
		 - The user pool manages the overhead of handling the tokens that are returned from social sign-in through Facebook, Google, Amazon, and Apple, and from OpenID Connect (OIDC) and SAML IdPs
		 - After a successful authentication, your web or mobile app will receive user pool tokens from Amazon Cognito. You can use those tokens to retrieve AWS credentials that allow your app to access other AWS services, or you might choose to use them to control access to your server-side resources, or to the Amazon API Gateway
		 - ![enter image description here](https://docs.aws.amazon.com/cognito/latest/developerguide/images/scenario-authentication-cup.png)
	 - *Access Your Server-side Resources with a User Pool*
		 - After a successful user pool sign-in, your web or mobile app will receive user pool tokens from Amazon Cognito
		 - You can use those tokens to control access to your server-side resource
		 - ![enter image description here](https://docs.aws.amazon.com/cognito/latest/developerguide/images/scenario-standalone.png)
	 - *Access Resources with API Gateway and Lambda with a User Pool*
		 - API Gateway validates the tokens from a successful user pool authentication, and uses them to grant your users access to resources including Lambda functions, or your own API
		 - You can use groups in a user pool to control permissions with API Gateway by mapping group membership to IAM roles
		 - ![enter image description here](https://docs.aws.amazon.com/cognito/latest/developerguide/images/scenario-api-gateway.png)
	 - Access AWS Services with a User Pool and an Identity Pool
		 - After a successful user pool authentication, your app will receive user pool tokens from Amazon Cognito. You can exchange them for temporary access to other AWS services with an identity pool
		 - ![enter image description here](https://docs.aws.amazon.com/cognito/latest/developerguide/images/scenario-cup-cib.png)
	 - *Authenticate with a Third Party and Access AWS Services with an Identity Pool*
		 - You can enable your users access to AWS services through an identity pool
		 - An identity pool requires an IdP token from a user that's authenticated by a third-party identity provider
		 - ![enter image description here](https://docs.aws.amazon.com/cognito/latest/developerguide/images/scenario-identity-pool.png)
	 - *Access AWS AppSync Resources with Amazon Cognito*
		 - You can grant your users access to AWS AppSync resources with tokens from a successful Amazon Cognito authentication
		 - ![enter image description here](https://docs.aws.amazon.com/cognito/latest/developerguide/images/scenario-appsync.png)
###  [Glue](https://docs.aws.amazon.com/glue/latest/dg/what-is-glue.html)
- Fully serverless service
- Managed extract, transform, and load (ETL) service
- Useful to prepare and transform data for analytics
- ![enter image description here](https://d1.awsstatic.com/Products/product-name/diagrams/product-page-diagram_Glue_Event-driven-ETL-Pipelines.e24d59bb79a9e24cdba7f43ffd234ec0482a60e2.png)
- [Glue Data Catalog](https://docs.aws.amazon.com/glue/latest/dg/populate-data-catalog.html)
	- Catalog of datasets
	- Serves as Central metadata repository
	- Contains references to data that is used as sources and targets of your extract, transform, and load (ETL) jobs
	- It is an index to the location, schema, and runtime metrics of your data
	- Information in the Data Catalog is stored as metadata tables, where each table specifies a single data store
	- ![enter image description here](https://docs.aws.amazon.com/glue/latest/dg/images/PopulateCatalog-overview.png)
	- ![enter image description here](https://csharpcorner-mindcrackerinc.netdna-ssl.com/UploadFile/NewsImages/08192020072349AM/Glu1.png)

### Serverless Q&A
Q: You have a Lambda function that will process data for 25 minutes before successfully completing. The code is working fine in your machine, but in AWS Lambda it just fails with a "timeout" issue after 3 seconds. What should you do?
> Run your code somewhere else than Lambda - the maximum timeout is 15 minutes

Q: You'd like to have a dynamic DB_URL variable loaded in your Lambda code
> Environment variables allow for your Lambda to have dynamic variables from within

Q: We have to provision the instance type for our DynamoDB database
> False, DynamoDB is a serverless service and as such we don't provision an instance type for our database. We just say how much RCU and WCU we require for our table (or auto scaling)

Q: A DynamoDB table has been provisioned with 10 RCU and 10 WCU. You would like to increase the RCU to sustain more read traffic. What is true about RCU and WCU?
> RCU and WCU are decoupled, so WCU can stay the same

Q: You are about to enter the Christmas sale and you know a few items in your website are very popular and will be read often. Last year you had a **ProvisionedThroughputExceededException.** What should you do this year?
> Create DAX Cluster (A DynamoDB Accelerator (DAX) cluster is a cache that fronts your DynamoDB tables and caches the most frequently read values. They help offload the heavy reads on hot keys off of DynamoDB itself, hence preventing the ProvisionedThroughputExceededException)

Q: You would like to automate sending welcome emails to the users who subscribe to the Users table in DynamoDB. How can you achieve that?
> Enable DynamoDB Streams and have the Lambda function receive the events in real-time

Q: To make a serverless API, I should integrate API Gateway with
> Lambda (Lambda is a serverless technology)

Q: You would like to provide a Facebook login before your users call your API hosted by API Gateway. You need seamlessly authentication integration, you will use
> Cognito User Pool (Cognito User Pools directly integration with Facebook Logins)

Q: Your production application is leveraging DynamoDB as its backend and is experiencing smooth sustained usage. There is a need to make the application run in development as well, where it will experience unpredictable, sometimes high, sometimes low volume of requests. You would like to make sure you optimize for cost. What do you recommend?
> Provision WCU & RCU and enable auto-scaling for production and use on-demand capacity for development

## Security
### Encryption
- *In flight (SSL)*
	- Data is encrypted before sending and decrypted after receiving 
	- SSL certificates helps with encryption (HTTPS)
	- Encryptions in flight ensures no MITM (man in middle attack)
- *Server side encryption at rest*
	- Data is encrypted after being received by the server
	- Data is decrypted before being sent
	- It is stored in an encrypted form, thanks to a key (data key)
	- The encryption/decryption key must be managed somewhere and the server must not have access to it
- *Client side encryption*
	- Data is encrypted by client and never decrypted by the server
	- Data will be decrypted  by a receiving client
	- Could leverage Envelope Encryption
### [KMS](https://docs.aws.amazon.com/kms/latest/developerguide/overview.html)
- Create and control the cryptographic keys that are used to protect your data
- Fully integrated with IAM for authorization
- Seamlessly integrated with
	- EBS - Encrypt volumes
	- S3 - Encrypt objects (sever side)
	- Redshift - Encrypt data
	- RDS - Encrypt data
	- SSM - Parameter store
- You can also use the CLI/SDK
- Customer Master Key (CMK) types
	- You can use a KMS key to encrypt, decrypt, and re-encrypt data
	- It can also generate data keys that you can use outside of AWS KMS
	- _KMS key_ is a logical representation of an encryption key
	- In addition to the key material used to encrypt and decrypt data, a KMS key includes metadata, such as the key ID, creation date, description, and key state
	- [Symmetric key (AES-256)](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#symmetric-cmks)
		- When you create an AWS KMS key, by default, you get a symmetric KMS key
		- AWS services that are integrated with KMS use Symmetric CMKs
		- Necessary for envelope encryption
		- You never get access to the unencrypted key (you must call AWS KMS to get the key)
	- [Asymmetric key (RSA & ECC pairs)](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#asymmetric-keys-concept)
		- Represents a mathematically related public key (encryption) and private key (decryption) pair (that's how SSL works)
		- Used for Encrypt/Decrypt, or Sign/Verify operations
		- You can download public key, however private key is not accessible
		- Use Case - encryption outside of AWS by users who can't call the KMS API
- Use KMS, anytime you need to share sensitive information like
	- Database passwords
	- Creds to external service
	- Private Key of SSL certificates
- KMS can only help in encrypting up to 4KB of data per call, if data > 4KB use envelope encryption
- To give access to KMS to someone
	- [Key Policy](https://docs.aws.amazon.com/kms/latest/developerguide/key-policies.html) to allow the user
		- Default - AWS account (root user) that owns the KMS key will have full access to the KMS key and enables IAM policies in the account to allow access to the KMS key
			```json
			{
			  "Sid": "Enable IAM policies",
			  "Effect": "Allow",
			  "Principal": {
			    "AWS": "arn:aws:iam::111122223333:root"
			   },
			  "Action": "kms:*",
			  "Resource": "*"
			}
			```
		- Custom - For users, roles. Also can define who can administer the key. useful for cross account access of your KMS key
	- [IAM Policy](https://docs.aws.amazon.com/kms/latest/developerguide/iam-policies.html) to allow the API calls
		- Attach a permissions policy to a user or a group
		- Attach a permissions policy to a role for federation or cross-account permissions
		- e.g. allow the IAM identities to which it is attached to list all KMS keys and alias
			```json
			{
			  "Version": "2012-10-17",
			  "Statement": {
			    "Effect": "Allow",
			    "Action": [
			      "kms:ListKeys",
			      "kms:ListAliases"
			    ],
			    "Resource": "*"
			  }
			} 
			```
- **Copying snapshot across accounts** (which using KMS encryption)
	- You have EBS volume encrypted with CMK *Key A*
	- Create a snapshot, encrypted with CMK *Key A*
	- Attach a KMS key policy to authorize cross-account access (allow target to read our key)
	- Share encrypted snapshot
	- In target, create a copy of the snapshot, re-encrypt it with a new KMS *Key B* 
	- Create volume encrypted from that snapshot with a new KMS *Key B* 
	- More details are [here](https://aws.amazon.com/blogs/security/how-to-create-a-custom-ami-with-encrypted-amazon-ebs-snapshots-and-share-it-with-other-accounts-and-regions/) ![enter image description here](https://d2908q01vomqb2.cloudfront.net/22d200f8670dbdb3e253a90eee5098477c95c23d/2016/09/29/solutiondiagram_ey_AMIs_a.png)
- [Key Rotation](https://docs.aws.amazon.com/kms/latest/developerguide/rotate-keys.html)
	- For customer managed CMK (Not for AWS managed CMK)
	- *Automatic rotation* 
		- Key rotation happens every 1 year
		- Previous key is kept active so you can decrypt old data
		- New key has then same CMK ID (only the backing key is changed) ![enter image description here](https://docs.aws.amazon.com/kms/latest/developerguide/images/key-rotation-auto.png)
	-  *Manual rotation*
		- Key rotation can happen every 90 days, 180 days etc.
		- New key has a different CMK ID
		- Previous key is kept active so you can decrypt old data
		- Better to use aliases in this case (to hide change of key id)
		- Good solution to rotate CMK which can not be rotate automatically (like asymmetric CMK) ![enter image description here](https://docs.aws.amazon.com/kms/latest/developerguide/images/key-rotation-manual.png)
### [SSM Parameter Store](https://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-parameter-store.html)
- Secure storage for configuration and secrets
- Optional encryption using KMS
- Serverless, scalable, durable, easy SDK
- Versioning of configuration and secrets
- Notification with CloudWatch Events
- Integration with CloudFormation
- Policies
	- Allow to assign a TTL to a parameter (expiration date) to force updating or deleting sensitive data such as password
	- Can assign multiple policies at a time
	- *Expiration* - When the expiration date and time is reached, Parameter Store deletes the parameter
		```json
		{
		  "Type":"Expiration",
		  "Version":"1.0",
		  "Attributes":{
		     "Timestamp":"2018-12-02T21:34:33.000Z"
		  }
		}
		```
	- *ExpirationNotification* - This policy initiates an event in Amazon EventBridge (EventBridge) that notifies you about the expiration
		```json
		{
		 "Type":"ExpirationNotification",
		 "Version":"1.0",
		 "Attributes":{
		    "Before":"15",
		    "Unit":"Days"
		 }
		}
		```
	- *NoChangeNotification* - This policy initiates an event in EventBridge if a parameter has _not_ been modified for a specified period of time
		```json
		{
		   "Type":"NoChangeNotification",
		   "Version":"1.0",
		   "Attributes":{
		      "After":"20",
		      "Unit":"Days"
		   }
		}
		```
### [Secret Manager](https://docs.aws.amazon.com/secretsmanager/latest/userguide/intro.html)
-	New service, meant for storing secrets
-	Capability to force *rotation of secrets* every X days
-	Automate generation of secrets on rotation (uses Lambda)
-	Has integration with RDS
-	Secrets are encrypted using KMS
-	Mostly meant for RDS integration
-	In Secrets Manager, a _secret_ consists of a set of credentials, user name and password, and the connection details to access a database or other service
-	A secret has metadata
	-	ARN - `arn:aws:secretsmanager:<Region>:<AccountId>:secret:SecretName-<6RandomCharacters>`
	-	The name of the secret, a description, a resource policy, and tags
	-	The ARN for an _encryption key_, an AWS KMS key that Secrets Manager uses to encrypt and decrypt the secret value
	-	Information about how to rotate the secret
-	A secret has _versions_ which hold copies of the encrypted secret value ![enter image description here](https://res.cloudinary.com/practicaldev/image/fetch/s--VgHWgUbW--/c_limit,f_auto,fl_progressive,q_auto,w_880/https://thepracticaldev.s3.amazonaws.com/i/a25p0b9tmxsosoh4khj7.png)
### [CloudHSM](https://docs.aws.amazon.com/crypto/latest/userguide/awscryp-service-hsm.html)
- Service for creating and maintaining hardware security modules (HSM)
- AWS provisions encryption hardware
- Dedicated hardware (HSM)
- You manage your own encryption keys entirely
- HSM device is tamper resistant
- Support both symmetric and asymmetric encryption
- No free tier
- Must use the CloudHSM Client software
- Redshift supports CloudHSM for DB encryption and key management
- Good option to use with SSE-C encryption
- More details [here](https://aws.amazon.com/blogs/aws/aws-cloud-hsm-secure-key-storage-and-cryptographic-operations/)

### [Shield](https://docs.aws.amazon.com/waf/latest/developerguide/ddos-overview.html)
- Free service that is activated for every AWS customer
- Provides protection from attacks such as SYN/UDP floods, reflection attacks and other layer3/layer4 attacks
- Optional DDoS mitigation service ($3000/month/organization)
- Protects against more sophisticated attack on EC2, ELB, CloudFront, Global Accelerator and Route 53
- 24/7 access to AWS DDoS response team
- Protect against higher fees during usage spikes due to DDoS

### [Web Application Firewall (WAF)](https://docs.aws.amazon.com/waf/latest/developerguide/waf-chapter.html)
- Protects your web application from commo web exploits (Layer 7 i.e HTTP)
- Deploy on ALB, API gateway and CloudFront only
- Define Web ACL (Access Control List)
	- Rules that can includes IP address, HTTP headers, HTTP Body or URI strings
	- Protects from common attacks such SQL injection and Cross Site Scripting (XSS)
	- Can define size constraint, geo-match (to block countries)
	- Rate based rules for DDoS protection
- WAF vs Shield ![enter image description here](https://static.wixstatic.com/media/7ac7b2_7af300d480324ef485e79912c21f4eb1~mv2.png/v1/fill/w_1000,h_543,al_c,usm_0.66_1.00_0.01/7ac7b2_7af300d480324ef485e79912c21f4eb1~mv2.png)

### Guard​Duty
- Intelligent Threat discovery to protect account
- Uses ML algorithms, anomaly detection, 3rd party data
- One click to enable (30 days trial)
- Input data for *Guard​Duty* includes
	- *CloudTrail Logs* - unusual API calls, unauthorized deployments
	- *VPC flow logs* - unusual internal traffic, unusual IP address
	- *DNS logs* - compromised EC2 instances sending encoded data within DNS queries
- Can setup *CloudWatch Event* rules to be notified in case of findings
- CloudWatch Event rules can target AWS Lambda or SNS
- Can protect against *Cryptocurrency* attacks ![enter image description here](https://d2908q01vomqb2.cloudfront.net/77de68daecd823babbb58edb1c8e14d7106e83bb/2018/05/15/GuardDuty-Integration.jpg)

### Inspector
- An automated security assessment service that helps improve the security and compliance of applications deployed on AWS
- For EC2 instances only
- Analyze the running OS against known vulnerabilities
- Analyze against unintended network accessibility
- To use, AWS inspector agent must be installed on underlaying OS of EC2 instance
- After assessment, you get a report with a list of vulnerabilities
- Possibility to send notifications to SNS ![enter image description here](https://d2908q01vomqb2.cloudfront.net/22d200f8670dbdb3e253a90eee5098477c95c23d/2017/11/18/part1-Step0-Overview-v2-a-1260x490.png)
- *For network assessment*
	- Agentless
	- Check against network reachability
- *For host assessment*
	- With agent
	- Check against common vulnerabilities and exposures
	- Center for internet security benchmarks
### Macie
- Fully managed data security and data privacy service that uses machine learning and pattern matching to discover and protect your sensitive data in AWS
- Macie automatically provides an inventory of Amazon S3 buckets including a list of unencrypted buckets, publicly accessible buckets, and buckets shared with AWS accounts outside those you have defined in AWS Organizations.
- Then, Macie applies machine learning and pattern matching techniques to the buckets you select to identify and alert you to sensitive data, such as personally identifiable information (PII)
- This can help you meet regulations, such as the Health Insurance Portability and Accountability Act (HIPAA) and General Data Privacy Regulation (GDPR) ![enter image description here](https://d1.awsstatic.com/product-marketing/macie/Product-Page-Diagram_AWS-Macie@2x.369dcc5a001e7a44b121d65637ff82b60b809148.png)

### [Shared Responsibility](https://docs.aws.amazon.com/whitepapers/latest/aws-security-incident-response-guide/shared-responsibility.html)
- The responsibility for security and compliance is shared between AWS and you
- *AWS responsibility*
	- Security **of** the cloud
	- Protecting infra (hardware, software, facilities and networking) that runs all the AWS services
	- Managed services like S3, DynamoDB, RDS, etc.
- *Customer responsibility*
	- Security **in** the cloud
	- For EC2 instance, customer responsible for management of guest OS. firewall & network configuration
	- Encrypting application data
- Shared control	- Patch management, Configuration Management, Awareness & training
- Example, for RDS
	- *AWS responsibility*
		- Manage underlaying EC2 instances, disable SSH
		- Automate DB patching
		- Automate OS patching
		- Audit the underlaying instance & disks
	- *Customer responsibility*
		- Check the ports/IP/SG inbound rules
		- In database server creation and permission
		- Creating database with or without public access
		- Ensure parameter group or DB is configures to only allow SSL
		- Database encryption setting
-  Example, for S3
	- 	*AWS responsibility*
		- Guarantee you get unlimited storage
		- Guarantee you get encryption
		- Ensure separation of the data between different customers
		- Ensure AWs employeed can't acess your data
	- *Customer responsibility*
		- Bucker configuration
		- Bucket policy/public setting
		- IAM user and roles
		
### Security Q&A
Q: To enable In-flight Encryption (In-Transit Encryption), we need to have ...
> an HTTPS endpoint with an SSL certificate (In-flight Encryption = HTTPS, and HTTPS can not be enabled without an SSL certificate)

Q: Server-Side Encryption means that the data is sent encrypted to the server.
> False (Server-Side Encryption means the server will encrypt the data for us. We don't need to encrypt it beforehand)

Q: In Server-Side Encryption, where do the encryption and decryption happen?
> Both Encryption and Decryption happen on the server (In Server-Side Encryption, we couldn't be able to decrypt the data ourselves as we can't have access to the corresponding encryption key)

Q: In Client-Side Encryption, the server must know our encryption scheme before we can upload the data
> False (With Client-Side Encryption, the server doesn't need to know any information about the encryption scheme being used, as the server will not perform any encryption or decryption operations)

Q: You need to create KMS Keys in AWS KMS before you are able to use the encryption features for EBS, S3, RDS ...
> False (You can use the AWS Managed Service keys in KMS, therefore we don't need to create our own KMS keys. However, you could also create your own keys for AWS to do the encryption, but it's not mandatory)

Q: AWS KMS supports both symmetric and asymmetric KMS keys
> True (KMS keys can be symmetric or asymmetric. A symmetric KMS key represents a 256-bit key used for encryption and decryption. An asymmetric KMS key represents an RSA key pair used for encryption and decryption or signing and verification, but not both. Or it represents an elliptic curve (ECC) key pair used for signing and verification)

Q: When you enable Automatic Rotation on your KMS Key, the backing key is rotated every ...
> 1 year

Q: You have an AMI that has an encrypted EBS snapshot using KMS CMK. You want to share this AMI with another AWS account. You have shared the AMI with the desired AWS account, but the other AWS account still can't use it. How would you solve this problem?
> You need to share the KMS CMK used to encrypt the AMI with the other AWS account

Q: You have created a Customer-managed CMK in KMS that you use to encrypt both S3 buckets and EBS snapshots. Your company policy mandates that your encryption keys be rotated every 3 months. What should you do?
> Rotate the KMS CMK manually. Create a new KMS CMK and use Key Aliases to reference the new KMS CMK. Keep the old KMS CMK so you can decrypt the old data

Q: What should you use to control access to your KMS CMKs?
> KMS key policies

Q: You have a Lambda function used to process some data in the database. You would like to give your Lambda function access to the database password. Which of the following options is the most secure?
> Have it as an encrypted environment variable and decrypt it at runtime

Q: You have a secret value that you use for encryption purposes, and you want to store and track the values of this secret over time. Which AWS service should you use?
> SSM Parameters Store (can be used to store secrets and has built-in version tracking capability. Each time you edit the value of a parameter, SSM Parameter Store creates a new version of the parameter and retains the previous versions. You can view the details, including the values, of all versions in a parameter's history)

Q: According to AWS Shared Responsibility Model, what are you responsible for in RDS?
> SG rules

Q: Your user-facing website is a high-risk target for DDoS attacks and you would like to get 24/7 support in case they happen and AWS bill reimbursement for the incurred costs during the attack. What AWS service should you use?
> AWS Shield Advanced

Q: You would like to externally maintain the configuration values of your main database, to be picked up at runtime by your application. What's the best place to store them to maintain control and version history?
> SSM Parameters Store

Q: You would like to use a dedicated hardware module to manage your encryption keys and have full control over them. What do you recommend?
> CloudHSM

Q: AWS GuardDuty do not scans the following data sources ...
> CloudWatch Logs

Q: You have a website hosted on a fleet of EC2 instances fronted by an Application Load Balancer. What should you use to protect your website from common web application attacks (e.g., SQL Injection)?
> WAF

Q: You would like to analyze OS vulnerabilities from within EC2 instances. You need these analyses to occur weekly and provide you with concrete recommendations in case vulnerabilities are found. Which AWS service should you use?
> Inspector

Q: What is the most suitable AWS service for storing RDS DB passwords which also provides you automatic rotation?
> Secret Manager

Q: Which AWS service allows you to centrally manage EC2 Security Groups and AWS Shield Advanced across all AWS accounts in your AWS Organization?
> AWS Firewall Manager (is a security management service that allows you to centrally configure and manage firewall rules across your accounts and applications in AWS Organizations. It is integrated with AWS Organizations so you can enable AWS WAF rules, AWS Shield Advanced protection, security groups, AWS Network Firewall rules, and Amazon Route 53 Resolver DNS Firewall rules)

Q: Which AWS service helps you protect your sensitive data stored in S3 buckets?
> Amazon Macie (is a fully managed data security service that uses Machine Learning to discover and protect your sensitive data stored in S3 buckets. It automatically provides an inventory of S3 buckets including a list of unencrypted buckets, publicly accessible buckets, and buckets shared with other AWS accounts. It allows you to identify and alert you to sensitive data, such as Personally Identifiable Information (PII))

## Deployment
### [Elastic Beanstalk](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/Welcome.html)
- Beanstalk, you can quickly deploy and manage applications in the AWS Cloud without having to learn about 
   the infrastructure that runs those applications.
-  After you create and deploy your application, information about the application—including metrics, events, and
   environment status—is available through the Elastic Beanstalk console, APIs, or Command Line Interfaces, including 
   the unified AWS CLI
- Developer centric
- Managed service, instance configuration and deployment auto handled ![enter image description here](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/images/clearbox-flow-00.png)
- Models
	- Single instance development
	- LB + ASG great for prd and pre-prd web apps
- Components
	- Application
	- Application version for each deployment
	- Environment name
- Rollback feature to previous version
- Full control over lifecycle of environments

## Solution Architecture Discussions

### Classic
- We're considering 5 pillars for a well architect application: Cost, Performance, Reliability, Security & Operational Excellence
- **WhatIsTheTime.com (Stateless)**
	- Requirement
		- No Database
		- Low latency selected
	- Solution
		- Setup private EC2 Instances 
			- In different AZs for [Availability and Disaster recovery]
			- with ASG  (we can add and remove instances at run time) [Better Costing]
			- Setup SG (EC2 will on receive request for ELB) [Security]
			- Reserving instance for minimum capacity [Better Costing]
		- Add Multi AZ ELB [Performance] 
		- Health Check [Performance]
		- Use Route 53 to manage DNS queries [Reliability]
		- Fully Automated like ASG, ELB and Route 53 [Operational Excellence]
- **MyClothes (Stateful)**
	- Requirement
		- Database with hundreds of Users the same time
		- Need vertically scaling
	- Solution
		- Introduce Route 53
		- Introduce Multi AZ ELB
		- Setup private EC2 Instances 
			- In different AZs
			- with ASG  (we can add and remove instances at run time)
			- Setup SG (EC2 will on receive request for ELB)
			- Reserving instance for minimum capacity
		- Introduce ElastiCache (Alt DynamoDB)
			- For storing user session data 
			- For caching data from RDS
			- Multi AZ
		- Introduce RDS
			- For storing user data
			- Read replicas for scaling reads
			- Multi AZ for disaster recovery
		- Restrict traffic to EC2 SG from ELB
		- Restrict traffic to ElastiCache SG from the EC2 SG
		- Restrict traffic to RDS SG from the EC2 SG
- **MyWordPress (Stateful)**
	- Requirement
		- Fully scalable
		- Display images
		- Has user data and blog content stored in MySQL
	- Solution
		- Introduce Route 53
		- Introduce Multi AZ ELB
		- Setup private EC2 Instances 
			- In different AZs
			- with ASG  (we can add and remove instances at run time)
			- Setup SG (EC2 will on receive request for ELB) 
			- Reserving instance for minimum capacity
			- Create ENI (Elastic network Interface) in each AZ to access EFS
		- Introduce EFS (EBS good for single instance only)
			- Elastic File System
			- To store images in distributed application
		- Introduce RDS
			- For storing user data using MySQL
			- Read replicas for scaling reads
			- Multi AZ for disaster recovery
		- Restrict traffic to EC2 SG from ELB
		- Restrict traffic to EFS SG from the EC2 SG
		- Restrict traffic to RDS SG from the EC2 SG
### Serverless
- **MyTodoList**
	- Requirement
		- Rest API with HTTPS
		- Serverless
		- User should able to directly interact with their own folder in S3
		- User should authenticate through a managed serverless service
		- User can write and read to-dos, but mostly read them
		- Database should scale and have some high read throughput
	- Solution
		- Introduce REST API through API gateway
		- Mobile client will authenticate using amazon Cognito which in turn verify authentication through API gateway
		- API gateway will invoke Lambda function which in turn query DynamoDB
		- Cognito will generate temp credentials for Client by calling AWS STS 
		- Client can use this temp credentials to access S3 bucket to store/retrieve files
		- Use DAX Caching layer for increase RCUs
		- Use API response caching![enter image description here](https://raw.githubusercontent.com/nikxsh/aws/master/diagrams/aws-serverless-mytodolist.png)
- **MyBlog.com**
	- Requirement
		- Should scale globally
		- Rarely written, often read
		- Some of the website is purely static files, the rest is a dynamic REST API
		- Caching must be implemented
		- New subscriber should receive a welcome mail
		- Photo uploaded to the blog should have thumbnail generated
	- Solution
		- To serve static content globally use Amazon CloudFront Global distribution, interacting with edge locations
		- To serve securely, use OAI (origin access identity) to access S3, Bucker policy will only allows request with OAI
		- API gateway will invoke Lambda function which in turn query DynamoDB
		- Use DAX Caching layer for increase RCUs
		- Can use DynamoDB Global tables
		- Use DynamoDB stream to invoke Lambda which will AWS Simple Message Service to send Email
		- S3 will call Lambda which will generate thumbnail and save to S3 (optionally can push to SQS and SNS)![enter image description here](https://raw.githubusercontent.com/nikxsh/aws/master/diagrams/aws-serverless-myblog.com.png)
- **Micro Services**
	- Requirement
		- Synchronous & asynchronous patterns
		- Serverless
		- Pay per usage
	- Solutions
		- Synchronous using API Gateway and Load balancer
		- Asynchronous using SQS, Kinesis, SNS and Lambda triggers (S3)
		- Serverless using API Gateway, Lambda![enter image description here](https://raw.githubusercontent.com/nikxsh/aws/master/diagrams/aws-serverless-microservices.png)
- **Distributed Paid Content**
	- Requirement
		- Sell videos online
		- User have to pay to buy videos
		- Distribute videos only to premium users
		- Database for premium users
		- Link we send to premium users should be short lived
		- Global application
		- Fully serverless
	- Solution
		- Premium User service, to store/retrieve premium users using DynamoDB Tables
		- CloudFront to generate short lived signed URL using AWS SDK and lambda invoke
		- S3 to store and retrieve videos files
		- Cognito to verify AUTH
		- *S3 signed URL not efficient for global access*
	- ![enter image description here](https://raw.githubusercontent.com/nikxsh/aws/master/diagrams/aws-severless-distributing-paid-content.png)
- **Software update distribution**
	- Requirement
		- We have application running on EC2, that distributes software updates once in a while
		- When a new software update is out, we get lot of requests as our content distributed in mass over the network (very costly)
		- We don't want to change application but want to optimize our cost and CPU
	- Solution
		- Use CloudFront, because
			- No change is architecture
			- Will cache software updates files at the edge
			- Software updates files are static
			- EC2 instances are not serverless
			- ASG will not scale as much, and we'll save in EC2
			- Will save in availability, network bandwidth cost etc.![enter image description here](https://raw.githubusercontent.com/nikxsh/aws/master/diagrams/aws-serveless-software-update-offloading.png)
- **Big Data Ingestion Pipeline**
	- Requirement
		- Ingestion pipeline to be fully serverless
		- Collect data in real time
		- Transform data
		- Query transformed data
		- Load data to warehouse and create dashboards
	- Solution
		- IoT devices will send data to Kinesis Data Stream using AWS IoT Core
		- Kinesis Data Stream will talk to Kinesis Data Firehouse which will upload our data to ingestion bucket every min
		- Kinesis Data Firehouse also will talk to lambda function to cleanse or transform our data
		- Ingestion bucket will call lambda function which will trigger Amazon Athena SQL query
		- Amazon Athena will pull data using query from ingestion bucket and upload it to the reporting bucket ![enter image description here](https://raw.githubusercontent.com/nikxsh/aws/master/diagrams/aws-serverless-data-ingestion.png)
### Case Studies Q&A

Q:  You have an ASG that scales on demand based on the traffic going to your new website: TriangleSunglasses.Com. You      would like to optimize for cost, so you have selected an ASG that scales based on demand going through your ELB. Still,      you want your solution to be highly available so you have selected the minimum instances to 2. How can you further      optimize the cost while respecting the requirements?
> Reserve two EC2 instances (This is the way to save further costs as we know we will run 2 EC2 instances no matter what.)

Q: Which of the following will **NOT** help make our application tier stateless?
> Storing shared data on EBS volumes (EBS volumes are created for a specific AZ and can only be attached to one EC2
      instance at a time. This will not help make our application stateless)
      
Q:  You are looking to store shared software updates data across 100s of EC2 instances. The software updates should be      dynamically loaded on the EC2 instances and shouldn't require heavy operations. What do you suggest?
> Store the software updates on EFS and mount EFS as a network drive (EFS is a network file system (NFS) and allows to mount the same file system to 100s of EC2 instances. Publishing software updates their allow each EC2 instance to access them.)

Q: As a solution architect managing a complex ERP software suite, you are orchestrating a migration to the AWS cloud. The     software traditionally takes well over an hour to setup on a Linux machine, and you would like to make sure your      application does leverage the ASG feature of auto scaling based on the demand. How do you recommend you speed up
     the installation process?
 > Use golden AMI (Golden AMI are a standard in making sure you snapshot a state after an application installation so that future instances can boot up from that AMI quickly.)

Q: I am creating an application and would like for it to be running with minimal cost in a development environment with     Elastic Beanstalk. I should run it in
> Single Instance Mode (This will create one EC2 instance and one Elastic IP)

Q: My deployments on Elastic Beanstalk have been painfully slow, and after looking at the logs, I realize this is due to the    fact that my dependencies are resolved on each EC2 machine at deployment time. How can I speed up my deployment     with the minimal impact?
> Create a Golden AMI that contains the dependencies and launch the EC2 instances from that. (Golden AMI are a standard in making sure save the state after the installation or pulling dependencies so that future instances can boot up from that AMI quickly)

Q: As a solutions architect, you have been tasked to implement a fully Serverless REST API. Which technology choices do you recommend?
> API Gateway + AWS Lambda

Q: Which technology does not have an out of the box caching feature?
> Lambda (Lambda does not have an out of the box caching feature (it's often paired with API gateway for that))

Q: Which service allows to federate mobile users and generate temporary credentials so that they can access their own S3 bucket sub-folder?
> Cognito (in combination with STS)

Q: You would like to distribute your static content which currently lives in Amazon S3 to multiple regions around the world, such as the US, France and Australia. What do you recommend?
> CloudFront

Q: You have hosted a DynamoDB table in ap-northeast-1 and would like to make it available in eu-west-1. What must be enabled first to create a DynamoDB Global Table?
> DynamoDB Streams (Streams enable DynamoDB to get a changelog and use that changelog to replicate data across regions)

Q: A Lambda function is triggered by a DynamoDB stream and is meant to insert data into SQS for further long processing jobs. The Lambda function does seem able to read from the DynamoDB stream but isn't able to store messages in SQS. What's the problem?
> The lambda IAM role is missing permission

Q: You would like to create a micro service whose sole purpose is to encode video files with your specific algorithm from S3 back into S3. You would like to make that micro-service reliable and retry upon failure. Processing a video may take over 25 minutes. The service is asynchronous and it should be possible for the service to be stopped for a day and resume the next day from the videos that haven't been encoded yet. Which of the following service would you recommend to implement this service?
>  SQS + EC2 (SQS allows you to retain messages for days and process them later, while we take down our EC2 instances)(Lambda has a 15 minutes timeout)

Q: You would like to distribute paid software installation files globally for your customers that have indeed purchased the content. The software may be purchased by different users, and you want to protect the download URL with security including IP restriction. Which solution do you recommend?
> CloudFront Signed URL (This will have security including IP restriction)

Q: You are a photo hosting service and publish every month a master pack of beautiful mountains images, that are over 15 GB in size and downloaded from all around the world. The content is currently hosted on EFS and distributed by ELB and EC2 instances. You are experiencing high load each month and very high network costs. What can you recommend that won't force an application refactor and reduce network costs and EC2 load dramatically?
> Create CloudFront distribution (CloudFront can be used in front of an ELB)

Q: You would like to deliver big data streams in real time to multiple consuming applications, with replay features. Which technology do you recommend?
> Kinesis Data Streams (Kinesis Data Streams has all these features)

## AWS Development

 - https://docs.aws.amazon.com/cli/latest/reference/s3/
 - https://docs.aws.amazon.com/IAM/latest/UserGuide/tutorial_managed-policies.html
 - https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_examples.html
 - https://cloud.google.com/storage-transfer/docs/using-iam-permissions-and-roles
 - https://docs.amazonaws.cn/en_us/IAM/latest/UserGuide/id_roles_create_for-user.html
 - https://console.aws.amazon.com/iam
### AWS Development Q&A

Q: My EC2 Instance does not have the permissions to perform an API call PutObject on S3. What should I do?
> I should ask an administrator to attach a Policy to the IAM Role on my EC2 Instance that authorizes it to do the API call (IAM roles are the right way to provide credentials and permissions to an EC2 instance)

Q: I have an on-premise personal server that I'd like to use to perform AWS API calls
> I should run `aws configure` and put my credentials there. Invalidate them when I'm done (Even better would be to create a user specifically for that one on-premise server)

Q: I need my colleagues help to debug my code. When he runs the application on his machine, it's working fine, whereas I get API authorization exceptions. What should I do?
> Compare his IAM policy and my IAM policy in the policy simulator to understand the differences

Q: To get the instance id of my EC2 machine from the EC2 machine, the best thing is to...
> Query the meta data at http://169.254.169.254/latest/meta-data
