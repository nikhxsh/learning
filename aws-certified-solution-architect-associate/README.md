---


---

<h2 id="aws-regions-and-availability-zones">AWS regions and Availability zones</h2>
<ul>
<li>Regions
<ul>
<li>AWS has the concept of a Region, which is a physical location around the world where we cluster data centers</li>
<li>Each AWS Region consists of multiple, isolated, and physically separate AZs within a geographic area</li>
<li>AWS maintains multiple geographic regions like us-east1, us-west-2 etc.</li>
<li>Most AWS services are region scoped</li>
<li>How to choose Region?
<ul>
<li>Compliance with data governance and legality</li>
<li>Proximity to Customers</li>
<li>Available services within region - new service not available in every region</li>
<li>Pricing which varies from region to region</li>
</ul>
</li>
</ul>
</li>
<li>Availability Zones
<ul>
<li>Each region has many AZs (Usually 3, min 2, max 6)</li>
<li>Example:
<ul>
<li>ap-southeast-2a</li>
<li>ap-southeast-2b</li>
<li>ap-southeast-2c</li>
</ul>
</li>
<li>Each AZ is one or more discreet data centers with redundant power, network and connectivity</li>
<li>Each AZs separated from each other to isolate from disaster</li>
<li>Ability to operate production applications and databases that are more highly available, fault tolerant, and scalable than 	   would be possible from a single data center.</li>
<li>Each AZs connected with high bandwidth, low latency network</li>
<li>All traffic between AZs is encrypted</li>
</ul>
</li>
<li>Local Zones
<ul>
<li>Place compute, storage, database, and other select AWS services closer to end-users</li>
<li>You can easily run highly-demanding applications that require single-digit millisecond latencies</li>
</ul>
</li>
<li>Edge Network Locations
<ul>
<li>216 Point of Presence (205 edge locations &amp; 11 Regional cache) in 84 cities across countries</li>
<li>Content delivered with lower latency</li>
</ul>
</li>
<li>AWS Wavelength
<ul>
<li>Enables developers to build applications that deliver single-digit millisecond latencies to mobile devices and end-users 	  such as game and live video streaming, machine learning inference at the edge, and augmented and virtual reality</li>
<li>Application traffic can reach application servers running in Wavelength Zones without leaving the mobile provider’s	  network</li>
</ul>
</li>
<li>AWS Outposts
<ul>
<li>Bring native AWS services, infrastructure, and operating models to virtually any data center, co-location space, or on-premises facility</li>
<li>AWS Outposts is designed for connected environments and can be used to support workloads that need to remain on-premises due to low latency or local data processing needs</li>
</ul>
</li>
</ul>
<h2 id="iam-identity-and-access-management">IAM (Identity and Access Management)</h2>
<ul>
<li><a href="https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction.html">Docs</a></li>
<li>Global Service i.e. not scoped to regions</li>
<li>Helps you securely control access to AWS resources</li>
<li>Use IAM to control who is authenticated (signed in) and authorized (has permissions) to use resources</li>
<li>Root account created by default, shouldn’t be used or shared</li>
<li>You manage access in AWS by creating policies and attaching them to IAM identities (users, groups of users, or roles) or AWS resources</li>
<li><a href="https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users.html">Users</a>
<ul>
<li>People within your organization</li>
<li>Can belong to multiple groups</li>
<li>Can inherit multiple policies</li>
<li>IAM users are not separate accounts; they are users within your account</li>
<li>An IAM user with administrator permissions is not the same thing as the AWS account <a href="https://docs.aws.amazon.com/IAM/latest/UserGuide/id_root-user.html">root user</a></li>
<li>Use the ARN when you need to uniquely identify the user across all of AWS<br>
E.g. arn:aws:iam::<code>account-ID-without-hyphens</code>:user/nik</li>
</ul>
</li>
<li><a href="https://docs.aws.amazon.com/IAM/latest/UserGuide/id_groups.html">Groups</a>
<ul>
<li>Group is a collection of IAM users</li>
<li>Let you specify permissions for multiple users</li>
<li>Group is a way to attach policies to multiple users at one time</li>
<li>Groups can’t be nested i.e. they can contain only users, not other user groups</li>
</ul>
</li>
<li><a href="https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies.html">Policies</a>
<ul>
<li>A policy is an object in AWS that, when associated with an identity or resource, defines their permissions</li>
<li>Users and groups can be assigned JSON documents called policies</li>
<li>IAM policies define permissions for an action of the users</li>
<li>Apply least priviledge principle (dont give permission more than user needs)</li>
<li>Supports six types of policies
<ul>
<li>Identity-based policies
<ul>
<li>Grant permissions to an identity (users, groups to which users belong, or roles)</li>
<li>Identity-based policies grant permissions to an identity.</li>
</ul>
</li>
<li>Resource-based policies
<ul>
<li>Inline policies to resources</li>
<li>Resource-based policies grant permissions to the principal that is specified in the policy</li>
</ul>
</li>
<li>Permissions boundaries
<ul>
<li>Use a managed policy as the permissions boundary for an IAM entity</li>
<li>Defines the maximum permissions that the identity-based policies can grant to an entity, but does not grant				permissions</li>
</ul>
</li>
<li>Organizations SCPs
<ul>
<li>Define the maximum permissions for account members of an organization or organizational unit (OU).</li>
<li>SCPs limit permissions that identity-based policies or resource-based policies grant to entities (users or roles)			    within the account, but do not grant permissions.</li>
</ul>
</li>
<li>ACLs
<ul>
<li>Control which principals in other accounts can access the resource to which the ACL is attached.</li>
<li>Only policy type that does not use the JSON policy document structure ACLs are cross-account permissions policies			    that grant permissions to the specified principal.</li>
<li>ACLs cannot grant permissions to entities within the same account.</li>
</ul>
</li>
<li>Session policies
<ul>
<li>Pass advanced session policies when you use the AWS CLI or AWS API to assume a role or a federated user</li>
<li>Session policies limit permissions for a created session, but do not grant permissions</li>
</ul>
</li>
</ul>
</li>
</ul>
</li>
<li><a href="https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles.html">Roles</a>
<ul>
<li>An IAM Role is an identity that you can create in your account that has specific permissions</li>
<li>It is an AWS identity with permission policies that determine what the identity can and cannot do in AWS</li>
<li>When you assume a role, It provides you with temporary security credentials for your role session</li>
<li>You can use roles to delegate access to users, applications, or services that don’t normally have access to your AWS    resources e.g. Sometimes you want to give AWS access to users who already have identities defined outside of AWS, such as in your corporate directory. Or, you might want to grant access to your account to third parties so that they can perform an audit on your resources</li>
<li>Common roles
<ul>
<li>EC2 Instance role</li>
<li>Lambda function role</li>
<li>Roles for CloudFormation</li>
</ul>
</li>
</ul>
</li>
<li>Tools
<ul>
<li>Credentials Report (Account Level) that lists all your account’s users and status of various credentials</li>
<li>Access Advisor (User level) shows the service permissions granted to the user and when those accessed</li>
</ul>
</li>
<li>Best practices
<ul>
<li>Do not use root account</li>
<li>One AWS user = One Physical user</li>
<li>Assign users to groups and assign permission to groups</li>
<li>Create strong password policy</li>
<li>Use and enforce MFA</li>
<li>Create Roles for giving special permissions</li>
<li>Use Access keys for programmatic access (CLI/SDK)</li>
<li>Audit permissions of your account using Credentials Report</li>
<li>Never share IAM users and Access keys</li>
</ul>
</li>
</ul>
<hr>
<p>Q: You are getting started with AWS and your manager wants things to remain simple yet secure. He wants the management of engineers to be easy, and not re-invent the wheel every time someone joins your company. What will you do?</p>
<blockquote>
<p>I’ll create multiple IAM users and groups, and assign policies to groups. New users will be<br>
added to groups</p>
</blockquote>
<p>Q: What is a proper definition of IAM Roles?</p>
<blockquote>
<p>An IAM entity that defines a set of permissions for making AWS service requests, that will be used by AWS services (Some AWS service will need to perform actions on your behalf. To do so, you assign permissions to AWS services with IAM Roles.)</p>
</blockquote>
<p>Q: Which of the following is an IAM Security Tool?</p>
<blockquote>
<p>IAM Credentials Report (IAM Credentials report lists all your account’s users and the status of their various credentials. The other IAM Security Tool is IAM Access Advisor. It shows the service permissions granted to a user and when those services were last accessed.)</p>
</blockquote>
<p>Q: Which answer is INCORRECT regarding IAM Users?</p>
<blockquote>
<p>IAM Users access AWS with the root account credentials (IAM Users access AWS using a username and a password.)</p>
</blockquote>
<p>Q: Which of the following is an IAM best practice?</p>
<blockquote>
<p>Do not use root account (You only want to use the root account to create your first IAM user, and for a few account and service management tasks. For every day and administration tasks, use an IAM user with permissions.)</p>
</blockquote>
<p>Q: What are IAM Policies?</p>
<blockquote>
<p>JSON documents to define Users, Groups or Roles’ permissions (An IAM policy is an entity that, when attached to an identity or resource, defines their permissions.)</p>
</blockquote>
<p>Q: Which principle should you apply regarding IAM Permissions?</p>
<blockquote>
<p>Grant least privilege (Don’t give more permissions than the user needs.)</p>
</blockquote>
<p>Q: What should you do to increase your root account security?</p>
<blockquote>
<p>Enable MFA (You want to enable MFA in order to add a layer of security, so even if your password is stolen, lost or hacked your account is not compromised.)</p>
</blockquote>
<h2 id="ec2-elastic-compute-cloud">EC2 (Elastic Compute Cloud)</h2>
<ul>
<li><a href="https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/concepts.html">Docs</a></li>
<li>Provides scalable computing capacity in the Amazon Web Services (AWS) Cloud</li>
<li>Infrastructure as a Service (IaaS)</li>
<li>Main capabilities are
<ul>
<li>Renting Virtual Machines (EC2)</li>
<li>Storing data on virtual drives (EBS)</li>
<li>Distributing load across machines (ELB)</li>
<li>Scaling the service using an auto-scaling group (ASG)</li>
</ul>
</li>
<li>You can use Amazon EC2 to launch as many or as few virtual servers as you need, configure security and networking, and    manage storage</li>
<li>EC2 enables you to scale up or down to handle changes in requirements or spikes in popularity, reducing your need to    forecast traffic</li>
<li>Features
<ul>
<li>Virtual computing environments, known as  <em>instances</em></li>
<li>Preconfigured templates for your instances, known as <em>Amazon Machine Images (AMIs)</em>,</li>
<li>Secure login information for your instances using <em>key pairs</em></li>
<li>Storage volumes for temporary data that’s deleted when you stop, hibernate, or terminate your instance, known as	    <em>instance store volumes</em></li>
<li>Persistent storage volumes like Elastic Block Store (EBS)</li>
<li>A firewall</li>
<li>Static IPv4 addresses for dynamic cloud computing, known as  <em>Elastic IP addresses</em></li>
<li>Virtual networks you can create that are logically isolated from the rest of the AWS Cloud</li>
</ul>
</li>
<li><a href="https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/Instances.html">Instances</a>
<ul>
<li>An instance is a virtual server in the cloud</li>
<li>Its configuration at launch is a copy of the AMI that you specified when you launched the instance</li>
<li>Select an instance type based on the amount of memory and computing power that you need for the application or software that you plan to run on the instance</li>
<li><a href="https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-types.html">Instance types</a>
<ul>
<li>Instance types comprise varying combinations of CPU, memory, storage, and networking capacity and give you the flexibility to choose the appropriate mix of resources for your applications.</li>
<li>Each instance type includes one or more instance sizes, allowing you to scale your resources to the requirements of your target workload.</li>
<li>Overview
<ul>
<li><code>m5.2xlarge</code></li>
<li>m:instance class</li>
<li>5: generation</li>
<li>2xlarge: size within the instance class</li>
<li>Naming
<ul>
<li>R: applications that needs a lot of RAM (In-memory cache)</li>
<li>C: applications that needs good CPU (compute/db)</li>
<li>M: applications that are balanced (web-app)</li>
<li>I: applications that needs good local I/O (instance storage - db)</li>
<li>G: applications that needs a GPU (video rendering/ machine learning)</li>
</ul>
</li>
</ul>
</li>
<li>Types
<ul>
<li><a href="https://instances.vantage.sh/">Instance Comparison</a></li>
<li><a href="https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/general-purpose-instances.html">General Purpose</a>
<ul>
<li>Diversity of workloads such as web-servers or code repos</li>
<li>Balance between Compute, Memory and Networking</li>
<li>Use cases
<ul>
<li>M5 and M5a - balance of compute, memory, and networking resources
<ul>
<li>Small and midsize databases</li>
<li>Data processing tasks that require additional memory</li>
<li>Caching fleets</li>
<li>Backend servers for enterprise applications</li>
<li>E.g. <code>m5.large, m5a.16xlarge</code></li>
</ul>
</li>
<li>M5zn - extremely high single-thread performance, high throughput, and low latency networking
<ul>
<li>Gaming</li>
<li>High performance computing</li>
<li>E.g. <code>m5zn.large</code></li>
</ul>
</li>
<li>M6g and M6gd - balanced compute, memory, and networking
<ul>
<li>Application servers</li>
<li>Microservices</li>
<li>Gaming servers</li>
<li>Midsize data stores</li>
<li>E.g. <code>m6gd.medium</code></li>
</ul>
</li>
</ul>
</li>
<li><a href="https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/burstable-performance-instances.html">Burstable performance instances</a>
<ul>
<li>The T instance family provides a baseline CPU performance with the ability to burst above the baseline at any time for as long as required</li>
<li>The EC2 burstable instances consist of T4g, T3a and T3 instance types, and the previous generation T2 instance types</li>
<li>T instances are not supported on a Dedicated Host</li>
<li>Unlimited mode
<ul>
<li>Can sustain high CPU utilization for any period of time whenever required</li>
<li>The hourly instance price automatically covers all CPU usage spikes if the average CPU utilization of the instance is at or below the baseline over a rolling 24-hour period or the instance lifetime, whichever is shorter</li>
<li>T4g, T3a and T3 instances launch as <code>unlimited</code> by default</li>
</ul>
</li>
<li>Standard mode
<ul>
<li>Suited to workloads with an average CPU utilization that is consistently below the baseline CPU utilization of the instance</li>
<li>To burst above the baseline, the instance spends credits that it has accrued in its CPU credit balance</li>
<li>If the instance is running low on accrued credits, CPU utilization is gradually lowered to the baseline level, so that the instance does not experience a sharp performance drop-off when its accrued CPU credit balance is depleted</li>
</ul>
</li>
<li>Use cases
<ul>
<li>Websites and web applications</li>
<li>Code repositories</li>
<li>Development, build, test, and staging environments</li>
<li>Microservices</li>
</ul>
</li>
<li>E.g. <code>t4g.nano, t2.medium, t3.2xlarge</code></li>
</ul>
</li>
</ul>
</li>
<li><a href="https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/compute-optimized-instances.html">Compute optimized</a>
<ul>
<li>Optimized for compute-intensive tasks that require high performance</li>
<li>Use cases
<ul>
<li>Batch processing workloads</li>
<li>Media transcoding</li>
<li>High-performance web servers</li>
<li>High-performance computing (HPC)</li>
<li>Scientific modeling</li>
<li>Dedicated gaming servers and ad serving engines</li>
<li>Machine learning inference and other compute-intensive applications</li>
</ul>
</li>
<li>E.g. <code>C5 and C5d instances (c5.large, c5.metal, c5d.24xlarge, c5d.metal)</code></li>
</ul>
</li>
<li><a href="https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/memory-optimized-instances.html">Memory optimized</a>
<ul>
<li>Optimized for fast performance for workloads with large data set in memory</li>
<li>Use cases
<ul>
<li>High-performance, relational (MySQL) and NoSQL (MongoDB, Cassandra) databases</li>
<li>Distributed web scale cache stores (Memcached and Redis)</li>
<li>In-memory, optimized data storage formats and analytics for business intelligence (for example, SAP HANA)</li>
<li>Real-time processing of big unstructured data (financial services, Hadoop/Spark clusters)</li>
<li>High-performance computing (HPC) and Electronic Design Automation (EDA) applications.</li>
</ul>
</li>
<li>E.g. <code>R5, R5a, R5b, and R5n instances(r5.large, r5a.24xlarge, r5b.2xlarge, r5n.metal)</code></li>
</ul>
</li>
<li><a href="https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/storage-optimized-instances.html">Storage optimized</a>
<ul>
<li>Optimized for storage-intensive tasks that require high, sequential reads and write access to large data set on local storage</li>
<li>Use cases
<ul>
<li>High frequency online transaction processing systems</li>
<li>Relational &amp; NoSQL databases</li>
<li>Cache for in-memory</li>
<li>Data warehouse applications</li>
<li>Distributed files systems</li>
</ul>
</li>
<li>E.g. <code>D2 instances (d2.xlarge, d2.8xlarge)</code></li>
</ul>
</li>
</ul>
</li>
</ul>
</li>
<li><a href="https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-purchasing-options.html">Instance purchasing options</a>
<ul>
<li>On-demand
<ul>
<li>You pay for compute capacity by the second with no long-term commitments
<ul>
<li>Linux - billing per second, after first minute</li>
<li>All other OS billing per hour</li>
</ul>
</li>
<li>You pay for only when its running</li>
<li>Recommended for short-term and un-interrupted workloads</li>
<li>Highest costs but no upfront payment</li>
</ul>
</li>
<li>Reserved instances
<ul>
<li>Minimum 1 or 3 year</li>
<li>Up to 72-75% discount compare to on-demand but upfront payment</li>
<li>Long workloads</li>
<li>Recommended for steady state usage application like sql-server</li>
<li>Reserved Instances provide you with significant savings on your Amazon EC2 costs compared to On-Demand Instance pricing</li>
<li>Convertible Reserved
<ul>
<li>Enables you to <em>exchange</em> one or more Convertible Reserved Instances for another Convertible Reserved Instance with a different configuration, including instance family, operating system, and tenancy</li>
<li>Long workloads with flexible instances i.e. can change instance type</li>
<li>Upto 54% discount</li>
<li>There are no limits to how many times you perform an exchange</li>
<li>Cannot be sold in the Reserved Instance Marketplace</li>
</ul>
</li>
<li>Scheduled reserved
<ul>
<li>Launch within time window you reserve</li>
<li>When you required a fraction of day/week/month</li>
<li>Commitment over 1-3 years</li>
<li>E.g. every Thursday between 3 to 6 pm</li>
</ul>
</li>
</ul>
</li>
<li>Spot instances
<ul>
<li>Enable you to request unused EC2 instances at steep discounts</li>
<li>90% discount compare to on-demand (most cost efficient)</li>
<li>Define max spot price and get instance while current spont price &lt; max</li>
<li>Spot Instances are a cost-effective choice if you can be flexible about when your applications run and if your applications can be interrupted. For example, Spot Instances are well-suited for data analysis, batch jobs, background processing, and optional tasks</li>
<li>Not great for critical jobs or databases</li>
<li>Spot Instance request defines Maximum price per hour, desired number of instances, launch specification, request type  (one time/persistent), valid from to until</li>
<li><em>Instance that you own can “lose” at any point of time if your max price is less than the current spot price</em></li>
<li><a href="https://aws.amazon.com/blogs/aws/new-ec2-spot-blocks-for-defined-duration-workloads/">Spot block</a>
<ul>
<li>Block spot instance during specified frame (1-6 hours) without interruptions  (In rare situation instance may get reclaimed)</li>
<li>Pricing is based on the requested duration and the available capacity, and is typically 30% to 45% less than On-Demand, with an additional 5% off during non-peak hours for the region.</li>
</ul>
</li>
<li>Spot fleets
<ul>
<li>Spot fleet simplifies bidding by allowing you to select the number of vCPUs you need, and automatically launching the lowest priced Spot instances to request and maintain your desired capacity.</li>
<li>A Spot Fleet is set of Spot Instances and optionally On-Demand Instances that is launched based on criteria that you specify</li>
<li>Automatically request spot instances with lowest price</li>
<li>Can have multiple launch pools so that the fleet can choose</li>
<li>Stops launching instances when reaching capacity or max</li>
<li>Extra saving</li>
</ul>
</li>
<li>Good for workloads which are resilient to failures</li>
<li>Use cases
<ul>
<li>Batch jobs</li>
<li>Data analysis</li>
<li>Image processing</li>
<li>Any distributed workloads</li>
<li>workloads with flexible times</li>
</ul>
</li>
</ul>
</li>
<li><a href="https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/dedicated-hosts-overview.html">Dedicated hosts</a>
<ul>
<li>Physical dedicated EC2 server</li>
<li>Full control of instance placement</li>
<li>Visibility into underlying sockets, cores</li>
<li>Allocated for 3 years</li>
<li>More Expensive</li>
<li>Useful for software with complicated licensing model (BYOL - Bring Your Own Licence) or companies with strong regulatory and compliance needs</li>
<li>no other customer will share your hardware</li>
</ul>
</li>
<li><a href="https://aws.amazon.com/ec2/pricing/dedicated-instances/">Dedicated instances</a>
<ul>
<li>Run in a VPC on hardware that’s dedicated to a single customer</li>
<li>Enable you to use your existing server-bound software licenses like Windows Server and address corporate compliance and regulatory requirement</li>
<li>May share hardware with other instances in a same account</li>
<li>No control over instance placement</li>
<li>Per instance billing</li>
</ul>
</li>
</ul>
</li>
<li><a href="https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-instance-lifecycle.html">Instance lifecycle</a>
<ul>
<li><a href="https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/Hibernate.html">EC2 Hibernate</a>
<ul>
<li>Enable hibernation while instance launch</li>
<li>When you stop instance the data on disk (EBS) keep intact in the next start</li>
<li>When you terminate EBS volume (root set-up to be destroyed)</li>
<li>When you start OS boots up and ec2 used data script is run then application starts and can take time</li>
<li>When you hibernate then
<ul>
<li>In-memory (RAM) state is preserved i.e. RAM state is written to a file in the root EBS volume</li>
<li>The root EBS volume must be encrypted</li>
<li>Useful for long running processing or service that take time to initialize</li>
<li>Instance RAM must be less than 150 gb</li>
<li>Supported by Amazon linux 2, linux AMI Ubantu and windows</li>
<li>Can not be hibernated for more than 60 days</li>
</ul>
</li>
</ul>
</li>
</ul>
</li>
</ul>
</li>
<li><a href="https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-instances-and-amis.html">AMI (Amazon Machine Image)</a>
<ul>
<li>Template that contains a software configuration (for example, an operating system, an application server, and	    applications)</li>
<li>From an AMI, you launch an <em>instance</em>, which is a copy of the AMI running as a virtual server in the cloud
<ul>
<li>An instance is a virtual server in the cloud</li>
<li>Your instances keep running until you stop, hibernate, or terminate them, or until they fail</li>
<li>AWS account has a limit on the number of instances that you can have running</li>
<li>The root device for your instance contains the image used to boot the instance. The root device is either an EBS		   volume or an instance store volume</li>
</ul>
</li>
<li>You can launch multiple instances of an AMI</li>
<li>Built for specific region only (and can be copied across regions)</li>
<li>By default its private and locked for your account/region</li>
<li>You can make them public OR sell them on the AMI marketplace</li>
<li>Marketplace AMIs
<ul>
<li>AMI someone else made (selling purpose)</li>
</ul>
</li>
<li>Custom AMIs
<ul>
<li>Faster boot time</li>
<li>Pre-installed packages needed</li>
<li>Comes with configuration</li>
<li>Control maintenance</li>
<li>Control over network</li>
<li>Active directory integration</li>
</ul>
</li>
<li>Public AMIs
<ul>
<li>Can use already tested and optimized AMI for other people rent from other</li>
<li>Storage (live in S3)</li>
<li>Pricing - lives on S3 so charged for space in takes on S3</li>
<li>To copy an AMI that was shared with you from another account, the owner of the source AMI must grant you read permission for the storage that backs the AMI, either the associated EBS snapshot (EBS backend EMI) or associated S3 bucket (instance store backed AMI)</li>
<li>You cant copy encrypted AMI that shared with you instead can copy underlaying snapshot and en ket shared with you and can register it as new AMIs</li>
<li>Can’t copy AMI with an associated billing product code. To copy launch an EC2 instance from your account using shared AMI then create an AMI from instance</li>
</ul>
</li>
<li>Free tier Amazon linux 2, t2.micro</li>
<li>Subnet: In what AZ you want instance</li>
<li>Key-Pair (Pem) file ()</li>
<li>Connect ec2 using SSH (Mac/Linux), Putty(Window), EC2 Connect</li>
<li>Golden AMI
<ul>
<li>Install your application, os dependencies etc. beforehand and launch your EC2 instance from it</li>
</ul>
</li>
<li>After you deregister an AMI, you can’t use it to launch new instances, Existing instances launched from the AMI are not affected</li>
</ul>
</li>
<li><a href="https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-networking.html">Networking</a>
<ul>
<li>Virtual private cloud (VPC) enables you to launch AWS resources, such as Amazon EC2 instances, into a virtual network dedicated to your AWS account,</li>
<li>Private vs public IP (V4)
<ul>
<li>IPv6 is hexadecimal string, newer and solves problems for the IoT</li>
<li>IPv4 allows 3.7 B addresses in the public place</li>
<li>Public IP mean
<ul>
<li>Machine can be identified on internet(WWW)</li>
<li>Must be unique and can be geolocated</li>
</ul>
</li>
<li>Private IP
<ul>
<li>Identified by private network</li>
<li>Unique across the private network</li>
<li>2 different private network can have same IP</li>
<li>Machine connect to WWW using internet gateway or proxy</li>
<li>Specified ranges used only as private IP</li>
</ul>
</li>
<li>Elastic IP
<ul>
<li>When you restart EC2 instance it can change its public IP so to have fixed public IP you need elastic IP (you own it until you delete)</li>
<li>You can attach to one instance at a time</li>
<li>You can mask failure of an instance by remapping the address to another instance but you can only have 5 Elastic IP per account (You can ask AWS to increase that limit)</li>
<li>Try avoid using EIP, instead use random public IP and register a DNS name to it or load balancer</li>
</ul>
</li>
</ul>
</li>
<li><a href="https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-eni.html">Elastic network interfaces</a>
<ul>
<li>Logical component in VPC that represent a virtual network card</li>
<li>Gives EC2 instance access to network</li>
<li>Each ENI has
<ul>
<li>Private IPv4</li>
<li>One/more secondary IPv4/V6</li>
<li>One EIP per private IPv4</li>
<li>One public IPv4</li>
<li>One/more SG</li>
<li>A MAC address</li>
</ul>
</li>
<li>Can create ENI independently attach them on the fly (move them) on EC2 instances for failover</li>
<li>Bound to specific AZ</li>
</ul>
</li>
<li><a href="https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/placement-groups.html">Placement group</a>
<ul>
<li>You can use <em>placement groups</em> to influence the placement of a group of <em>interdependent</em> instances to meet the needs of your workload.</li>
<li>Depending on the type of workload, you can create a placement group using one of the following placement strategies</li>
<li>Cluster
<ul>
<li>Cluster instances into low latency group in a single AZ</li>
<li>Same rack (hardware) single AZ</li>
<li>Pros
<ul>
<li>Great network (10 Gbps)</li>
</ul>
</li>
<li>Cons
<ul>
<li>If rack fails, all instances fails at the same time</li>
</ul>
</li>
<li>Use cases
<ul>
<li>Big data job to complete faster</li>
<li>App that need extremely low latency and high network throughput</li>
</ul>
</li>
</ul>
</li>
<li>Spread
<ul>
<li>Spread instances across underlying hardware (max 7 instances/AZ) for critical applications</li>
<li>Each EC2 instance in different AZ</li>
<li>Pros
<ul>
<li>Reduced risk of simultaneous failure as EC2 instance in different hardware</li>
<li>Can span across AZs</li>
</ul>
</li>
<li>Cons
<ul>
<li>Limited to 7 instances</li>
</ul>
</li>
<li>Use cases
<ul>
<li>Application that needs to maximize high availability</li>
<li>Critical application where each instance must be isolated from failure from each other</li>
</ul>
</li>
</ul>
</li>
<li>Partition
<ul>
<li>Spread instances across many different partitions within AZ (max 7 partitions per AZ)</li>
<li>Scales to 100s of EC2 instances per group (Hadoop, cassandra, kafka)</li>
<li>Instance does not share racks with the instances in the other partition</li>
<li>Partition failure won’t affect other partitions</li>
<li>For HDFS, HBase, cassandra, kafka</li>
</ul>
</li>
</ul>
</li>
</ul>
</li>
<li><a href="https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/infrastructure-security.html">Infrastructure security</a>
<ul>
<li>As a managed service, Amazon EC2 is protected by the AWS global network security procedures</li>
<li>IAM management
<ul>
<li>Use IAM to allow other users, services, and applications to use your Amazon EC2 resources without sharing your security credentials</li>
<li>By using IAM with Amazon EC2, you can control whether users in your organization can perform a task using specific Amazon EC2 API actions and whether they can use specific AWS resources</li>
</ul>
</li>
<li>Key pairs
<ul>
<li>A key pair, consisting of a public key and a private key, is a set of security credentials that you use to prove your identity when connecting to an EC2 instance</li>
<li>Amazon EC2 stores the public key on your instance</li>
<li>When your instance boots for the first time, the public key that you specified at launch is placed on your Linux instance in an entry within <code>~/.ssh/authorized_keys</code></li>
<li>When you connect to your Linux instance using SSH, to log in you must specify the private key that corresponds to the public key</li>
<li>The keys that Amazon EC2 uses are 2048-bit SSH-2 RSA keys. You can have up to 5,000 key pairs per Region</li>
</ul>
</li>
<li>Security Groups
<ul>
<li>Control inbound/outbound traffic using ports</li>
<li>Add rules using different types  (Eg SSH - TCP - 22 - Custom - 0.0.0.0/0 - SSH allowed<br>
from anywhere) it allows to connect using ssh</li>
<li>Firewall to EC2 instance</li>
<li>They regulate access to ports, authorised IP ranges (IPv4/V6), control inbound network<br>
(other to the instance), outbound network(from instance to other)</li>
<li>Can be attached to multiple instances</li>
<li>Locked down to a region or VPC combination</li>
<li>Lives outside the ec2</li>
<li>Good to maintain separate group for SSH access</li>
<li>Timeout &gt; probable SG issue</li>
<li>Connection refused &gt; application error</li>
<li>By default all inbound traffic is blocked</li>
<li>By default all outbound traffic is authorised</li>
<li>2 different EC2 can connect by authorising each other’s SG in inbound rules</li>
</ul>
</li>
</ul>
</li>
<li><a href="https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/Storage.html">EC2 Storage</a>
<ul>
<li>Amazon EC2 provides you with flexible, cost effective, and easy-to-use data storage options for your instances</li>
<li><a href="https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AmazonEBS.html">Amazon Elastic Block Store</a>
<ul>
<li>Is a network drive you can attach to your instance while they run</li>
<li>It allows your instance to persist data even after termination</li>
<li>Can only be mounted to one instance at a time</li>
<li>Bound to specific AZ</li>
<li>Think of them as a “network USB stick”</li>
<li>EBS Volume
<ul>
<li>Its a network drive (not physical one)
<ul>
<li>It uses the network to communicate the instance, means bit of latency</li>
<li>Can be detached from one EC2 instance and attached to another one quickly</li>
</ul>
</li>
<li>Its locked to an AZ
<ul>
<li>An EBS volume in us-east-1a a can’t be attached to us-east-1b</li>
<li>To move a volume across, you first need to snapshot it</li>
</ul>
</li>
<li>Has provisioned capacity (Size in GBs, &amp; IOPS)
<ul>
<li>Billed for all the provisioned capacity</li>
<li>Can increase the capacity of the drive over time</li>
</ul>
</li>
<li>Delete on Termination attribute
<ul>
<li>Controls the EBS behaviour when an instance terminates
<ul>
<li>By default, the root EBS volume is deleted (attribute enabled)</li>
<li>By default, any other attached EBS volume is not deleted (attribute enabled)</li>
</ul>
</li>
<li>Use case is to preserve root volume when instance is terminated</li>
</ul>
</li>
<li>EBS Snapshot
<ul>
<li>Backup of EBS volume at a point in time</li>
<li>Not need to detach volume to do snapshot</li>
<li>Can copy snapshots across AZ or region</li>
</ul>
</li>
<li>EBS volumes are network drives with good but limited performance
<ul>
<li>If you need a high-performance hardware disk, use local EC2 instance store</li>
<li>Better I/O performance</li>
<li>Good throughput</li>
<li>But EC2 Instance Store lose their storage if they’re stopped</li>
</ul>
</li>
<li>Volume Types
<ul>
<li>EBS volumes are characterized in Size | Throughput | IOPS (i/o per sec)</li>
<li>General Purpose SSD
<ul>
<li>General purpose SSD volume</li>
<li>Balances price and performance  for wide variety of workloads</li>
<li>Cost effective storage with low latency</li>
<li>Used for System boot volumes, virtual desktops, dev and test environments</li>
<li>1GiB to 16TiB</li>
<li>gp3
<ul>
<li>New generation</li>
<li>Baseline of 3000 IOPS and throughput of 125 MiB/s</li>
<li>Can increase IOPS up to 16000 and throughput up to 1000 MiB/s</li>
</ul>
</li>
<li>gp2
<ul>
<li>Small volumes can burst IOPS to 3000</li>
<li>Size of the volumes and IOPS are linked, max IOPS is 16000</li>
<li>3 IOPS per GB, means at 5334 GB we are at the max IOPS</li>
</ul>
</li>
</ul>
</li>
<li>Provisioned IOPS (PIOPS) SSD
<ul>
<li>Highest-performance SSD volume</li>
<li>For mission critical, low latency or high throughput workloads</li>
<li>Great database workloads</li>
<li>For application that need more than 16000 IOPS</li>
<li>io1/io2
<ul>
<li>4GiB -16 TiB</li>
<li>Max PIOPS 64000 for Nitro EC2 &amp; 32000 for other</li>
<li>Can increase PIOPS independently from storage size</li>
<li>io2 have more durability and more IOPS per GiB (at same price as io1)</li>
</ul>
</li>
<li>io2 block express
<ul>
<li>Sub-millisecond latency</li>
<li>Max PIOPS 256000 with an IOPS:GiB ratio 100:1</li>
</ul>
</li>
</ul>
</li>
<li>Hard Disk Drives (HDD)
<ul>
<li>Can not be a boot volume</li>
<li>125 MiB to 16 TiB</li>
<li>st1 (Throughput Optimized HDD)
<ul>
<li>Low cost HDD volume</li>
<li>Designed for frequently accessed, throughput intensive workloads</li>
<li>Max throughput 500 MiB/s - max IOPS 500</li>
</ul>
</li>
<li>sc1 (Cold HDD)
<ul>
<li>Lowest cost HDD volume</li>
<li>Designed for less frequently accessed workloads</li>
<li>Max throughput 250 MiB/s - max IOPS 250</li>
</ul>
</li>
</ul>
</li>
<li>Only gp2/gp3 and io1/io2 can be used as boot volumes</li>
</ul>
</li>
<li>EBS Multi-Attach
<ul>
<li>io1/io2 family</li>
<li>Attach same EBS volume to multiple EC2 instances in the same AZ</li>
<li>Each instance has full read &amp; write permissions to the volume</li>
<li>Use case
<ul>
<li>Achieve higher application availability in clustered Linux application</li>
<li>Application must manage concurrent write operation</li>
</ul>
</li>
<li>Must use a file system that’s cluster aware</li>
</ul>
</li>
<li>EBS Encryption
<ul>
<li>When you create an encrypted EBS volume
<ul>
<li>Data at rest is encrypted inside the volume</li>
<li>All data in flight moving between the instance and the volume is encrypted</li>
<li>All snapshots are encrypted</li>
<li>All volumes created form the snapshot</li>
</ul>
</li>
<li>Encryption and decryption are handled transparently</li>
<li>Has minimal impact on latency</li>
<li>Leverage keys form KMS</li>
<li>Copying an unencrypted snapshot allows encryption
<ul>
<li>Create an EBS snapshot from volume</li>
<li>Encrypt the EBS snapshot (using copy)</li>
<li>Create new EBS volume from snapshot (the volume will also be encrypted)</li>
<li>Now attach the encrypted volume to the original instance</li>
</ul>
</li>
</ul>
</li>
<li><a href="https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/raid-config.html">EBS RAID Options</a>
<ul>
<li>Want to increase IOPS</li>
<li>Want to mirror your EBS volumes</li>
<li>Would mount volumes in parallel in RAID settings</li>
<li>RAID is possible as long as your OS supports it</li>
<li>RAID 0 (Increase performance)
<ul>
<li>Combining 2 or more volumes and letting total disk space and I/O</li>
<li>But if one disk fails, all the data is failed</li>
<li>Use case
<ul>
<li>Application that needs a lot of IOPS and doesn’t needs fault-tolerance</li>
<li>A database that has replication already built-in</li>
</ul>
</li>
<li>We can have very big disk with lot of IOPS</li>
<li>E.g. Two 5GiB EBS io1 volumes with 4000 provisioned IOPS will create 1000 GiB RAID 0 array with available bandwidth of 8000 IOPS and 1000 MiB/s of throughput</li>
</ul>
</li>
<li>RAID 1 (Increase fault tolerance)
<ul>
<li>Mirroring a volume to another</li>
<li>If one disk fails, or logical volume is still working</li>
<li>Have to send the data to two EBS volume at the same time</li>
<li>Use cases
<ul>
<li>Application that need increase volume fault tolerance</li>
<li>Application where you need to service disks</li>
<li>E.g. Two 500 GiB EBS io1 volumes with 4000 provisioned IOPS will create 500 GiB RAID 1 array with available bandwidth of 4000 IOPS and 500 MiB/s of throughput</li>
</ul>
</li>
</ul>
</li>
</ul>
</li>
</ul>
</li>
</ul>
</li>
<li><a href="https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AmazonEFS.html">Amazon Elastic Files System</a>
<ul>
<li>Shared network file system</li>
<li>Managed NFS (Network file system) that can be mounted on many EC2</li>
<li>EFS works with EC2 instances in multi-AZ</li>
<li>Highly available, scalable, expensive (3 times gp2), pay per use</li>
<li>Use cases
<ul>
<li>Content management</li>
<li>Web serving</li>
<li>Data sharing</li>
</ul>
</li>
<li>Uses NFSv4.1 protocol</li>
<li>Use SG to control access</li>
<li>Compatible with Linux based AMI</li>
<li>Encryption at rest using KMS</li>
<li>Scales automatically, no capacity planning</li>
<li>EFS Scale
<ul>
<li>1000s of concurrent NFS clients, 10 GiB/s throughput</li>
<li>Grow to Petabyte-scale network file system, automatically</li>
</ul>
</li>
<li>Performance mode
<ul>
<li>General purpose
<ul>
<li>Ideal for latency sensitive use case like Web serving environments or Content management Delivery</li>
</ul>
</li>
<li>Max I/O
<ul>
<li>Scale to higher levels of aggregate throughput and operation per second</li>
<li>higher latency, throughput, highly parallel for big data, media processing</li>
</ul>
</li>
</ul>
</li>
<li>Throughput mode
<ul>
<li>Bursting
<ul>
<li>Throughput scales with the file system size</li>
<li>1TB=50MiB/s + burst of up to 100 MiB/s</li>
</ul>
</li>
<li>Provisioned
<ul>
<li>Throughput fixed at specified amount</li>
<li>Set your throughput regardless of storage size e.g. I GiB for 1TB storage</li>
</ul>
</li>
</ul>
</li>
<li>Storage Tiers
<ul>
<li>Lifecycle management feature</li>
<li>Move file after N days</li>
<li>Standard - for frequently accessed files</li>
<li>Infrequent access (EFS-IA) - cost to retrieve files, lower price to store</li>
</ul>
</li>
</ul>
</li>
<li><a href="https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/InstanceStorage.html">Instance Store</a>
<ul>
<li>Many instances can access storage from disks that are physically attached to the host computer</li>
<li>This disk storage is referred to as <em>instance store</em></li>
<li>An <em>instance store</em> provides temporary block-level storage for your instance</li>
<li>This storage consists of a preconfigured and pre-attached block of disk storage on the same physical server that hosts the EC2 instance for which the block provides storage.</li>
<li>The amount of the disk storage provided varies by EC2 instance type</li>
<li>The data on an instance store volume persists only during the life of the associated instance; if you stop, hibernate, or terminate an instance, any data on instance store volumes is lost</li>
<li>Maximum amount of I/O</li>
<li>Instance store is ideal for
<ul>
<li>Temporary storage of information that changes frequently, such as buffers, caches, scratch data, and other temporary content</li>
<li>For data that is replicated across a fleet of instances, such as a load-balanced pool of web servers</li>
</ul>
</li>
</ul>
</li>
<li>EFS vs EBS
<ul>
<li>EBS
<ul>
<li>Can be attached to only one instance at a time</li>
<li>Locked at the AZ level</li>
<li>gp2: IO increase if the disk size increase</li>
<li>io1 can increase IO independently</li>
<li>Has snapshot options that can be restored to another AZ</li>
<li>If root EBS volumes of instance get terminated by default if the EC2 instances gets terminated (you can disable this feature)</li>
</ul>
</li>
<li>EFS
<ul>
<li>Can be mounted on 100s of instances across AZ</li>
<li>Shares website files</li>
<li>Only for Linux Instances</li>
<li>Hight price point than EBS</li>
<li>Can leverage EFS-IA for cost saving</li>
</ul>
</li>
</ul>
</li>
</ul>
</li>
<li>Advanced Concepts
<ul>
<li>EC2 Nitro
<ul>
<li>Underlying platform for next gen EC2 instances</li>
<li>New virtualization tech</li>
<li>Allows for better performance
<ul>
<li>Better networking options (enhanced networking, HPC, IPv6)</li>
<li>Higher Speed EBS (64000 EBS IOPS for Nitro, max 32000 for non-Nitro)</li>
</ul>
</li>
<li>Better underlying security</li>
<li>Instance types using Nitro are A1, C5, G4, I3en, M5a etc. and Bare metal</li>
</ul>
</li>
<li>vCPU
<ul>
<li>Multiple thread can run on one CPU</li>
<li>Each thread represent Virtual CPU</li>
<li>Example: <code>m5.2xlarge</code>
<ul>
<li>4 CPU</li>
<li>2 threads per CPU</li>
<li>So 8 vCPUs</li>
</ul>
</li>
<li>EC2 instance come with combination of RAM and vCPU</li>
<li>Some case you may want to changes vCPU options
<ul>
<li>Decrease CPU cores (Need high RAM and low number of CPU) to decrease licensing costs</li>
<li>Decrease threads per core (disable multithreading to have 1 thread per CPU) for high performace computing workloads</li>
</ul>
</li>
<li>Only specified during instance launch</li>
</ul>
</li>
<li>Capacity reservation
<ul>
<li>Ensure you have EC2 capacity when needed</li>
<li>Manual or planned end-date for the reservation</li>
<li>No need for 1-3 years commitment</li>
<li>Specify
<ul>
<li>The AZ in which to reserve the capacity</li>
<li>The number of instances for which to reserve the capacity</li>
<li>The instance attribute including instance type, tenancy etc.</li>
</ul>
</li>
<li>Combined with Reserved Instances and Savings Plans to do cost savings</li>
</ul>
</li>
</ul>
</li>
<li>Notes
<ul>
<li>Billed by the second, t2.micro is free tier</li>
<li>SSH on Linux/Mac, Putty on window</li>
<li>SSH on port 22</li>
<li>SG can reference other SG instead of IP ranges</li>
</ul>
</li>
<li>Dev
<ul>
<li>Provide credentials to EC2 from IAM role only</li>
<li>EC2 user data
<ul>
<li>Bootstrap instance using user data script which run once at first start</li>
<li>Used to automate boot tasks such as installing updates/ software, download files etc.</li>
<li>Check if apache installed</li>
<li>Create machine &gt; advance details &gt; user data<br>
#!/bin/bash<br>
.<br>
.<br>
user commands</li>
<li>Review and Launch</li>
</ul>
</li>
<li>SSH (Secure Shell - The SSH protocol uses encryption to secure the connection between a client and a server)  Port 22 over www
<ul>
<li>connect to public ip and port 22, allowed by SG</li>
<li>chmod 0400 pemfile.pem (protects it by making it read only and only for the owner,<br>
protect a file against accidental overwriting)<br>
Note: For window, go to property of pem file &gt; security &gt; add yourself owner and<br>
remove rest &gt; apply and save</li>
<li>ssh -i pemfile.pem ec2-user@publicIp</li>
<li>whoami</li>
<li>exit</li>
</ul>
</li>
<li>Putty
<ul>
<li>If SSH not supported</li>
<li>Import pem files to Putty key generator and save private key</li>
<li>open Putty program, add host name and port, link private key (SSH&gt; Auth &gt; PPK) and open instance</li>
</ul>
</li>
<li>EC2 instance connect
<ul>
<li>From browser connect option select EC2 instance connect</li>
<li>Enter user name</li>
<li>Click on connect</li>
<li>Make sure to have port 22 open through inbound rules</li>
</ul>
</li>
<li>When you have stopped your EC2 instance and then started it again today. When you do so,<br>
the public IP of your EC2 instance will change</li>
<li>Launch Apache server<br>
//Get public Ip<br>
//login to EC@ instance<br>
yum update -y<br>
yum install httpd.x86_64<br>
systemctl start httpd.service<br>
systemctl enable httpd.service<br>
curl localhost:80<br>
//can not access IP through browser unless (HTTP - TCP - 80) in inboud rules<br>
echo “Hello World from $(host -f)” &gt; /var/www/html/index.html<br>
//check browser</li>
<li>EC2 Instance Metadata
<ul>
<li>Allows EC2 instance to “learn about themselves” without using IAM Role for that purpose</li>
<li>The URL is <a href="http://169.254.169.254/latest/meta-data">http://169.254.169.254/latest/meta-data</a> (Worked only from instances)</li>
<li>Can retrieve the IAM Role name from the metadata, can not retrieve IAM policy
<ul>
<li>
<p>curl <a href="http://169.254.169.254">http://169.254.169.254</a></p>
</li>
<li>
<p>curl <a href="http://169.254.169.254/latest">http://169.254.169.254/latest</a></p>
<blockquote>
<p>meta-data<br>
user-data</p>
</blockquote>
</li>
<li>
<p><a href="http://169.254.169.254/latest/meta-data">http://169.254.169.254/latest/meta-data</a></p>
<blockquote>
<p>… All metadata …</p>
</blockquote>
</li>
</ul>
</li>
</ul>
</li>
</ul>
</li>
</ul>
<hr>
<p>Q: You are launching an EC2 instance in us-east-1 using this Python script snippet: (we will see SDK in a later section, for now just look at the code reference ImageId) ec2.create_instances(ImageId=‘ami-b23a5e7’, MinCount=1, MaxCount=1) It works well, so you decide to deploy your script in us-west-1 as well. There, the script does not work and fails with “ami not found” error. What’s the problem?</p>
<blockquote>
<p>AMI is region locked and the same ID cannot be used across regions</p>
</blockquote>
<p>Q: You would like to deploy a database technology and the vendor license bills you based on the  physical cores and underlying network socket visibility. Which EC2 launch modes allow you to get visibility into them?</p>
<blockquote>
<p>Dedicated Hosts</p>
</blockquote>
<p>Q: You are launching an application on EC2 and the whole process of installing the application  takes about 30 minutes. You would like to minimize the total time for your instance to boot up and be operational to serve traffic. What do you recommend?</p>
<blockquote>
<p>Create an AMI after installing apps and launch from the AMI (Creating an AMI after installing the applications allows you to start more EC2 instances directly from that AMI, hence bypassing the need to install the application (as it’s already installed))</p>
</blockquote>
<p>Q: You are running a critical workload of three hours per week, on Monday. As a solutions architect, which EC2 Instance Launch Type should you choose to maximize the cost savings  while ensuring the application stability?</p>
<blockquote>
<p>Scheduled Reserved Instances</p>
</blockquote>
<p>Q: You would like to make sure your EC2 instances have the highest performance while talking to  each other as you’re performing big data analysis. Which placement group should you choose?</p>
<blockquote>
<p>Cluster (Cluster placement groups places your instances next to each other giving you high performance computing and networking)</p>
</blockquote>
<p>Q: You plan on running an open-source MongoDB database year-round on EC2. Which instance launch mode should you choose?</p>
<blockquote>
<p>Reserved Instances (This will allow you to save cost as you know in advance that the instance will be a up for a full year)</p>
</blockquote>
<p>Q: You built and published an AMI in the ap-southeast-2 region, and your colleague in us-east-1 region cannot see it</p>
<blockquote>
<p>An AMI created for a region can only seen in that region</p>
</blockquote>
<p>Q: Which EC2 Purchasing Option can provide the biggest discount, but is not suitable for critical jobs or databases?</p>
<blockquote>
<p>Spot Instances (Spot Instances are good for short workloads, but are less reliable.)</p>
</blockquote>
<p>Q: Which network security tool can you use to control traffic in and out of EC2 Instances?</p>
<blockquote>
<p>Security Groups (Security Groups operate at instance level and can control traffic.)</p>
</blockquote>
<p>Q: How long can you reserve an EC2 Reserved Instance?</p>
<blockquote>
<p>1 year or 3 years terms are available for EC2 Reserved Instances</p>
</blockquote>
<p>Q: A company would like to deploy a high-performance computing (HPC) application on EC2. Which EC2 instance type should it choose?</p>
<blockquote>
<p>Compute Optimized (Compute Optimized EC2 instances are great for compute-intensive workloads requiring high performance processors, such as batch processing, media transcoding, high performance web servers, high performance computing, scientific modelling &amp; machine learning, and dedicated gaming servers)</p>
</blockquote>
<p>Q: Which EC2 Purchasing Option should you use for an application you plan on running on a server continuously for 1 year?</p>
<blockquote>
<p>Reserved Instances (Reserved Instances are good for long workloads. You can reserve instances for 1 or 3 years)</p>
</blockquote>
<p>Q: Your instance in us-east-1a just got terminated, and the attached EBS volume is now available. Your colleague tells you he can’t seem to attach it to your instance in us-east-1b.</p>
<blockquote>
<p>EBS Volumes are AZ locked (EBS Volumes are created for a specific AZ. It is possible to migrate them between different AZ through backup and restore)</p>
</blockquote>
<p>Q: You have provisioned an 8TB gp2 EBS volume and you are running out of IOPS. What is NOT a way to increase performance?</p>
<blockquote>
<p>Increase the EBS volume size (EBS IOPS peaks at 16,000 IOPS. or equivalent 5334 GB.)</p>
</blockquote>
<p>Q: You would like to leverage EBS volumes in parallel to linearly increase performance, while accepting greater failure risks. Which RAID mode helps you in achieving that?</p>
<blockquote>
<p>RAID 0</p>
</blockquote>
<p>Q: Although EBS is already a replicated solution, your company SysOps advised you to use a RAID mode that will mirror data and will allow your instance to not be affected if an EBS volume entirely fails. Which RAID mode did he recommend to you?</p>
<blockquote>
<p>RAID 1</p>
</blockquote>
<p>Q: You would like to have the same data being accessible as an NFS drive across AZ on all your EC2 instances. What do you recommend?</p>
<blockquote>
<p>Mount EFS (EFS is a network file system (NFS) and allows to mount the same file system on EC2 instances that are in different AZ)</p>
</blockquote>
<p>Q: You would like to have a high-performance cache for your application that mustn’t be shared. You don’t mind losing the cache upon termination of your instance. Which storage mechanism do you recommend as a Solution Architect?</p>
<blockquote>
<p>Instance Store (Instance Store provide the best disk performance)</p>
</blockquote>
<p>Q: You are running a high-performance database that requires an IOPS of 210,000 for its underlying filesystem. What do you recommend?</p>
<blockquote>
<p>Instance Store (Is running a DB on EC2 instance store possible? It is possible to run a database on EC2. It is also possible to use instance store, but there are some considerations to have. The data will be lost if the instance is stopped, but it can be restarted without problems. One can also set up a replication mechanism on another EC2 instance with instance store to have a standby copy. One can also have back-up mechanisms. It’s all up to how you want to set up your architecture to validate your requirements. In this case, it’s around IOPS, and we build an architecture of replication and back up around it)</p>
</blockquote>
<h2 id="scalability-and-high-availability">Scalability and High Availability</h2>
<ul>
<li><strong>Scalability</strong>
<ul>
<li>Means that application/system can handle greater loads by adapting</li>
<li>Vertical
<ul>
<li>Mean increasing the size of instances (scale up/down)</li>
<li>If your application runs on t2.micro then scaling vertically means running it on t2.large</li>
<li>Very common for non distributed systems such as database</li>
<li>RDS, ElastiCache are services that can scale vertically</li>
<li>Has hardware limits</li>
</ul>
</li>
<li><strong>Horizontal</strong>
<ul>
<li>Increasing number of instances / systems for application (scale out/in)</li>
<li>Implies distributed systems</li>
<li>Very common for web and modern applications</li>
<li>Easy to configure due to cloud offering such as EC2</li>
<li>Achieved by Increasing number of instances (with the help of Auto Scaling Groups (ASG) and Load Balancer)</li>
</ul>
</li>
</ul>
</li>
<li><strong>High Availability</strong>
<ul>
<li>Goes hand in hand with Horizontal Scaling</li>
<li>Running your application in at least 2 data centres</li>
<li>Goal is to survive a data center loss</li>
<li>Can be passive (for RDS Multi AZ) or can be active (for horizontal scaling)</li>
<li>Achieved by running instances for same application across Multi-AZ (with the help of Auto Scaling Groups (ASG) and Load Balancer Multi-AZ)</li>
</ul>
</li>
<li><a href="https://docs.aws.amazon.com/AmazonECS/latest/developerguide/service-load-balancing.html">Load Balancer</a>
<ul>
<li>Servers that forward internet traffic to multiple servers or backend EC2 instances</li>
<li>Can expose single point of access (DNS)</li>
<li>Seamlessly handle failure downstream instances</li>
<li>Do regular checks on your instances</li>
<li>Enforce stickiness with cookies</li>
<li>High availability across zones</li>
<li>Separates public traffic from private</li>
<li><strong>ELB (Elastic load balancer)</strong>
<ul>
<li>Managed Load balancer</li>
<li>Elastic Load Balancing automatically distributes your incoming traffic across multiple targets, such as EC2 instances, containers, and IP addresses, in one or more Availability Zones.</li>
<li>AWS takes cares of upgrades, maintenance, high availability</li>
<li>AWS provides only a few configuration knobs</li>
<li>Cost to setup but will be lot more effort on your end</li>
<li>Integrated with other AWS services</li>
<li>Health checks is done on a port and a route (/health is common)
<ul>
<li>if the response if not 200 (OK), then instance is unhealthy</li>
</ul>
</li>
<li>It can automatically scale to the vast majority of workloads</li>
<li>You can setup internal(private) and external(public) ELBs</li>
<li><img src="https://res.cloudinary.com/hy4kyit2a/f_auto,fl_lossy,q_70/learn/modules/aws-optimization/route-traffic-with-amazon-elastic-load-balancing/images/5e6d6d4ec8ab0c7bb984cf7624178d5a_b-7-eef-6-f-4-fdb-5-4-e-01-a-89-d-88-d-7257-bf-924.png" alt="enter image description here" width="500" height="300"></li>
<li><a href="https://docs.aws.amazon.com/elasticloadbalancing/latest/classic/introduction.html">Classic Load Balancer(CLB)</a>
<ul>
<li>Old generation, 2009, TCP (layer 4) HTTP &amp; HTTPS (layer 7)</li>
<li>A Classic Load Balancer makes routing decisions at either the transport layer (TCP/SSL) or the application layer (HTTP/HTTPS)</li>
<li>Classic Load Balancers currently require a fixed relationship between the load balancer port and the container instance port. E.g. it is possible to map the load balancer port 80 to the container instance port 3030 and the load balancer port 4040 to the container instance port 4040. However, it is not possible to map the load balancer port 80 to port 3030 on one container instance and port 4040 on another container instance.</li>
<li>This static mapping requires that your cluster has at least as many container instances as the desired count of a single service that uses a Classic Load Balancer</li>
<li><img src="https://docs.aws.amazon.com/elasticloadbalancing/latest/classic/images/load_balancer.png" alt="enter image description here"></li>
<li>Features
<ul>
<li>Health check are TCP or HTTP based</li>
<li>Fixed hostname <a href="http://xxx.region.elb.amazonaws.com">xxx.region.elb.amazonaws.com</a></li>
<li>Support for EC2-Classic</li>
<li>Support for TCP and SSL listeners</li>
<li>Support for sticky sessions using application-generated cookies</li>
</ul>
</li>
</ul>
</li>
<li><a href="https://docs.aws.amazon.com/elasticloadbalancing/latest/application/introduction.html">Application Load Balancer (ALB)</a>
<ul>
<li>New generation, HTTP &amp; HTTPS (layer 7), support for HTTP/2 and WebSocket</li>
<li>Makes routing decisions at the application layer (HTTP/HTTPS), supports path-based routing, and can route requests to one or more ports on each container instance in your cluster</li>
<li>Support dynamic host port mapping. For example, if your task’s container definition specifies port 80 for an NGINX container port, and port 0 for the host port, then the host port is dynamically chosen from the ephemeral port range of the container instance (such as 32768 to 61000 on the latest Amazon ECS-optimized AMI)</li>
<li>It monitors the health of its registered targets, and routes traffic only to the healthy targets</li>
<li><em>Listener</em>
<ul>
<li>Checks for connection requests from clients, using the protocol and port that you configure</li>
<li>The rules that you define for a listener determine how the load balancer routes requests to its registered targets.</li>
</ul>
</li>
<li><em>Target group</em>
<ul>
<li>Routes requests to one or more registered targets, such as EC2 instances, using the protocol and port number that you specify</li>
<li>You can register a target with multiple target groups</li>
<li>You can configure health checks on a per target group basis</li>
<li>Target groups are
<ul>
<li>EC2 instances (HTTP)</li>
<li>ECS Tasks (HTTP)</li>
<li>Lambda functions (HTTP request is translated into JSON event)</li>
<li>IP address (must be private)</li>
</ul>
</li>
<li>Can route to multiple target groups</li>
<li>Health check at Target group level</li>
<li><img src="https://www.bogotobogo.com/DevOps/AWS/images/NLB-ASG/ALB-Diagram.png" alt="enter image description here"></li>
<li><img src="https://i.pinimg.com/originals/53/77/ee/5377ee17410f880ec70c9f4bf3463bc3.png" alt="enter image description here"></li>
</ul>
</li>
<li>Features
<ul>
<li>Fixed hostname <a href="http://xxx.region.elb.amazonaws.com">xxx.region.elb.amazonaws.com</a></li>
<li>Load balancing to multiple HTTP applications across machines</li>
<li>Load balancing to multiple applications on same machine (containers)</li>
<li>Support route routing, based on different target groups
<ul>
<li>Path in the URL (E.g. /users (Target 1) &amp; /posts (Target 2))</li>
<li>Hostname in the URL (E.g. <a href="http://example.com">example.com</a> (Target 1) &amp; <a href="http://test.example.com">test.example.com</a> (Target 2))</li>
<li>Query string or headers in the URL (E.g. ?id=1234 (Target 1) &amp; ?name=xyz (Target 2))</li>
</ul>
</li>
<li>Great fit for Microservices and container based application (Docker &amp; Amazon ECS)</li>
<li>Access logs contain additional information and are stored in compressed format</li>
<li>Has a port mapping feature to redirect to a dynamic port in ECS</li>
<li>Application server don’t see the IP of client directly
<ul>
<li>The true IP of client is inserted in header X-Forwarded-For</li>
<li>We can also get Port (X-Forwarded-Port) and proto (X-Forwarded-Proto)</li>
</ul>
</li>
</ul>
</li>
<li>In comparison, multiple CLB per application</li>
</ul>
</li>
<li><a href="https://docs.aws.amazon.com/elasticloadbalancing/latest/network/introduction.html">Network Load Balancer (NLB)</a>
<ul>
<li>New generation, 2017 - TCP, TLS (Secure TCP) &amp; UDP</li>
<li>Makes routing decisions at the transport layer (TCP/SSL)</li>
<li>After the it receives a connection, it selects a target from the target group for the default rule using a flow hash routing algorithm</li>
<li>It attempts to open a TCP connection to the selected target on the port specified in the listener configuration</li>
<li>It forwards the request without modifying the headers</li>
<li>A <em>listener</em> checks for connection requests from clients, using the protocol and port that you configure, and forwards requests to a target group.</li>
<li>Each <em>target group</em> routes requests to one or more registered targets, such as EC2 instances, using the TCP protocol and the port number that you specify</li>
<li>You can add and remove targets from your load balancer as your needs change, without disrupting the overall flow of requests to your application</li>
<li>It has <strong>one static IP per AZ</strong> and supports assigning Elastic IP (Helpful for IP whitelisting)</li>
<li><img src="https://exampleloadbalancer.com/assets/with_nlbtls.png" alt="enter image description here"></li>
<li>Features
<ul>
<li>Support dynamic host port mapping</li>
<li>Used for extreme performance, TCP or UDP traffic</li>
<li>Handle millions of requests per second</li>
<li>Less latency ~100ms (vs 400ms for ALB)</li>
<li>Has one static IP per AZ and supports assigning EIP which helpful for  IP whitelisting</li>
<li>Not included in AWS free tier</li>
<li>Has own SG which communicate with EC2 SG</li>
</ul>
</li>
</ul>
</li>
</ul>
</li>
<li><a href="https://docs.aws.amazon.com/elasticloadbalancing/latest/gateway/introduction.html">Gateway Load Balancer</a>
<ul>
<li>Enable you to deploy, scale, and manage virtual appliances, such as firewalls, intrusion detection and prevention systems, and deep packet inspection systems</li>
<li>Operates at the third layer of the Open Systems Interconnection (OSI) model, the network layer</li>
<li>It listens for all IP packets across all ports and forwards traffic to the target group that’s specified in the listener rule</li>
<li>It maintains stickiness of flows to a specific target appliance using 5-tuple (for TCP/UDP flows) or 3-tuple (for non-TCP/UDP flows)</li>
<li>Uses Gateway Load Balancer endpoints to securely exchange traffic across VPC boundaries</li>
<li>You deploy the Gateway Load Balancer in the same VPC as the virtual appliances</li>
<li>Traffic to and from a Gateway Load Balancer endpoint is configured using route tables</li>
<li>You must create the Gateway Load Balancer endpoint and the application servers in different subnets</li>
<li><img src="https://miro.medium.com/max/1121/1*qNMaVza5BzgrimG2fz0gNg.png" alt="enter image description here"></li>
</ul>
</li>
<li><strong>Load balancer stickiness</strong>
<ul>
<li>Stickiness is that same client always get redirects to the same instance</li>
<li>Works for CLB and ALB</li>
<li>The cookie used for stickiness has an expiration date</li>
<li>To make sure user doesn’t lose his session data</li>
<li>May bring imbalance to the load balancing over to the backend EC2 instances</li>
<li>Can enable at target group level</li>
</ul>
</li>
<li><strong>Cross Zone Load Balancer</strong>
<ul>
<li>Each load balancer instance evenly distributes load across all registered instances in all AZ</li>
<li>Without each load balancer distributes requests evenly across the registered instances in its AZ only
<ul>
<li>CLB (disabled by default, no charges for inter AZ data if enabled)</li>
<li>ALB (Always on, no charges for inter AZ data)</li>
<li>NLB (disabled by default, pay charges for inter AZ data if enabled)</li>
</ul>
</li>
</ul>
</li>
<li><strong>SSL (Secure Socked Layer) Certificate</strong>
<ul>
<li>Allows traffic between clients and LB to be encrypted in transit</li>
<li>Issued by public authority (Symantec, GoDaddy etc.)</li>
<li>LB uses X.509 certificate</li>
<li>You can manage certificates using ACM</li>
<li>Client can use SNI (Server Name Indication) to specify the hostname they reach</li>
<li>SNI solved problem if loading multiple SSL certificates onto one web server</li>
<li>SNI works for ALB and NLB only
<ul>
<li>CLB (support only one certificate, must use multiple CLB for multiple hostname with mu certificates)</li>
<li>ALB (support multiple listeners with multiple SSL certificates, uses SNI to make it work)</li>
<li>NLB (support multiple listeners with multiple SSL certificates, uses SNI to make it work)</li>
</ul>
</li>
</ul>
</li>
<li><strong>Connection draining</strong>
<ul>
<li>Time to complete “in-flight requests” while the instance is de-registering or unhealthy</li>
<li>Stop sending request to instance while its de-registering</li>
<li>Between 1 to 3600 secs, default is 300 secs</li>
<li>Can’t be disabled (set value to 0)</li>
<li>Set to a low value if your requests are short</li>
<li>Connection draining is for CLB</li>
<li>Deregistration delay is for ALB and NLB</li>
</ul>
</li>
</ul>
</li>
<li><a href="https://docs.aws.amazon.com/autoscaling/ec2/userguide/AutoScalingGroup.html">Auto scaling group</a>
<ul>
<li>Scale out (Add ec2 instance) to match increased load</li>
<li>Scale in (remove ec2 instance) to match decreased load</li>
<li><img src="https://miro.medium.com/max/487/1*uS9J8btKCQaMOhnUXp62aA.jpeg" alt="enter image description here"></li>
<li>Auto register new instance to a load balancer</li>
<li>ASG is Free</li>
<li>If having instance under ASG get terminated for whatever reason then ASG will automatically created new ones as a replacement</li>
<li>ASG can terminate instance marked as unhealthy by LB (and replace them)</li>
<li>Has following attributes
<ul>
<li>A Launch configuration has
<ul>
<li>Instance type</li>
<li>EC2 User data</li>
<li>EBS volume</li>
<li>Security groups</li>
<li>SSH Key pair</li>
</ul>
</li>
<li>Min/Max Size and initial capacity</li>
<li>Network + Subnet information</li>
<li>Load balancer information  or Target Group information</li>
<li>Scaling policies</li>
</ul>
</li>
</ul>
</li>
<li>Auto scaling alarms
<ul>
<li>Scale in/out based on CloudWatch alarms</li>
<li>Monitors metrics</li>
<li>Based on can created policies</li>
<li><img src="https://railsadventures.files.wordpress.com/2015/02/auto-scaling-on-aws-flow.png" alt="enter image description here"></li>
</ul>
</li>
<li>Scaling policies
<ul>
<li>Possible to define rules that are directly managed by EC2 e.g. Target Average CPU usage, number of requests per ELB</li>
<li>Target tracking scaling policy
<ul>
<li>Most simple and easy to set up</li>
<li>E.g. average ASG CPU to stay around 40%</li>
</ul>
</li>
<li>Simple/Step scaling policy
<ul>
<li>When CloudWatch alarm triggered (e.g. CPU &gt; 70%) then add 2 units</li>
<li>When CloudWatch alarm triggered (e.g. CPU &lt; 30%) then remove 1 unit</li>
</ul>
</li>
<li>Scheduled actions
<ul>
<li>Anticipate a scaling based on know usage patterns</li>
<li>E.g. increase min capacity to 10 at 5pm on Friday</li>
</ul>
</li>
</ul>
</li>
<li>Scaling cooldowns
<ul>
<li>Helps to insure ASG doesn’t launch or terminate instance before previous scaling  activity takes effect</li>
<li>One common use for scaling specific cooldowns is</li>
<li>If the default cooldown period of 300 sec is too long you can reduce cost by applying scaling specific cooldown period of 180 secs to scale-in policy</li>
<li>If your application is scaling-up and down multiple times each hour, modify the ASG cool-down times and the CloudWatch Alarm Period that triggers scale-in</li>
</ul>
</li>
<li>Default Termination policy
<ul>
<li>Find AZ which has most instances, if there are multiple then delete the one with oldest launch configuration</li>
<li>ASG tries the balance the number of imbalances across AZ by default</li>
</ul>
</li>
<li><a href="https://docs.aws.amazon.com/autoscaling/ec2/userguide/AutoScalingGroupLifecycle.html">Lifecycle Hooks</a>
<ul>
<li>Can perform extra steps (Extract info/ logging) before instance goes in service (Pending state) or before it get terminated (Terminating state)</li>
<li><img src="https://docs.aws.amazon.com/autoscaling/ec2/userguide/images/auto_scaling_lifecycle.png" alt="enter image description here"></li>
<li>ASG
<ul>
<li>(Scale Out) &gt; Pending [ Lifecycle hooks &gt;&gt; Pending:wait &gt; Pending: Proceed] &gt; InService</li>
<li>(Scale In) &gt; Terminating [ Lifecycle hooks &gt;&gt; Terminating:wait &gt; Terminating: Proceed] &gt; Terminated</li>
</ul>
</li>
</ul>
</li>
<li>Launch configuration: Legacy, must be created every time vs Launch Template: newer, can have multi-versions, parameter subsets and provision for both On-Demand and Spot instances</li>
</ul>
<hr>
<p>Q1: Load Balancers provide a</p>
<blockquote>
<p>Static DNS name we can used n our application</p>
</blockquote>
<p>Q: You are running a website with a load balancer and 10 EC2 instances. Your users are complaining  about the fact that your website always asks them to re-authenticate when they switch pages. You  are puzzled, because it’s working just fine on your machine and in the dev environment with 1  server. What could be the reason?</p>
<blockquote>
<p>The load balancer does not have stickiness enabled (stickiness ensures traffic sent to the same backend instance for a client to maintain session data)</p>
</blockquote>
<p>Q: Your application is using an Application Load Balancer. It turns out your application only sees  traffic coming from private IP which are in fact your load balancer’s. What should you do to  find the true IP of the clients connected to your website?</p>
<blockquote>
<p>look into the X-Forwarded-For header in the backend</p>
</blockquote>
<p>Q: You quickly created an ELB and it turns out your users are complaining about the fact that  sometimes, the servers just don’t work. You realise that indeed, your servers do crash from time  to time. How to protect your users from seeing these crashes?</p>
<blockquote>
<p>Enable health checks (ensures your ELB wont send traffic to unhealthy instances)</p>
</blockquote>
<p>Q: You are designing a high performance application that will require millions of connections to be handled, as well as low latency. The best Load Balancer for this is</p>
<blockquote>
<p>Network Load balancer</p>
</blockquote>
<p>Question 6: Application Load Balancers handle all these protocols except</p>
<blockquote>
<p>TCP (NLB support TCP)</p>
</blockquote>
<p>Q: The application load balancer can route to different target groups based on all these<br>
except…</p>
<blockquote>
<p>Geography (correct ones are Hostname, request-path, Source IP)</p>
</blockquote>
<p>Q: You are running at desired capacity of 3 and the maximum capacity of 3. You have alarms set at  60% CPU to scale out your application. Your application is now running at 80% capacity. What  will happen?</p>
<blockquote>
<p>Nothing (The capacity of your ASG cannot go over the maximum capacity you have allocated<br>
during scale out events)</p>
</blockquote>
<p>Q: I have an ASG and an ALB, and I setup my ASG to get health status of instances thanks to my ALB.  One instance has just been reported unhealthy. What will happen?</p>
<blockquote>
<p>The ASG will terminate the EC2 instance (Because the ASG has been configured to leverage the ALB<br>
health checks, unhealthy instances will be terminated)</p>
</blockquote>
<p>Q: Your boss wants to scale your ASG based on the number of requests per minute your application makes  to your database.</p>
<blockquote>
<p>Create CloudWatch custom metrics and build alarm on this to scale your ASG (The metric “requests<br>
per minute” is not an AWS metric, hence it needs to be a custom metric)</p>
</blockquote>
<p>Q: Scaling an instance from an r4.large to an r4.4xlarge is called</p>
<blockquote>
<p>Vertical Scalability</p>
</blockquote>
<p>Q: Running an application on an auto scaling group that scales the number of instances in and out  is called</p>
<blockquote>
<p>Horizontal Scalability</p>
</blockquote>
<p>Q: You would like to expose a fixed static IP to your end-users for compliance purposes, so they can  write firewall rules that will be stable and approved by regulators. Which Load Balancer should you use?</p>
<blockquote>
<p>Network Load Balancer (Network Load Balancers expose a public static IP, whereas an Application or<br>
Classic Load Balancer exposes a static DNS (URL))</p>
</blockquote>
<p>Q: A web application hosted in EC2 is managed by an ASG. You are exposing this application through an Application Load Balancer. The ALB is deployed on the VPC with the following CIDR: 192.168.0.0/18.  How do you configure the EC2 instance security group to ensure only the ALB can access the port 80?</p>
<blockquote>
<p>Open up the EC2 Security on port 80 to the ALB’s security group (This is the most secure way of ensuring only the ALB can access the EC2 instances. Referencing by security groups in rules is an extremely powerful rule and many questions at the exam rely on it. Make sure you fully master the concepts behind it!)</p>
</blockquote>
<p>Q: Your application load balancer is hosting 3 target groups with hostnames being <a href="http://users.example.com">users.example.com</a>,   <a href="http://api.external.example.com">api.external.example.com</a> and <a href="http://checkout.example.com">checkout.example.com</a>. You would like to expose HTTPS traffic for each of these hostnames. How do you configure your ALB SSL certificates to make this work?</p>
<blockquote>
<p>Use SNI (SNI (Server Name Indication) is a feature allowing you to expose multiple SSL certs if the client supports it)</p>
</blockquote>
<p>Q: An ASG spawns across 2 availability zones. AZ-A has 3 EC2 instances and AZ-B has 4 EC2 instances. The ASG is about to go into a scale-in event. What will happen?</p>
<blockquote>
<p>AZ-B will terminate instance with the oldest configuration (Make sure you remember the Default<br>
Termination Policy for ASG. It tries to balance across AZ first, and then delete based on the age<br>
of the launch configuration.)</p>
</blockquote>
<p>Q: The Application Load Balancers target groups can be all of these EXCEPT…</p>
<blockquote>
<p>Network Load balancer (Correct ones are EC2 instance, IP, Lambda functions)</p>
</blockquote>
<p>Q: You are running an application in 3 AZ, with an Auto Scaling Group and a Classic Load Balancer. It  seems that the traffic is not evenly distributed amongst all the backend EC2 instances, with some  AZ being overloaded. Which feature should help distribute the traffic across all the available EC2<br>
instances?</p>
<blockquote>
<p>Cross Zone load balancing</p>
</blockquote>
<p>Q: Your Application Load Balancer (ALB) currently is routing to two target groups, each of them is  routed to based on hostname rules. You have been tasked with enabling HTTPS traffic for each hostname and have loaded the certificates onto the ALB. Which ALB feature will help it choose the<br>
right certificate for your clients?</p>
<blockquote>
<p>Use SNI (Server Name Indication)</p>
</blockquote>
<p>Q: An application is deployed with an Application Load Balancer and an Auto Scaling Group. Currently,  the scaling of the Auto Scaling Group is done manually and you would like to define a scaling  policy that will ensure the average number of connections to your EC2 instances is averaging at around 1000. Which scaling policy should you use?</p>
<blockquote>
<p>Target Tracking</p>
</blockquote>
<h2 id="aws-storage-services">AWS Storage Services</h2>
<ul>
<li><a href="https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Welcome.html">Relative Database Service</a>
<ul>
<li>Managed SQL database service i.e. web service that makes it easier to set up, operate, and<br>
scale a relational database in the AWS Cloud.</li>
<li>It provides cost-efficient, resizable capacity for an industry-standard relational database and<br>
manages common database administration tasks.</li>
<li>Automated provision and used SQL language</li>
<li>Allows to create databases in cloud managed by AWS like Postgres, MySQL, MariaDB, Oracle,<br>
MSSQL and Aurora</li>
<li>Multi AZ setup for DR</li>
<li>Scaling capabilities (Vertical and horizontal)</li>
<li>Storage backed by EBS</li>
<li>Can SSH into instances</li>
<li><strong>Backup</strong>
<ul>
<li>Automatic daily full</li>
<li>Transaction log backed every 5 mins</li>
<li>Restore to any point (oldest is 5 mins ago)</li>
<li>7 days retentions</li>
</ul>
</li>
<li><strong>Auto Scaling</strong>
<ul>
<li>increase storage dynamically</li>
<li>detect and scales automatically</li>
<li>you have to set Maximum Storage Threshold</li>
<li>Automatically modify storage if
<ul>
<li>Free storage less than 10% of allocated storage</li>
<li>Low storage last at least 5 mins</li>
<li>6 hours have passed since last modification</li>
</ul>
</li>
<li>Useful when unpredictable workloads</li>
<li>Supports all RDS databases</li>
</ul>
</li>
<li><strong>Read Replicas</strong>
<ul>
<li>Helps to scales reads</li>
<li>Up to 5 RR</li>
<li>Within AZ, Cross AZ or Cross Region</li>
<li>Async, eventual consistent</li>
<li>Replicas can be promoted to own DB</li>
<li>Must update connection string to leverage RR</li>
<li>Use case &gt; create new RDS RR with Async replication to divert additional reads so<br>
main application will be unaffected</li>
<li>RR used for Select Query only</li>
<li>If RR within same region, no charges else for cross region have to pay</li>
</ul>
</li>
<li><strong>Multi AZ for Disaster Recovery</strong>
<ul>
<li>SYNC Replication</li>
<li>One DNS name - Automatic app failover</li>
<li>Increase availability</li>
<li>No manual intervention</li>
<li>Not used for scaling</li>
<li>Note: can setup read replicas as Multi AZ for Disaster Recovery</li>
</ul>
</li>
<li><strong>Single AZ to Multi AZ</strong>
<ul>
<li>Zero downtime</li>
<li>Just modify setting</li>
<li>Snapshot taken from main DB &gt; new DB restored from this snapshot in a<br>
new AZ &gt; Synchronization established between two DBs</li>
</ul>
</li>
<li><strong>Security</strong>
<ul>
<li>Can not SSH</li>
<li><em>At rest</em> encryption
<ul>
<li>Possible to encrypt the master and read replicas</li>
<li>Has to be defined at launch time</li>
<li>If master not encrypted then RR can not be encrypted</li>
</ul>
</li>
<li><em>In-flight</em> encryption
<ul>
<li>SSL certificate to encrypt</li>
<li>To enforce
<ul>
<li>Postgres: rds_ssl = 1 in the parameter groups</li>
<li>MySQL: GRANT USAGE ON <em>.</em> TO ‘mysqluser’@’%’ REQUIRE SSL;</li>
</ul>
</li>
</ul>
</li>
<li>Backup will only encrypted if snapshot taken from encrypted DB but can copy a  un-encrypted snapshot as<br>
encrypted and create DB out of it</li>
<li>leverage SG (control which IP/SG can communicate with RDS), IAM Roles(work with MySQL and<br>
Postgres using auth token obtained from IAM with 15 mins lifetime) and EC2 instance</li>
</ul>
</li>
</ul>
</li>
<li><a href="https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/CHAP_AuroraOverview.html">Aurora</a>
<ul>
<li>Not open sourced</li>
<li>Fully managed relational database engine that’s compatible with MySQL and PostgreSQL.</li>
<li>AWS Cloud optimized (5x performance improvement over MySQL, over 3x in Postgres),<br>
supported by Postgres and MySQL</li>
<li>Automatically grows in increment of 10GB, up to 64TB</li>
<li>Can have 15 replicas while MySQL has 5, replication process is faster</li>
<li>Cost more than RDS (20%) but more efficient</li>
<li>Master instance take writes and failover in less than 30 secs</li>
<li>Master + up to 15 read replicas (Auto scaling)</li>
<li>Support cross origin replication</li>
<li>DB Cluster
<ul>
<li>Writer endpoint (points to master)</li>
<li>Reader endpoint (points to RR using connection load balancing)</li>
</ul>
</li>
<li>Backtrack: restore data at any point of time without using backups</li>
<li>Security same as RDS</li>
<li>We can define custom read endpoints for specific reads</li>
<li>Serverless: Automated db instantiation and auto scaling, no capacity planning needed, pay per second</li>
<li>Multi-Master: In case you want immediate failover for write node, every node does r/w<br>
or promoting a RR as the new master</li>
<li>Cross Region RR: useful in disaster recovery, simple to put in place</li>
<li>Global Database: 1 Primary region (r/w), 5 secondary (r only) region with &lt; 1sec replication lag, up to 16 RR per secondary region and promoting another region with RTO &lt; 1 min</li>
<li>ML: prediction using SQL (SageMaker and Comprehend)</li>
</ul>
</li>
<li><a href="https://docs.aws.amazon.com/AmazonElastiCache/latest/red-ug/elasticache-use-cases.html">ElastiCache</a>
<ul>
<li>Allows you to seamlessly set up, run, and scale popular open-source compatible in-memory data stores in the cloud.</li>
<li>Popular choice for real-time use cases like Caching, Session Stores, Gaming, Geospatial Services, Real-Time Analytics,  and Queuing</li>
<li>Makes your application stateless</li>
<li>Reduce load off of databases for read intensive workloads</li>
<li><strong>REDIS</strong>
<ul>
<li>In memory data structure store used as database, cache and message broker</li>
<li>Multi AZ with Auto failover</li>
<li>RR to scale reads and have high availability</li>
<li>Data durability using AOF persistence</li>
<li>Backup and restore feature</li>
</ul>
</li>
<li><strong>MEMCACHED</strong>
<ul>
<li>High performance, distributed memory object caching system</li>
<li>intended for use in speeding up dynamic web applications</li>
<li>Multi-node for partitioning of data (Sharding)</li>
<li>No high availability (Replication)</li>
<li>Non persistent</li>
<li>No backup and restore</li>
<li>Multi-threaded architecture</li>
</ul>
</li>
<li><strong>Security</strong>
<ul>
<li>Do not support IAM authentication</li>
<li>IAM policies are only used for AWS API level security</li>
<li>Redis: Use AUTH (password/token), supports SSL in flight encryption</li>
<li>Memcached: SASL based authentication</li>
</ul>
</li>
<li><strong>Patterns</strong>
<ul>
<li>Lazy loading: all read data is cached</li>
<li>Write through: Adds or update data in the cache when written to DB (np stale data)</li>
<li>Session store: store temporary session data in a cache (using TTL)</li>
</ul>
</li>
<li><strong>Use cases</strong>
<ul>
<li>Gaming leaderboard: Redis sorted set guarantee both uniqueness and order, so each time a new element added, its ranked in real time and added in order</li>
</ul>
</li>
</ul>
</li>
<li><a href="https://docs.aws.amazon.com/AmazonS3/latest/userguide/Welcome.html">AWS S3 (Simple Storage Service)</a>
<ul>
<li>Simple web services interface that you can use to store and retrieve any amount of data, at any time, from anywhere</li>
<li>It gives any developer access to the same highly scalable, reliable, fast, inexpensive data storage infrastructure</li>
<li><strong>S3 Buckets</strong>
<ul>
<li>Has global unique name</li>
<li>Defined at the region level (S3 is not global service but do provide global console)</li>
<li>Naming (No uc, lc, 3-63 chars long, not an IP and start with lc or number)</li>
<li>Key is composed of prefix (my_folder) + object name (my_file.txt) = my_folder/my_file.txt</li>
<li>Full URL would be S3://my-unique-bucket/my_folder/my_file.txt</li>
<li>No concepts of directory within buckets</li>
<li><em>Objects</em>
<ul>
<li>Max size of object 5TB</li>
<li>For upload more than 5gb, must use multi-part upload</li>
<li>Metadata (list of key/value pair)</li>
<li>Tags (Unicode key/value pair, up to 10)</li>
<li>Version Id</li>
<li>Has public object URL (Has to configure it as public)</li>
</ul>
</li>
</ul>
</li>
<li><strong>S3 Versioning</strong>
<ul>
<li>Version your files with multiple uploads of same file</li>
<li>Enabled at bucket level</li>
<li>Easy to role back in case of accidental delete</li>
<li>Normal delete just add delete-marker on object and hide them, you have delete versions for permenent delete</li>
</ul>
</li>
<li><strong>S3 Encryption</strong>
<ul>
<li><em>SSE-S3</em> Encrypts objects using keys handled &amp; managed by AWS
<ul>
<li>Encrypted at server side</li>
<li>Encrypted with AES-256 &amp; S3 managed data key</li>
<li>Must set header “x-amz-server-side-encryption”:“AES256” in request</li>
</ul>
</li>
<li><em>SSE-KMS</em> Encrypts objects using keys from KMS
<ul>
<li>Encrypted at server side</li>
<li>Encrypted using keys managed by KMS</li>
<li>Must set header “x-amz-server-side-encryption”:“aws:kms” in request</li>
<li>Has user control and audit trails</li>
<li>KMS can caused performance issue as it calls KMS API GenerateDataKey while upload and Decrypt while download</li>
</ul>
</li>
<li><em>SSE-C</em> With own encryption keys
<ul>
<li>Encrypted at server side</li>
<li>Encrypted using keys managed by customer outside AWS</li>
<li>HTTPS must be used</li>
<li>Key must be provided in HTTP headers for every request</li>
</ul>
</li>
<li><em>Client side</em> encryption
<ul>
<li>Encrypted at client side</li>
<li>Customer uploads encrypted object</li>
</ul>
</li>
<li><em>In transit (SSL/TLS)</em>
<ul>
<li>S3 exposed
<ul>
<li>HTTP endpoint which is non-encrypted</li>
<li>HTTPS endpoint which is encryption in flight</li>
</ul>
</li>
<li>Encryption in flight also called SSL/TLS</li>
</ul>
</li>
</ul>
</li>
<li><strong>Security and Bucket policies</strong>
<ul>
<li><em>IAM</em>, API called allowed for specific user
<ul>
<li>An IAM principle can access an S3 object if the user IAM permissions allow it OR resource policy allows it<br>
and there’s not explicit deny</li>
</ul>
</li>
<li><em>Resource based</em> policies
<ul>
<li>Bucket policies: bucket wide rules from S3 - allows across account</li>
<li>Object Access Control List (ACL) - Finer grain</li>
<li>Bucket Access Control List (ACL) - less common</li>
</ul>
</li>
<li><em>Json based</em> policies
<ul>
<li>Resources: buckets and objects</li>
<li>Actions: Set of API to allow or deny</li>
<li>Effect: Allow/Deny</li>
<li>Principle: The account or user to apply the policy to</li>
<li>Use cases
<ul>
<li>Grant public access to bucket</li>
<li>Force object to be encrypted at upload</li>
<li>Grant access to another account</li>
</ul>
</li>
</ul>
</li>
<li><strong>Block public access</strong> by Bucket settings
<ul>
<li>Block public access to bucket and object granted through
<ul>
<li>By Access control list (any, new)</li>
<li>By Access point policies</li>
</ul>
</li>
<li>These settings created to prevent data leaks</li>
<li>If you don’t want your bucket public leave this setting ON</li>
<li>Can be set at the account level</li>
</ul>
</li>
<li><strong>Network Security</strong>
<ul>
<li>Supports VPC endpoints (for instance in VPC without www internet)</li>
<li>Access logs can be stored in other bucket</li>
<li>API call logged in AWS CloudTrails</li>
</ul>
</li>
<li><strong>User Security</strong>
<ul>
<li>MFA for versioned buckets to delete objects</li>
<li>Pre-signed URLs that are valid only for limited time</li>
</ul>
</li>
</ul>
</li>
<li><strong>S3 Websites</strong>
<ul>
<li>S3 can host static websites and have them accessible on the www</li>
<li>URL will be <a href="http://bucket-name.s3-website-aws-region.amazonaws.com">bucket-name.s3-website-aws-region.amazonaws.com</a></li>
<li>Make sure bucket allows public reads</li>
</ul>
</li>
<li><strong>S3 CORS</strong> (Cross Origin Resource Sharing)
<ul>
<li>Web Browser based mechanism does not allow request to other origins while visiting the main<br>
origin unless allowed by other origin</li>
<li>If client does a cross origin request on our S3 bucket, we need to enable the correct CORS headers</li>
<li>You can allow specific origin or all (*)</li>
</ul>
</li>
<li><strong>S3 Consistency Model</strong>
<ul>
<li>Strong consistent as of Dec 2020</li>
<li>After a
<ul>
<li>Successful write of new object (new PUT)</li>
<li>or an overwrite or delete of existing object</li>
</ul>
</li>
<li>subsequent request immediately receives a latest version of the object (r/w consistency)</li>
<li>subsequent list request  immediately reflect changes (List consistency)</li>
</ul>
</li>
<li><a href="https://docs.aws.amazon.com/AmazonS3/latest/userguide/MultiFactorAuthenticationDelete.html">S3 MFA-Delete</a>
<ul>
<li>Enable versioning on bucket</li>
<li>MFA will be needed to
<ul>
<li>Permanently delete an object version</li>
<li>Suspend versioning</li>
</ul>
</li>
<li>MFA will not be needed to
<ul>
<li>Enabling versioning</li>
<li>Listing deleted versions</li>
</ul>
</li>
<li>Only the bucket owner (root account) can enable/disable MFA-Delete</li>
</ul>
</li>
<li><strong>S3 Access logs</strong>
<ul>
<li>For audit purpose you may want to log all access, request made to S3 etc.</li>
<li>Do not set logging bucket to be monitored bucket (app bucket), it will create logging loop and bucket size will<br>
grow exponentially</li>
</ul>
</li>
<li><strong>S3 Replication</strong>
<ul>
<li>Must enable versioning</li>
<li>Bucket can be different account</li>
<li>Copying is asynchronous</li>
<li>Must give proper IAM permissions to S3</li>
<li>Cross Region Replication (CRR) for compliance, lower latency access, replication across account</li>
<li>Same Region Replication (SRR) Log aggregation, live replication between accounts</li>
<li>After activating only new object are replicated</li>
<li>For delete operations
<ul>
<li>Can replicate delete markers from source to target (optional setting)</li>
<li>Deletion with a version ID are not replicated</li>
</ul>
</li>
<li>No chaining of replication (if bucket 1 has replication into bucket 2, which has replication on bucket 3 then objects<br>
created in bucket 1 are not replicated in bucket 3)</li>
<li>Does not replicate deletes</li>
</ul>
</li>
<li><strong>S3 Pre-signed URL</strong>
<ul>
<li>Using CLI or SDK</li>
<li>Expiration of 3600s, can change with --expires-in [Times-in-seconds] argument</li>
<li>Users given pre-signed URL inherit the permissions of the person who generate the URL for GET/PUT</li>
<li>Example
<ul>
<li>Allow only logged in user to download video from S3 bucket</li>
<li>Allow temporary user to upload file to precise location of bucket</li>
</ul>
</li>
</ul>
</li>
<li><a href="https://aws.amazon.com/s3/storage-classes/">S3 Storage classes</a>
<ul>
<li><em>S3 Standard</em>
<ul>
<li>General purpose</li>
<li>High Durability (99.9%) of objects across Multi-AZ</li>
<li>99.9% Availability over a given year</li>
<li>Sustain 2 concurrent facility failure</li>
<li>Use cases
<ul>
<li>Big data analytics</li>
<li>Mobile and gaming application</li>
<li>Content distribution</li>
</ul>
</li>
</ul>
</li>
<li><em>S3 Standard - Infrequent Access (IA)</em>
<ul>
<li>When files are less frequently accessed</li>
<li>Low cost compare to Standard</li>
<li>High Durability (99.9%) of objects across Multi-AZ</li>
<li>99.9% Availability over a given year</li>
<li>Sustain 2 concurrent facility failure</li>
<li>Use cases
<ul>
<li>Data store for disaster recovery, backups</li>
</ul>
</li>
<li>Minimum storage duration charge for 30 days</li>
</ul>
</li>
<li><em>S3 One Zone - Infrequent Access (IA)</em>
<ul>
<li>Same as IA but data stored in a single AZ</li>
<li>Low cost compare to Standard</li>
<li>High Durability (99.9%) of objects in a single AZ, but when AZ destroyed data will be lost</li>
<li>99.5% Availability over a given year</li>
<li>Low latency and high throughput</li>
<li>Support SSL</li>
<li>Lower cost compared to IA</li>
<li>Use cases
<ul>
<li>Secondary backups of on-premise data</li>
<li>Storing data you can recreate</li>
</ul>
</li>
<li>Minimum storage duration charge is 30 days</li>
</ul>
</li>
<li><em>Intelligent tiering</em>
<ul>
<li>Same Low latency and high throughput as Standard</li>
<li>Small monthly monitoring and auto-tiering fee</li>
<li>Auto move data between two access tiers based on chaining access patterns</li>
<li>High Durability (99.9%) of objects across Multi-AZ</li>
<li>99.9% Availability over a given year</li>
<li>Resilient against events that impact an entire AZ</li>
<li>Minimum storage duration charge for  30 days</li>
</ul>
</li>
<li><em>Glacier</em>
<ul>
<li>Low cost object storage mean for achieving</li>
<li>Data is retained for the longer term (10s of years)</li>
<li>High Durability (99.9%)</li>
<li>Cost/storage per month ($0.004/GB) + retrieval cost</li>
<li>Each item called as “Archive” (up to 40TB)</li>
<li>Archives are stored as “Vaults”</li>
<li>3 Retrieval options
<ul>
<li>Expedited (1-5m) - expensive (per GB retrieved)</li>
<li>Standard (3-5h)</li>
<li>Bulk (5-12h)</li>
</ul>
</li>
<li>Minimum storage duration charge for  90 days</li>
</ul>
</li>
<li><em>Glacier Deep Archive</em>
<ul>
<li>For Archive you don’t need right away</li>
<li>Retrieval options
<ul>
<li>Standard (12h)</li>
<li>Bulk (48h)</li>
</ul>
</li>
<li>Minimum storage duration charge for 180 days</li>
</ul>
</li>
<li><strong>Lifecycle Rules</strong>
<ul>
<li>Moving objects between storage classes can be automated using lifecycle configuration</li>
<li>Transition actions - defines when object are transitioned into another storage class
<ul>
<li>Move objects to Standard IA after 60 days of creation</li>
<li>Move to Glacier for archiving after 6 months</li>
</ul>
</li>
<li>Expiration actions - configure objects to expire (delete) after some time
<ul>
<li>Access log files can be set to delete after 365 days</li>
<li>Can be used to delete old version of files (if versioning enabled)</li>
<li>Can be used to delete incomplete multi-part uploads</li>
</ul>
</li>
<li>Rules can be created for a certain prefix (e.g. s3://mybucket/mp3/*)</li>
<li>Rules can be created for a certain object tags (e.g. Dept:Fianance)</li>
</ul>
</li>
</ul>
</li>
<li><strong>S3 Analytics</strong>
<ul>
<li>To determine when to transition objects between storage classes</li>
<li>Does not work for One Zone IA or Glacier</li>
<li>Takes 24-48h to first start</li>
</ul>
</li>
<li><strong>S3 Performance</strong>
<ul>
<li>Auto scales to high request rate, latency 100-200ms</li>
<li>Can achieve at least 3500 PUT/COPY/POST/DELETE and 5500 GET/HEAD request / second / prefix in a bucket</li>
<li>No limit to the number of prefixes in a bucket</li>
<li>bucket/folder-x/folder-y/file &gt;&gt;prefix is /folder-x/folder-y/</li>
<li>if you spread reads across n prefixes evenly then you can achieve n*5500 request / second for GET and HEAD</li>
<li>S3 Transfer Acceleration increase speed by transferring file to an AWS edge location which will forward the data to S3<br>
S3 bucket in the target region (File in USA &gt;fast www&gt; Edge Location &gt;fast private dns&gt; S3 bucket in Australia)</li>
<li>S3 Byte-Range Fetches - parallelize GETS by requesting specific byte ranges, has better resilience in case of<br>
failures, useful to speed up downloads</li>
</ul>
</li>
<li><strong>S3 Select &amp; Glacier Select</strong>
<ul>
<li>Retrieve less data using SQL by performing server side filtering</li>
<li>Can filter by rows/columns</li>
<li>Less network transfer, less CPU cost</li>
</ul>
</li>
<li><strong>S3 Events notification</strong>
<ul>
<li>e.g. S3:ObjectCreated</li>
<li>Use case: generate thumbnails of images uploaded to s3</li>
<li>Can create as many S3 events</li>
<li>Delivers events in secs but may take mins sometime</li>
</ul>
</li>
<li><strong>S3 Requester Pays</strong>
<ul>
<li>In Standard Bucket - while download, Owner pays for both Storage and Networking Cost</li>
<li>In Requester Pays Bucket - while download, Owner pays Storage cost and Requester pays Networking Cost</li>
<li>Requester must be authenticated in AWS</li>
<li>Helpful when you want to share large datasets with other accounts</li>
</ul>
</li>
<li><a href="https://aws.amazon.com/premiumsupport/knowledge-center/analyze-logs-athena/">AWS Athena</a>
<ul>
<li>Serverless service to perform analytics directly against S3 files  using SQL queries</li>
<li>Has JDBC/ODBC driver</li>
<li>Charged per Query</li>
<li>Supports CSV, JSON, ORC etc.</li>
<li>Use cases
<ul>
<li>Business Intelligence/Analytics/Reporting</li>
<li>CloudTrails</li>
<li>VPC Flow logs/ELB logs</li>
</ul>
</li>
<li>Analyze data directly on S3 &gt; use Athena</li>
</ul>
</li>
<li><strong>S3 Lock policies &amp; Glacier Vault Lock</strong>
<ul>
<li><em>Glacier Vault Lock</em>
<ul>
<li>To Adopt WORM (Write once Read Many) model</li>
<li>Lock the policy for future edits (can no longer changed)</li>
<li>For Compliance and Data retention</li>
</ul>
</li>
<li><em>S3 Object lock</em>
<ul>
<li>Versioning must be enabled</li>
<li>To Adopt WORM (Write once Read Many) model</li>
<li>Block an object version deletion for specified time</li>
<li><em>Object Retention</em>
<ul>
<li>Retention period</li>
<li>Legal hold - No expiry date</li>
</ul>
</li>
<li><em>Modes</em>
<ul>
<li>Governance mode - user cant overwrite or delete object version or alter lock settings unless special permission</li>
<li>Compliance mode - protected object version cant be overwritten or deleted by any user, including root user, 				   when object locked in compliance mode, retention mode can’t be changed and retention period can’t be shortened</li>
</ul>
</li>
</ul>
</li>
</ul>
</li>
</ul>
</li>
<li><a href="https://docs.aws.amazon.com/snowball/">AWS Snow Family</a>
<ul>
<li>Highly secure portable devices to collect and process data a the edge, and migrate data into or out of AWS</li>
<li>You can use these devices to locally and cost-effectively access the storage and compute power of the AWS Cloud in places where an internet connection might not be an option</li>
<li><strong>Data migration</strong> devices
<ul>
<li>Snowcone</li>
<li>Snowball Edge</li>
<li>Snowmobile</li>
</ul>
</li>
<li><strong>Edge computing</strong> devices
<ul>
<li>Snowcone</li>
<li>Snowball Edge</li>
</ul>
</li>
<li><strong>When to use Snowball devices?</strong>
<ul>
<li>When migration has challenges such as
<ul>
<li>Limited connectivity and bandwidth</li>
<li>High network cost</li>
<li>Shared bandwidth</li>
<li>Unstable connection</li>
</ul>
</li>
<li>If data migration takes more than a week to transfer over the network</li>
</ul>
</li>
<li><img src="https://d2908q01vomqb2.cloudfront.net/e1822db470e60d090affd0956d743cb0e7cdf113/2020/12/08/AWS-Snow-Family-data-migration-workflow.png" alt="enter image description here"></li>
<li><a href="https://docs.aws.amazon.com/snowball/latest/snowcone-guide/snowcone-what-is-snowcone.html">Snowcone</a>
<ul>
<li>Small portable computing anywhere, rugged &amp; secure, withstand harsh enviroments</li>
<li>Light weight (2.1 kg)</li>
<li>Device used for edge computing, storage, and data transfer</li>
<li>8TB of usable storage</li>
<li>Must provide your won battery</li>
<li>Can be sent back to AWS offline, or connect it to internet and use AWS DataSync to send data</li>
<li><em>Use Cases</em>
<ul>
<li>Use where Snowball does not fir (space-constrained environment)</li>
<li>To collect data, process the data to gain immediate insight, and then transfer the data online to AWS</li>
<li>To distribute media, scientific, or other content from AWS storage services to your partners and customers</li>
</ul>
</li>
</ul>
</li>
<li><a href="https://docs.aws.amazon.com/snowball/latest/developer-guide/whatisedge.html">Snowball Edge</a>
<ul>
<li>Device with on-board storage and compute power for select AWS capabilities</li>
<li>Can do local processing and edge-computing workloads in addition to transferring data between</li>
<li>Each Snowball Edge device can transport data at speeds faster than the internet</li>
<li>This transport is done by shipping the data in the appliances through a regional carrier</li>
<li>The appliances are rugged, complete with E Ink shipping labels</li>
<li>Alternative to moving data over network (avoid paying network fees)</li>
<li>Pay per data transfer job</li>
<li>Provide block storage and S3 compatible object storage</li>
<li>Snowball Edge devices have three options for device configurations
<ul>
<li><em>Storage Optimized</em>
<ul>
<li>80TB of HDD for block volume and S3 compatible object storage <strong>for data transfer</strong></li>
<li>80 TB of usable storage space, 24 vCPUs, and 32 GiB of memory for compute functionality <strong>with EC2 compute functionality</strong></li>
</ul>
</li>
<li><em>Compute Optimized</em>
<ul>
<li>42 TB of HDD capacity for either object storage or block storage volumes</li>
<li>Most compute functionality, with 52 vCPUs, 208 GiB of memory, and 42 TB (39.5 usable) plus 7.68 TB of dedicated NVMe (Non-Volatile Memory Express)  SSD for compute instances for block storage volumes for EC2 compute instances</li>
</ul>
</li>
<li><em>Compute Optimized with GPU</em>
<ul>
<li>Identical to the Compute Optimized option, except for an installed GPU, equivalent to the one available in the P3 Amazon EC2 instance type</li>
<li>42 TB (39.5 TB of HDD storage that can be used for a combination of Amazon S3 compatible object storage and Amazon EBS compatible block storage volumes) plus 7.68 TB of dedicated NVMe SSD for compute instances</li>
</ul>
</li>
</ul>
</li>
<li><em>Use cases</em>
<ul>
<li>Cloud data migration</li>
<li>DC decommission</li>
<li>Disaster recovery</li>
</ul>
</li>
</ul>
</li>
<li>Snowmobile
<ul>
<li>Transfers exabytes of data (1EB = 1000 PB = 10,00,000 TBs)</li>
<li>Each Snowmobiles has 100 PB capacity (use multiple in parallel)</li>
<li>High Security - temperature controlled, GPS, 24x7 video surveillance</li>
<li>Use when want to send more than 10PB of data</li>
</ul>
</li>
<li><strong>Edge Computing</strong>
<ul>
<li>Process data while its being created on a edge location E.g. truck on load, a ship on the sea etc</li>
<li>These location may have limited or no internet</li>
<li>We can setup Snowball Edge / Snowcone device for edge computing</li>
<li>Use cases
<ul>
<li>Pre-process data</li>
<li>Machine learning at the edge</li>
<li>Transcoding media streams</li>
</ul>
</li>
<li>Eventually we can ship back device to AWS</li>
</ul>
</li>
<li><a href="https://docs.aws.amazon.com/snowball/latest/developer-guide/aws-opshub.html">AWS OpsHub</a>
<ul>
<li>It is an application that you can download and install on any Windows or Mac</li>
<li>Graphical user interface you can use to manage your AWS Snowball devices</li>
<li>Enabling you to rapidly deploy edge computing workloads and simplify data migration to the cloud</li>
<li>Supports the Snowball Edge Storage Optimized and Snowball Edge Compute Optimized devices</li>
<li>Monitors devices metrics (Storage capacity, Active instances)</li>
<li>Launches compatible AWS services on your devices (EC2, DataSync, NFS)</li>
</ul>
</li>
<li><strong>Snowball into Glacier</strong>
<ul>
<li>Snowball can not import to Glacier directly</li>
<li>You must import into S3 first then in combination with S3 lifecycle policy move it to Glacier</li>
</ul>
</li>
</ul>
</li>
<li><a href="https://aws.amazon.com/storagegateway/?whats-new-cards.sort-by=item.additionalFields.postDateTime&amp;whats-new-cards.sort-order=desc">Storage Gateway</a>
<ul>
<li>Set of hybrid cloud services that gives you on-premises access to virtually unlimited cloud storage</li>
<li>Bridge Between on-premise data and cloud data in S3</li>
<li>Customers use Storage Gateway to integrate AWS Cloud storage with existing on-site workloads so they can simplify storage management and reduce costs for key hybrid cloud storage use cases</li>
<li><a href="https://aws.amazon.com/storagegateway/file/s3/">Amazon S3 File Gateway</a>
<ul>
<li>Configured S3 buckets are accessible using SMB or NFS protocol with local caching</li>
<li>Supports S3 standard, S3 IA, S3 One Zone IA</li>
<li>Access using IAM roles for each gateway</li>
<li>Can be mounted on may servers</li>
<li>Integrated with Active Directory for user Authentication</li>
<li><img src="https://docs.aws.amazon.com/storagegateway/latest/userguide/images/file-gateway-concepts-diagram.png" alt="enter image description here"></li>
</ul>
</li>
<li><strong>Amazon FSx File Gateway</strong>
<ul>
<li><a href="https://aws.amazon.com/fsx/windows/">FSx for windows</a>
<ul>
<li>EFS is a shared POSIX system for Linux system and Windows File Server can not use EFS</li>
<li>To solve this, <em>FSx for windows</em> is fully managed Windows file system share drive</li>
<li>Supports SMB protocol &amp; Windows NTFS</li>
<li>Has Microsoft AD integration</li>
<li>Scale up to 10s of GB/s, millions of IOPS, 100s PB of data</li>
<li>Can be accessed from your on-premise infra</li>
<li>Can be configures to be Multi-AZ</li>
<li>Data backed up S3 daily</li>
<li><img src="https://res.infoq.com/news/2021/05/amazon-fsx-file-gateway/en/resources/1FSx-1619859707544.png" alt="enter image description here"></li>
</ul>
</li>
<li><a href="https://aws.amazon.com/fsx/lustre/">FSx for Lustre</a>
<ul>
<li>Type of parallel distributed file system for large scale computing</li>
<li><strong>L</strong> inux Cl <strong>uster</strong></li>
<li>Scales up to 100s GB/s, millions of IOPS</li>
<li>Seamless integration with S3 (<strong>Can read S3 as file system through FSx</strong>)</li>
<li><strong>Use cases</strong>
<ul>
<li>Machine Learning</li>
<li>Video Processing</li>
<li>Financial modelling</li>
</ul>
</li>
<li><strong>File System Deployment options</strong>
<ul>
<li><em>Scratch File System</em>
<ul>
<li>Temporary storage</li>
<li>Data is not replicated</li>
<li>High Burst</li>
<li>For short term processing</li>
</ul>
</li>
<li><em>Persistent File System</em>
<ul>
<li>Long-term storage</li>
<li>data is replicated</li>
<li>Replace failed files within mins</li>
<li>For long term processing, sensitive data</li>
</ul>
</li>
</ul>
</li>
<li><img src="https://d2908q01vomqb2.cloudfront.net/e1822db470e60d090affd0956d743cb0e7cdf113/2020/04/24/FSx-for-Lustre-persistent-storage-diagram.png" alt="enter image description here" width="500" height="500"></li>
</ul>
</li>
</ul>
</li>
<li><a href="https://aws.amazon.com/storagegateway/vtl/">Tape Gateway</a>
<ul>
<li>Backup process using physical tapes</li>
<li>With Tape gateway, company use same process but, in the cloud</li>
<li>Virtual Tape Library (VTL) backed by S3 and Glacier</li>
<li>Backup data using existing tape-based processes</li>
<li><img src="https://d1.awsstatic.com/cloud-storage/tape-gateway-diagram.4b6ca2b4e3f97d4df7988544400ae91424503248.png" alt="enter image description here"></li>
</ul>
</li>
<li><a href="https://aws.amazon.com/storagegateway/volume/">Volume Gateway</a>
<ul>
<li>Block storage using iSCSI protocol backed by S3</li>
<li>Backed by EBS snapshots to restore on-premises volumes</li>
<li><em>Cached Volume</em>-  low latency access to most recent data</li>
<li><em>Stored Volume</em> - entire dataset is on premise, schedule backups to S3</li>
<li><img src="https://d1.awsstatic.com/cloud-storage/volume-gateway-diagram.eedd58ab3fb8a5dcae088622b5c1595dac21a04b.png" alt="enter image description here"></li>
</ul>
</li>
<li><em>On-premised data to the cloud &gt;&gt; Use Storage Gateway</em><br>
- <em>File access / NFS with AD &gt;&gt; File Gateway (Backed by S3)</em><br>
- <em>Volume / Block Storage / iSCSI &gt;&gt; Volume Gateway (Backed by S3 with EBS snapshot)</em><br>
- <em>VTL Tape/Backup with iSCSI &gt;&gt; Tape Gateway  (Backed by S3 and Glacier)</em><br>
- <em>No on-premise virtualization&gt;&gt; Hardware appliances</em></li>
<li>AWS transfer family
<ul>
<li><img src="https://d1.awsstatic.com/cloud-storage/aws-transfer-family-s3-efs-how-it-works-diagram.6ff1dc0d717f63d4207ce4670a729aabb85d0d70.png" alt="AWS Transfer Family | Amazon Web Services"></li>
</ul>
</li>
<li>AWS Storage options
<ul>
<li><img src="https://d2908q01vomqb2.cloudfront.net/cb4e5208b4cd87268b208e49452ed6e89a68e0b8/2018/03/20/aws-storage-soutions.jpg" alt="enter image description here"></li>
<li><img src="https://miro.medium.com/max/1838/1*02FpTqeqNH6XzcrBUqjR_w.png" alt="enter image description here" width="600" height="600"></li>
</ul>
</li>
</ul>
</li>
</ul>
<hr>
<p>Q: My company would like to have a MySQL database internally that is going to be available even in  case of a disaster in the AWS Cloud. I should setup</p>
<blockquote>
<p>Multi-AZ (In this question, we consider a disaster to be an entire Availability Zone going down. In<br>
which case Multi-AZ will help. If we want to plan against an entire region going down, backups and<br>
replication across regions would help.)</p>
</blockquote>
<p>Q: Our RDS database struggles to keep up with the demand of the users from our website. Our million users mostly read news, and we don’t post news very often. Which solution is NOT adapted to this problem?</p>
<blockquote>
<p>RDS Multi-AZ (ElastiCache and RDS Read Replicas do indeed help with scaling reads.)</p>
</blockquote>
<p>Q: We have setup read replicas on our RDS database, but our users are complaining that upon updating  their social media posts, they do not see the update right away</p>
<blockquote>
<p>Read Replicas have asynchronous replication and therefore it’s likely our users will only observe<br>
eventual consistency</p>
</blockquote>
<p>Q: Which RDS Classic (not Aurora) feature does not require us to change our SQL connection string?</p>
<blockquote>
<p>Multi-AZ (Multi AZ keeps the same connection string regardless of which database is up. Read<br>
Replicas imply we need to reference them individually in our application as each read replica will<br>
have its own DNS name)</p>
</blockquote>
<p>Q: You want to ensure your Redis cluster will always be available</p>
<blockquote>
<p>Enable Multi-AZ (Multi AZ ensures high availability)</p>
</blockquote>
<p>Q: Your application functions on an ASG behind an ALB. Users have to constantly log back in and you’d  rather not enable stickiness on your ALB as you fear it will overload some servers. What should you  do?</p>
<blockquote>
<p>Store session data in ElastiCache (Storing Session Data in ElastiCache is a common pattern to<br>
ensuring different instances can retrieve your user’s state if needed.)</p>
</blockquote>
<p>Q: One analytics application is currently performing its queries against your main production  database. These queries slow down the database which impacts the main user experience. What should you do to improve the situation?</p>
<blockquote>
<p>Setup Read Replica</p>
</blockquote>
<p>Q: You have a requirement to use TDE (Transparent Data Encryption) on top of KMS. Which database technology does NOT support TDE on RDS?</p>
<blockquote>
<p>PostgreSQL</p>
</blockquote>
<p>Q: You would like to ensure you have a database available in another region if a disaster happens to your main region. Which database do you recommend?</p>
<blockquote>
<p>Aurora Global Database (Global Databases allow you to have cross region replication, RDS does not<br>
support replication at cross region level)</p>
</blockquote>
<p>Q: How can you enhance the security of your Redis cache to force users to enter a password?</p>
<blockquote>
<p>Use Redis Auth</p>
</blockquote>
<p>Q: Your company has a production Node.js application that is using RDS MySQL 5.6 as its data backend.  A new application programmed in Java will perform some heavy analytics workload to create a  dashboard, on a regular hourly basis. You want to the final solution to minimize costs and have minimal disruption on the production application, what should you do?</p>
<blockquote>
<p>Create a Read Replica in a different AZ and run the analytics workload on the replica database</p>
</blockquote>
<p>Q: You would like to create a disaster recovery strategy for your RDS PostgreSQL database so that in case of a regional outage, a database can be quickly made available for Read and Write workload in another region. The DR database must be highly available. What do you recommend?</p>
<blockquote>
<p>Create a Read Replica in a different region and enable multi-AZ on the main database</p>
</blockquote>
<p>Q: You are managing a PostgreSQL database and for security reasons, you would like to ensure users are authenticated using short-lived credentials. What do you suggest doing?</p>
<blockquote>
<p>Use PostgreSQL for RDS and authenticate using a token obtained through the RDS service.<br>
(In this case, IAM is leveraged to obtain the RDS service token, so this is the IAM<br>
authentication use case.)</p>
</blockquote>
<p>Q: Which RDS database technology does NOT support IAM authentication?</p>
<blockquote>
<p>Oracle</p>
</blockquote>
<p>Q: An application is running in production, using an Aurora database as its backend. Your development team would like to run a version of the application in a scaled-down application, but still, be able to perform some heavy workload on a need-basis. Most of the time, the application will be unused. Your CIO has tasked you with helping the team while minimizing costs. What do you suggest?</p>
<blockquote>
<p>Use Aurora Serverless</p>
</blockquote>
<p>Q: You’re trying to upload a 25 GB file on S3 and it’s not working</p>
<blockquote>
<p>Should use multipart upload for file bigger than 5GB (Multi Part Upload is also recommended as soon as the file is over 100MB)</p>
</blockquote>
<p>Q: I tried creating an S3 bucket named “dev” but it didn’t work. This is a new AWS Account and I have no buckets at all. What is the cause?</p>
<blockquote>
<p>Bucket names must be globally unique and “dev” is already taken</p>
</blockquote>
<p>Q: You’ve added files in your bucket and then enabled versioning. The files you’ve already added will have which version?</p>
<blockquote>
<p>null</p>
</blockquote>
<p>Q: Your client wants to make sure the encryption is happening in S3, but wants to fully manage the encryption keys and never store them in AWS. You recommend</p>
<blockquote>
<p>SSE-C (Here you have full control over the encryption keys, and let AWS do the encryption)</p>
</blockquote>
<p>Q: Your company wants data to be encrypted in S3, and maintain control of the rotation policy for the encryption keys, but not know the encryption keys values. You recommend</p>
<blockquote>
<p>SSE-KMS (With SSE-KMS you let AWS manage the encryption keys but you have full control of the key rotation policy)</p>
</blockquote>
<p>Q: Your company does not trust S3 for encryption and wants it to happen on the application. You recommend</p>
<blockquote>
<p>Client Side Encryption (With Client Side Encryption you perform the encryption yourself and send the encrypted data to AWS directly. AWS does not know your encryption keys and cannot decrypt your data.)</p>
</blockquote>
<p>Q: The bucket policy allows our users to read/write files in the bucket, yet we were not able to perform a PutObject API call.</p>
<blockquote>
<p>The IAM user must have an explicit DENY in the attached IAM policy (Explicit DENY in an IAM policy will take precedence over a bucket policy permission)</p>
</blockquote>
<p>Q: You have a website that loads files from another S3 bucket. When you try the URL of the files directly in your Chrome browser it works, but when the website you’re visiting tries to load these files it doesn’t. What’s the problem?</p>
<blockquote>
<p>CORS is wrong (Cross-origin resource sharing (CORS) defines a way for client web applications that are loaded in one domain to interact with resources in a different domain. To learn more about CORS, go here: <a href="https://docs.aws.amazon.com/AmazonS3/latest/dev/cors.html">https://docs.aws.amazon.com/AmazonS3/latest/dev/cors.html</a>)</p>
</blockquote>
<p>Q: You have enabled versioning and want to be extra careful when it comes to deleting files on S3. What should you enable to prevent accidental permanent deletions?</p>
<blockquote>
<p>Enable MFA Delete (MFA Delete forces users to use MFA tokens before deleting objects. It’s an extra level of security to prevent accidental deletes)</p>
</blockquote>
<p>Q: You would like all your files in S3 to be encrypted by default. What is the optimal way of achieving this?</p>
<blockquote>
<p>Enable “Default Encryption” on S3</p>
</blockquote>
<p>Q: You suspect some of your employees to try to access files in S3 that they don’t have access to. How can you verify this is indeed the case without them noticing?</p>
<blockquote>
<p>Enable S3 Access Logs and analyze them using Athena (S3 Access Logs log all the requests made to buckets, and Athena can then be used to run serverless analytics on top of the logs files)</p>
</blockquote>
<p>Q: You are looking for your entire S3 bucket to be available fully in a different region so you can perform data analysis optimally at the lowest possible cost. Which feature should you use?</p>
<blockquote>
<p>S3 cross region replication (S3 CRR is used to replicate data from an S3 bucket to another one in a different region)</p>
</blockquote>
<p>Q: You are looking to provide temporary URLs to a growing list of federated users in order to allow them to perform a file upload on S3 to a specific location. What should you use?</p>
<blockquote>
<p>S3 Pre-Signed URL  (Pre-Signed URL are temporary and grant time-limited access to some actions in your S3 bucket.)</p>
</blockquote>
<p>Q: How can you automate the transition of S3 objects between their different tiers?</p>
<blockquote>
<p>Use S3 Lifecycle rules</p>
</blockquote>
<p>Q: Which of the following is NOT a Glacier retrieval mode?</p>
<blockquote>
<p>Instant (10s)</p>
</blockquote>
<p>Q: Which of the following is a Serverless data analysis service allowing you to query data in S3?</p>
<blockquote>
<p>Athena</p>
</blockquote>
<p>Q: You are looking to build an index of your files in S3, using Amazon RDS PostgreSQL. To build this index, it is necessary to read the first 250 bytes of each object in S3, which contains some metadata about the content of the file itself. There is over 100,000 files in your S3 bucket, amounting to 50TB of data. how can you build this index efficiently?</p>
<blockquote>
<p>Create an application that will traverse the S3 bucket, issue a Byte Range Fetch for the first 250 bytes, and store that information in RDS.</p>
</blockquote>
<p>Q: You need to move hundreds of Terabytes into the cloud in S3, and after that pre-process it using many EC2 instances in order to clean the data. You have a 1 Gbit/s broadband and would like to optimize the process of moving the data and pre-processing it, in order to save time. What do you recommend?</p>
<blockquote>
<p>Use Snowball Edge (Snowball Edge is the right answer as it comes with computing capabilities and allows use to pre-process the data while it’s being moved in Snowball, so we save time on the pre-processing side as well)</p>
</blockquote>
<p>Q: You want to expose a virtually infinite storage for your tape backups. You want to keep the same software as today and want a iSCSI compatible interface. What do you use?</p>
<blockquote>
<p>Tape Gateway</p>
</blockquote>
<p>Q: Your EC2 Windows Servers need to share some data by having a Network File System mounted, that respect the Windows security mechanisms and has integration with Active Directory. What do you recommend putting in place as an NFS?</p>
<blockquote>
<p>FSx for windows</p>
</blockquote>
<p>Q: You would like to have a distributed POSIX compliant file system that will allow you to maximize the IOPS in order to perform some HPC and genomics computational research. That file system will have to scale easily to millions of IOPS. What do you recommend?</p>
<blockquote>
<p>FSx for Lustre</p>
</blockquote>
<pre><code>Important ports:
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
</code></pre>
<h2 id="route-53">Route 53</h2>
<ul>
<li><a href="https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/Welcome.html">Docs</a></li>
<li>DNS (Domain name system) is collection of rules and records which helps client to understand how to reach a server<br>
through its domain name</li>
<li>Amazon Route 53 is a highly available and scalable Domain Name System (DNS) web service. You can use Route 53<br>
to perform three main functions in any combination: domain registration, DNS routing, and health checking.</li>
<li>In aws, more common records are
<ul>
<li>A: Hostname to IPv4</li>
<li>AAAA: hostname to IPv6</li>
<li>CNAME: hostname to hostname (only work for non root domain e.g. works for <a href="http://mydomain.root.com">mydomain.root.com</a><br>
but not for <a href="http://root.com">root.com</a>)</li>
</ul>
</li>
<li>Alias: hostname to AWS resource (free of charge and has native health check)</li>
<li>Web browser makes DNS request for <a href="http://myapp.domain.com">myapp.domain.com</a> to Route 53, and ot will send back IP for 	application server</li>
<li>Can be use to
<ul>
<li>resolve public domain name</li>
<li>private domain name that can be resolved by your instance in your VPCs</li>
</ul>
</li>
<li>Support load balancing (client load balancing)</li>
<li>Support routing policies</li>
<li>No free tier</li>
<li>win : nslookup <a href="http://mydomain.com">mydomain.com</a></li>
<li>TTL (Time to live) Route 53 sends IP and TTL(e.g. 300s), so browser DNS cache IP for 300s only
<ul>
<li>High TTL for less traffic on DNS</li>
<li>Low TTL for high traffic on DNS</li>
<li>Mandatory for each DNS record</li>
</ul>
</li>
<li>Health checks
<ul>
<li>Default health check interval 30s</li>
<li>15 health checkers will check the endpoint health</li>
<li>One request / 2s</li>
<li>Can have HTTP/TCP/HTTPS health check (no SSL)</li>
<li>Can integrate with CloudWatch</li>
<li>can be linked with DNS queries</li>
</ul>
</li>
<li>Routing Policy
<ul>
<li>Simple: use when need to redirect single resource, can health check, if multiple values<br>
are returned then random one is chosen by the client</li>
<li>Weighted: % of requests goes to specific endpoints, split traffic between regions</li>
<li>Latency: redirect to server that has least latency close to us, useful when latency is<br>
a priority, evaluated in terms of user location to designated AWS region</li>
<li>Failover: If health check of primary endpoint fails then DNS request redirected to disaster<br>
recovery endpoint</li>
<li>Geolocation: Route traffic to your resource based on user location with default redirect</li>
<li>Geoproximity: Route traffic to your resource based on the geographic locations of the users,<br>
has ability to shift more on defined bias values, need Route 53 Traffic flow (advanced)</li>
<li>Multi Value: Use when traffic to multi-resources with associated health checks, up to 8 healthy<br>
records are returned for each multi-value query, not a substitute for ELB</li>
<li>Route 53 also a Registrar which manage reserved internet domain (Domain name != Registrar)</li>
</ul>
</li>
</ul>
<hr>
<p>Q: You have purchased “<a href="http://mycoolcompany.com">mycoolcompany.com</a>” on the AWS registrar and would like for it to point to<br>
<code>lb1-1234.us-east-2.elb.amazonaws.com</code> . What sort of Route 53 record is <strong>NOT POSSIBLE</strong> to set<br>
up for this?</p>
<blockquote>
<p>CNAME (The DNS protocol does not allow you to create a CNAME record for the top node of a DNS<br>
namespace (<a href="http://mycoolcompany.com">mycoolcompany.com</a>), also known as the zone apex)</p>
</blockquote>
<p>Q: You have deployed a new Elastic Beanstalk environment and would like to direct 5% of your  production traffic to this new environment, in order to monitor for CloudWatch metrics and  ensuring no bugs exist. What type of Route 53 records allows you to do so?</p>
<blockquote>
<p>Weighted  (Weighted allows you to redirect a part of the traffic based on a weight<br>
(hence a percentage). It’s common to use to send a part of a traffic to a new application<br>
you’re deploying</p>
</blockquote>
<p>Q: After updating a Route 53 record to point “<a href="http://myapp.mydomain.com">myapp.mydomain.com</a>” from an old Load Balancer to a new load balancer, it looks like the users are still not redirected to your new load balancer. You are wondering why…</p>
<blockquote>
<p>Becuase of TTL (DNS records have a TTL (Time to Live) in order for clients to know for how long<br>
to caches these values and not overload the DNS with DNS requests. TTL should be set to strike a<br>
balance between how long the value should be cached vs how much pressure should go on the DNS.)</p>
</blockquote>
<p>Q: You want your users to get the best possible user experience and that means minimizing the response time from your servers to your users. Which routing policy will help?</p>
<blockquote>
<p>Latency (Latency will evaluate the latency results and help your users get a DNS response that<br>
will minimize their latency (e.g. response time))</p>
</blockquote>
<p>Q: You have a legal requirement that people in any country but France should not be able to access  your website. Which Route 53 record helps you in achieving this?</p>
<blockquote>
<p>Geolocation</p>
</blockquote>
<p>Q: You have purchased a domain on Godaddy and would like to use it with Route 53. What do you need to change to make this work?</p>
<blockquote>
<p>Create a public hosted zone and update the 3rd party registrar NS records (Private hosted<br>
zones are meant to be used for internal network queries and are not publicly accessible.<br>
Public Hosted Zones are meant to be used for people requesting your website through the<br>
public internet. Finally, NS records must be updated on the 3rd party registrar.)</p>
</blockquote>
<h2 id="case-studies">Case Studies</h2>
<ul>
<li>We’re considering 5 pillars for a well architect application: Cost, Performance, Reliability, Security &amp; Operational Excellence</li>
<li><a href="http://WhatIsTheTime.com">WhatIsTheTime.com</a> (Stateless)
<ul>
<li>No Database</li>
<li>Low latency selected</li>
<li>Setup private EC2 Instances
<ul>
<li>In different AZs for [Availability and Disaster recovery]</li>
<li>with ASG  (we can add and remove instances at run time) [Better Costing]</li>
<li>Setup SG (EC2 will on receive request for ELB) [Security]</li>
<li>Reserving instance for minimum capacity [Better Costing]</li>
</ul>
</li>
<li>Add Multi AZ ELB [Performance]</li>
<li>Health Check [Performance]</li>
<li>Use Route 53 to manage DNS queries [Reliability]</li>
<li>Fully Automated like ASG, ELB and Route 53 [Operational Excellence]</li>
</ul>
</li>
<li>MyClothes (Stateful)
<ul>
<li>Require database with hundreds of Users the same time, Need vertically scaling</li>
<li>Introduce Route 53</li>
<li>Introduce Multi AZ ELB</li>
<li>Setup private EC2 Instances
<ul>
<li>In different AZs</li>
<li>with ASG  (we can add and remove instances at run time)</li>
<li>Setup SG (EC2 will on receive request for ELB)</li>
<li>Reserving instance for minimum capacity</li>
</ul>
</li>
<li>Introduce ElastiCache (Alt DynamoDB)
<ul>
<li>For storing user session data</li>
<li>For caching data from RDS</li>
<li>Multi AZ</li>
</ul>
</li>
<li>Introduce RDS
<ul>
<li>For storing user data</li>
<li>Read replicas for scaling reads</li>
<li>Multi AZ for disaster recovery</li>
</ul>
</li>
<li>Restrict traffic to EC2 SG from ELB</li>
<li>Restrict traffic to ElastiCache SG from the EC2 SG</li>
<li>Restrict traffic to RDS SG from the EC2 SG</li>
</ul>
</li>
<li>MyWordPress (Stateful)
<ul>
<li>Require fully scalable, display images, has user data and blog content stored in MySQL</li>
<li>Introduce Route 53</li>
<li>Introduce Multi AZ ELB</li>
<li>Setup private EC2 Instances
<ul>
<li>In different AZs</li>
<li>with ASG  (we can add and remove instances at run time)</li>
<li>Setup SG (EC2 will on receive request for ELB)</li>
<li>Reserving instance for minimum capacity</li>
<li>Create ENI (Elastic network Interface) in each AZ to access EFS</li>
</ul>
</li>
<li>Introduce EFS (EBS good for single instance only)
<ul>
<li>Elastic File System</li>
<li>To store images in distributed application</li>
</ul>
</li>
<li>Introduce RDS
<ul>
<li>For storing user data using MySQL</li>
<li>Read replicas for scaling reads</li>
<li>Multi AZ for disaster recovery</li>
</ul>
</li>
<li>Restrict traffic to EC2 SG from ELB</li>
<li>Restrict traffic to EFS SG from the EC2 SG</li>
<li>Restrict traffic to RDS SG from the EC2 SG</li>
</ul>
</li>
<li><a href="https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/Welcome.html">Elastic Beanstalk</a>
<ul>
<li>Beanstalk, you can quickly deploy and manage applications in the AWS Cloud without having to learn about<br>
the infrastructure that runs those applications.</li>
<li>After you create and deploy your application, information about the application—including metrics, events, and<br>
environment status—is available through the Elastic Beanstalk console, APIs, or Command Line Interfaces, including<br>
the unified AWS CLI</li>
<li>Developer centric</li>
<li>Managed service, instance configuration and deployment auto handled</li>
<li>Models
<ul>
<li>Single instance development</li>
<li>LB + ASG great for prd and pre-prd web apps</li>
</ul>
</li>
<li>Components
<ul>
<li>Application</li>
<li>Application version for each deployment</li>
<li>Environment name</li>
</ul>
</li>
<li>Rollback feature to previous version</li>
<li>Full control over lifecycle of environments</li>
</ul>
</li>
</ul>
<hr>
<p>Q:  You have an ASG that scales on demand based on the traffic going to your new website: <a href="http://TriangleSunglasses.Com">TriangleSunglasses.Com</a>. You      would like to optimise for cost, so you have selected an ASG that scales based on demand going through your ELB. Still,      you want your solution to be highly available so you have selected the minimum instances to 2. How can you further      optimize the cost while respecting the requirements?</p>
<blockquote>
<p>Reserve two EC2 instances (This is the way to save further costs as we know we will run 2 EC2 instances no matter what.)</p>
</blockquote>
<p>Q: Which of the following will <strong>NOT</strong> help make our application tier stateless?</p>
<blockquote>
<p>Storing shared data on EBS volumes (EBS volumes are created for a specific AZ and can only be attached to one EC2<br>
instance at a time. This will not help make our application stateles)</p>
</blockquote>
<p>Q:  You are looking to store shared software updates data across 100s of EC2 instances. The software updates should be      dynamically loaded on the EC2 instances and shouldn’t require heavy operations. What do you suggest?</p>
<blockquote>
<p>Store the software updates on EFS and mount EFS as a network drive (EFS is a network file system (NFS) and allows to mount the same file system to 100s of EC2 instances. Publishing software updates their allow each EC2 instance to access them.)</p>
</blockquote>
<p>Q: As a solution architect managing a complex ERP software suite, you are orchestrating a migration to the AWS cloud. The     software traditionally takes well over an hour to setup on a Linux machine, and you would like to make sure your      application does leverage the ASG feature of auto scaling based on the demand. How do you recommend you speed up<br>
the installation process?</p>
<blockquote>
<p>Use golden AMI (Golden AMI are a standard in making sure you snapshot a state after an application installation so that future instances can boot up from that AMI quickly.)</p>
</blockquote>
<p>Q: I am creating an application and would like for it to be running with minimal cost in a development environment with     Elastic Beanstalk. I should run it in</p>
<blockquote>
<p>Single Instance Mode (This will create one EC2 instance and one Elastic IP)</p>
</blockquote>
<p>Q: My deployments on Elastic Beanstalk have been painfully slow, and after looking at the logs, I realize this is due to the    fact that my dependencies are resolved on each EC2 machine at deployment time. How can I speed up my deployment     with the minimal impact?</p>
<blockquote>
<p>Create a Golden AMI that contains the dependencies and launch the EC2 instances from that. (Golden AMI are a standard in making sure save the state after the installation or pulling dependencies so that future instances can boot up from that AMI quickly)</p>
</blockquote>
<h2 id="cloudfront--aws-global-accelerator">CloudFront &amp; AWS Global Accelerator</h2>
<ul>
<li><a href="https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/Introduction.html">CloudFront</a>
<ul>
<li>It is a fast content delivery network (CDN) service that securely delivers data, videos, applications, and APIs to customers globally with low latency, high transfer speeds, all within a developer-friendly environment</li>
<li>Speeds up distribution of your static and dynamic web content, such as .html, .css, .js, and image files, to your users</li>
<li>Delivers your content through a worldwide network of data centers called edge locations (216 Point of Presence globally)</li>
<li>CloudFront speeds up the distribution of your content by routing each user request through the AWS backbone network to the edge location that can best serve your content</li>
<li>DDoS protection, integration with shield, AWS Web Application firewall</li>
<li>Can expose external HTTPS and can talk to internal HTTPS backends</li>
<li>If the content is already in the edge location with the lowest latency, CloudFront delivers it immediately</li>
<li>If you are accessing content from USA and the content is not in that edge location, CloudFront retrieves it from an origin that you’ve defined—such as an Amazon S3 bucket in Australia, a MediaPackage channel, or an HTTP server that you have identified as the source for the definitive version of your content</li>
<li>Files are cached for TTL (At edge locations upon request)</li>
<li>Origins
<ul>
<li>S3 as an Origin
<ul>
<li>For distributing files and caching them at the edge</li>
<li>Enhancing security with CloudFront Origin Access Identity (OAI)</li>
<li>CloudFront can be used as an ingress (to upload file to S3)</li>
<li>We can limit the S3 bucket to be accessed only using OAI</li>
</ul>
</li>
<li>Custom Origin (HTTP)
<ul>
<li>ALB (must be public and SG must allowed CloudFront requests, here backend instance can be private)</li>
<li>EC2 Instance (must be public and SG must allowed CloudFront requests)</li>
<li>S3 website (must enable bucket as a static S3 website)</li>
<li>Any HTTP backend you want</li>
</ul>
</li>
<li><img src="https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/images/how-cloudfront-delivers-content.png" alt="AmazonCloudFront"></li>
</ul>
</li>
<li>GeoRestriction
<ul>
<li>You can restrict who can access distribution</li>
<li>Whitelist: allow access to content only if they’re in one of the countries on a list of approved countries</li>
<li>Blacklist: prevent access to your content if they’re in one of the countries on a list of banned countries</li>
<li>The Country determined using 3rd party Geo-IP database</li>
<li>Use case: Copyright laws to control access to content</li>
</ul>
</li>
<li>Signed URL/ Signed Cookies
<ul>
<li>When you want to distribute paid shared content to premium users</li>
<li>We can sign URL/Cookie by attaching policy with
<ul>
<li>URL expiration</li>
<li>IP ranges to access data from</li>
<li>Trusted signer</li>
</ul>
</li>
<li>URL validity for
<ul>
<li>Shared content like movie or music - make it short</li>
<li>Private content - make it last for years</li>
</ul>
</li>
<li>Signed URL
<ul>
<li>Access to individual files</li>
<li>One signed URL per file</li>
<li>Allow access to a path, no matter the origin</li>
<li>Account wide key-pair, only root can manage it</li>
<li>Can filter by IP, path, date, expiration</li>
<li>Can leverage caching feature</li>
<li>S3 pre-signed URL
<ul>
<li>Issue a request as the person who pre-signed the URL</li>
<li>Used IAM key of the signing principle</li>
<li>Limited lifetime</li>
</ul>
</li>
</ul>
</li>
<li>Signed Cookies
<ul>
<li>Access to multiple files</li>
<li>One singed cookie for many files</li>
</ul>
</li>
</ul>
</li>
<li>Pricing
<ul>
<li>Cost of data per edge location varies</li>
<li>You can reduce the number of edge locations for cost reduction</li>
<li>3 price classes
<ul>
<li>Class All : All regions, best performance</li>
<li>Class 200: Most regions, but exclude expensive</li>
<li>Class 100: Only least expensive regions</li>
</ul>
</li>
</ul>
</li>
<li>Multiple Origin
<ul>
<li>Route to different kind of origins based on Content type and Path pattern (e.g. /images/*)</li>
</ul>
</li>
<li>Origin group
<ul>
<li>To increase high-availability</li>
<li>Has one primary and one secondary origin</li>
<li>If primary fails the second one is used</li>
</ul>
</li>
<li>Field Level Encryption
<ul>
<li>Protect used sensitive data trough application stack</li>
<li>Adds an additional layers of security along with HTTPS</li>
<li>Encrypted at the edge close to user</li>
</ul>
</li>
</ul>
</li>
<li><a href="https://docs.aws.amazon.com/global-accelerator/latest/dg/what-is-global-accelerator.html">AWS Global Accelerator</a>
<ul>
<li>Service in which you create <em>accelerators</em> to improve the performance of your applications for local and global users</li>
<li>Standard accelerator
<ul>
<li>You can improve availability of your internet applications that are used by a global audience</li>
<li>With a standard accelerator, Global Accelerator directs traffic over the AWS global network to endpoints in the nearest Region to the client</li>
<li>Provides you with two static IP addresses that you associate with your accelerator</li>
</ul>
</li>
<li>Custom routing accelerator
<ul>
<li>You can map one or more users to a specific destination among many destinations</li>
</ul>
</li>
<li>Unicast vs Anycast IP
<ul>
<li>Unicast
<ul>
<li>One server holds one IP address</li>
</ul>
</li>
<li>Anycast
<ul>
<li>All server holds same IP address and the client routed to the nearest one</li>
<li>AGA used Anycast IP address</li>
<li>2 anycast IP created for your application</li>
<li>Anycast IP send traffic directly to Edge locations and Edge locations send traffic to your application</li>
</ul>
</li>
</ul>
</li>
<li>Works with Elastic IP, EC2 instances, ALB, NLB</li>
<li>Failover less than 1 min for unhealthy, great for disaster recovery</li>
<li>Use cases
<ul>
<li>Non-HTTP use cases like gaming(UDP), IoT(MQTT) or Voice over IP</li>
<li>HTTP use cases that require static IP addresses</li>
<li>HTTP use cases that require deterministic, fast regional failover</li>
</ul>
</li>
<li><img src="https://miro.medium.com/max/1200/1*1iQaCMjicPnfR7LsFZ2hrg.png" alt="enter image description here"></li>
</ul>
</li>
</ul>
<p>Q: Which features allows us to distribute paid content from S3 securely, globally, if the S3 bucket is secured to only exchange data with CloudFront?</p>
<blockquote>
<p>CloudFront Signed URL (CloudFront Signed URL are commonly used to distribute paid content through dynamic CloudFront Signed URL generation)</p>
</blockquote>
<p>Q: You are hosting highly dynamic content in Amazon S3 in us-east-1. Recently, there has been a need to make that data available with low latency in Singapore. What do you recommend using?</p>
<blockquote>
<p>S3 CRR (S3 CRR allows you to replicate the data from one bucket in a region to another bucket in another region)</p>
</blockquote>
<p>Q: How can you ensure that only users who access our website through Canada are authorized in CloudFront?</p>
<blockquote>
<p>Use CloudFront Geo Restriction</p>
</blockquote>
<p>Q: You would like to provide your users access to hundreds of private files in your CloudFront distribution, which is fronting an HTTP web server behind an application load balancer. What should you use?</p>
<blockquote>
<p>CloudFront Signed Cookies (Allows you to access many files)</p>
</blockquote>
<p>Q: You are creating an application that is going to expose an HTTP REST API. There is a need to provide request routing rules at the HTTP level. Due to security requirements, your application can only be exposed through the use of two static IPs. How can you create a solution that validates these requirements?</p>
<blockquote>
<p>Use Global Accelerator and an Application Load Balancer (Global Accelerator will provide us with the two static IP, and the ALB will provide use with the HTTP routing rules)</p>
</blockquote>
<p>Q:What does this S3 bucket policy do?</p>
<pre><code>{
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
</code></pre>
<blockquote>
<p>Only allows the S3 bucket content to be accessed from your CloudFront distribution origin identity</p>
</blockquote>
<h2 id="decoupling-applications">Decoupling applications</h2>
<ul>
<li>
<p>Deploying multiple applications will need to communicate with each other and there are two types of patterns generally used for communication</p>
</li>
<li>
<p>Synchronous communications (Application to Application)</p>
</li>
<li>
<p>Asynchronous OR Event based communications (Application&gt; Queue &gt; Application)</p>
</li>
<li>
<p>As application workload increases Synchronous communications become problematic, in that case its better to break monolith and decouple your applications and scale them independently, in such case Asynchronous communication model as mentioned below is good choice</p>
<ul>
<li>SQS - Queue model</li>
<li>SNS - pub/sub model</li>
<li>Kinesis - real-time streaming model</li>
</ul>
</li>
<li>
<p><a href="https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/welcome.html">Amazon SQS</a></p>
<ul>
<li>Offers a secure, durable, and available hosted queue that lets you integrate and decouple distributed software systems and components</li>
<li>Supports both standard and FIFO queues</li>
<li><strong>Feature</strong>
<ul>
<li>Unlimited throughput, unlimited number of messages in queue</li>
<li>Default retention of messages 4 days, max 14 days</li>
<li>Low latency (&lt; 10ms)</li>
<li>256kb per message sent</li>
<li>Can have duplicate messages (at least once delivery)</li>
<li>Can have out of order messages (best effort ordering)</li>
</ul>
</li>
<li><strong>Producer</strong>
<ul>
<li>Send message to SQS using the SDK (SendMessage API)</li>
<li>The message is persisted in SQS until a consumer deletes it</li>
<li>Order will be sent in message (like OrderId) and consumer will take care of ordering</li>
</ul>
</li>
<li><strong>Consumer</strong>
<ul>
<li>Poll SQS for messages (receive up to 10 messages at a time)</li>
<li>Process the message</li>
<li>Consumers can be running on EC2 instances, servers or AWS Lambda</li>
<li>Delete message using DeleteMessage API</li>
<li><em>Multiple EC2 instance Consumers</em>
<ul>
<li>Receive and process messages in parallel</li>
<li>At least one delivery</li>
<li>Best effort message ordering</li>
<li>Delete message after processing</li>
</ul>
</li>
</ul>
</li>
<li><strong>Scaling</strong>
<ul>
<li>We can scale consumers horizontally to improve throughput of processing</li>
<li>ASG of EC2 can launch or terminate instance depending on number of messages in SQS</li>
<li><img src="https://docs.aws.amazon.com/autoscaling/ec2/userguide/images/sqs-as-custom-metric-diagram.png" alt="enter image description here"></li>
</ul>
</li>
<li><strong>Security</strong>
<ul>
<li><em>Encryption</em>
<ul>
<li>In-flight using HTTPS API</li>
<li>At-rest using KMS keys</li>
<li>Client-side encryption</li>
</ul>
</li>
<li><em>Access controls</em> using IAM policies</li>
<li><a href="https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-basic-examples-of-sqs-policies.html">Access policies</a>
<ul>
<li>Similar to S3 bucket policies</li>
<li>Cross Account Access - create queue account policy<br>
{<br>
“Version”: “2012-10-17”,<br>
“Id”: “Queue1_Policy_UUID”,<br>
“Statement”: [{<br>
“Sid”:“Queue1_AllActions”,<br>
“Effect”: “Allow”,<br>
“Principal”: {<br>
“AWS”: [<br>
“arn:aws:iam::111122223333:role/role1”,<br>
“arn:aws:iam::111122223333:user/username1”<br>
]<br>
},<br>
“Action”: “sqs:*”,<br>
“Resource”: “arn:aws:sqs:us-east-2:123456789012:queue1”<br>
}]<br>
}</li>
<li>Publish S3 Event Notifications to SQS queue - create queue account policy with source bucket condition (json)</li>
</ul>
</li>
</ul>
</li>
<li><img src="https://funnelgarden.com/wp-content/uploads/2020/01/AWS-SQS-Simple-Queue-Service-1024x379.png" alt="enter image description here" width="800" height="300"></li>
</ul>
</li>
</ul>
<h2 id="aws-development">AWS Development</h2>
<ul>
<li><a href="https://docs.aws.amazon.com/cli/latest/reference/s3/">https://docs.aws.amazon.com/cli/latest/reference/s3/</a></li>
<li><a href="https://docs.aws.amazon.com/IAM/latest/UserGuide/tutorial_managed-policies.html">https://docs.aws.amazon.com/IAM/latest/UserGuide/tutorial_managed-policies.html</a></li>
<li><a href="https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_examples.html">https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_examples.html</a></li>
<li><a href="https://cloud.google.com/storage-transfer/docs/using-iam-permissions-and-roles">https://cloud.google.com/storage-transfer/docs/using-iam-permissions-and-roles</a></li>
<li><a href="https://docs.amazonaws.cn/en_us/IAM/latest/UserGuide/id_roles_create_for-user.html">https://docs.amazonaws.cn/en_us/IAM/latest/UserGuide/id_roles_create_for-user.html</a></li>
<li><a href="https://console.aws.amazon.com/iam">https://console.aws.amazon.com/iam</a></li>
</ul>
<hr>
<p>Q: My EC2 Instance does not have the permissions to perform an API call PutObject on S3. What should I do?</p>
<blockquote>
<p>I should ask an administrator to attach a Policy to the IAM Role on my EC2 Instance that authorises it to do the API call (IAM roles are the right way to provide credentials and permissions to an EC2 instance)</p>
</blockquote>
<p>Q: I have an on-premise personal server that I’d like to use to perform AWS API calls</p>
<blockquote>
<p>I should run <code>aws configure</code> and put my credentials there. Invalidate them when I’m done (Even better would be to create a user specifically for that one on-premise server)</p>
</blockquote>
<p>Q: I need my colleagues help to debug my code. When he runs the application on his machine, it’s working fine, whereas I get API authorisation exceptions. What should I do?</p>
<blockquote>
<p>Compare his IAM policy and my IAM policy in the policy simulator to understand the differences</p>
</blockquote>
<p>Q: To get the instance id of my EC2 machine from the EC2 machine, the best thing is to…</p>
<blockquote>
<p>Query the meta data at <a href="http://169.254.169.254/latest/meta-data">http://169.254.169.254/latest/meta-data</a></p>
</blockquote>
