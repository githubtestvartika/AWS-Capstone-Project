To Design and Implement 3-Tier Architecture and helping a team transition from On-Prem to have their footprints in Cloud Journey.
In this task,we will design and deploy a highly available and scalable deployment of the WordPress application stack.
We would use a CloudFormation template to deploy the networking resources required to support the application using task1 cloudformation Template. 
Task 1: Scenario 
The scenario is to deploy a new web application on WordPress on their existing web hosting account. The first step is to gather the requirements for the architecture design.
To get started, the network team gave a list of requirements to ensure there are no issues with the existing landscape.
This includes the Classless Inter-Domain Routing (CIDR) range for the Amazon Virtual Private Cloud (Amazon VPC). Using this address range, build a VPC with two public subnets, two private application subnets, 
and two private database subnets. Make sure you attach an internet gateway to the VPC with a NAT gateway for routing traffic. They also requested two Elastic IP addresses. 
Ensure that you associate the following: 
• The public subnet with the internet gateway 
• The private application subnet and private database subnet with the NAT gateway 
The network team also provided the route tables needed with their subnet associations. 
We got the information request to a CloudFormation template to set up the security groups and outputs needed for future deployments. 
Please review the CloudFormation template with your cloud engineer, deploy the template, and check back with the network team to validate that the build meets all of their requirements.
![image](https://github.com/user-attachments/assets/edde3667-fabf-446a-a8d2-cda8a51fae1e)
![image](https://github.com/user-attachments/assets/628e8662-42e3-4abc-a0b4-da5cd9953515)

******Task 2: Create an Amazon RDS database **
****In this task, you create an Amazon Aurora DB instance and the subnet group to support it. 
**Task 2: Scenario 
**The network team verified the previous deployment requirements and everything looks good. 
The next phase of the project is to establish a secure backend database to give the database administrator (DBA) access for any migration operations before go-live. 
The current database requires a lot of administrative overhead and the business has agreed to move to a managed database service. 
They want their architecture to be highly available, so you recommend that they set up a Multi-AZ RDS Aurora database layer.
After reviewing the proposed design, the DBA has outlined the database requirements. Because of previous performance issues, the database does not require encryption,
and you agree that this is the best choice. 
The existing monitoring solution polls data every 10 minutes. The engineering team doesn’t have room in the budget for additional features; therefore, enhanced monitoring 
is not required. 
**Task 2: Requirements and Configuration** 
DB subnet group 
• As your first step, create a DB subnet group from the Amazon RDS console. 
• Place the subnet group in the Lab VPC. 
• Choose Availability Zone A and Availability Zone B. 
• Choose the subnet with 10.0.4.0/24 CIDR block for the DB Subnet in Availability Zone A. 
• Choose the subnet with 10.0.5.0/24 CIDR block for the DB Subnet in Availability Zone B. 
Amazon Aurora database 
• For the Master username, leave the default value as admin. 
• For the Master password, use the LabPassword from the left of these instructions. 
• The database instances need to be Amazon Aurora with MySql compatible and burstable-performance DB instance. 
• The database instance size needs to be db.t3.med
• The database layer needs to be deployed into the LabVPC. 
• The subnet group needs to be configured with the Database subnets only. 
• The database needs to be configured with the RDS Security Group set up by your security team only. 
• Make sure to provide an Initial database name, such as WPDatabase. 
• Make sure to provide a DB cluster identifier, such as MyDBCluster. 
• Turn off Encryption. 
• Turn off Enhanced monitoring. 
• Turn off Enable auto minor version upgrade. 
• Turn off Enable deletion protection. 
 Note: Take note of the following because you need them later: 
• Master username and Master password 
• Initial database name 
• Writer endpoint
**Task 3: Scenario** 
Example Corp. is having issues with the lead time on ordering new hardware. This is slowing down their ability to onboard new customers and expand the business. 
The SysOps team has a request for a storage solution built for the cloud. They need to be able to confirm that the backup policies and encryption settings meet their 
internal and regulatory compliance requirements. Managing time, cost, and compliance gives Example Corp. a competitive advantage. 
You recommend Amazon EFS to the business as a simple, serverless, set-and-forget, elastic file system. With Amazon EFS, they can share data securely, without provisioning 
or managing storage. 
Your task is to create an Amazon EFS file system that meets the SysOps team&apos;s requirements. 
**Task 3: Sub-tasks 
![image](https://github.com/user-attachments/assets/9741c283-27b4-4ab1-a8f9-4f6316d4e8b7)

• Create a new EFS file system. 
• Copy the EFS file system metadata. 
Task 3: Requirements and Configuration 
Customize the creation of an Amazon EFS file system: 
• It must have Regional availability and durability. 
• It needs to be General Purpose performance to control costs.
• It needs to have Bursting throughput to scale the system size. 
• Turn off Automatic backups and Encryption of data at rest. 
• EFS needs to be deployed in Lab VPC. 
• Select Availability Zone ending in &quot;a&quot; and Availability Zone ending in &quot;b&quot;. 
• Assign AppSubnet1 and AppSubnet2 as subnet IDs. 
• Remove the default security group and select EFSMountTargetSecurityGroup. 
• It does not need a file system policy 
**Task 4: Create an Application Load Balancer 
**In this task, you create the Application Load Balancer and a target group. 
**Task 4: Scenario 
**The SysOps team is excited about the new Amazon EFS configuration and eager to address their next pain point. The current application experiences frequent outages
because of variable, unexpected traffic loads. The SysOps team wants to know if AWS has a service to address this issue that can be used at the application layer (Layer 7). 
On further investigation, you recognize the need for an Application Load Balancer for the application servers in the private subnet.
Your task is to deploy and configure an Application Load Balancer with a health check, and register the required targets.
![image](https://github.com/user-attachments/assets/edba0dad-a4d8-4c4a-8490-e9eb37fa8a10)
**Task 4: Sub-tasks 
**• Create an Application Load Balancer. 
• Copy the Application Load Balancer metadata. 
Task 4: Requirements and Configuration 
The Application Load Balancer Target group should be configured to use the LabVPC for load balancing instances with the following properties: 
• Health check path: /wp-login.php 
• Advanced health check settings: 
o Healthy threshold: 2 
o Unhealthy threshold: 10
o Timeout: 50 
o Interval: 60 
• Leave all other settings at their default values. 
The Application Load Balancer needs to be configured in the LabVPC and in the public subnets with the following properties: 
• Subnets: Select all Public subnets. 
• SecurityGroups: Choose AppInstanceSecurityGroup. 
• Listener: Forward to the created target group. 
 Note: Take note of the Application Load Balancer&apos;s DNS name. we would need it later.
 CloudFormation 
In this task, you use a CloudFormation template to deploy the WordPress user data within an Amazon Elastic Compute Cloud (Amazon EC2) Auto Scaling launch template. 
The template includes the Amazon EFS mount points and the Aurora configurations. 
Task 5: Scenario 
Up to this point, you created the underlying network resources, the database, an Amazon EFS file system, and an Application Load Balancer. It’s time to put it all together.
Example Corp. already has a WordPress account, so the cloud engineer uses the settings and configuration from their existing environment to create a CloudFormation template. 
This includes all of the new resources you provisioned to set up a launch template. Review the CloudFormation template and create all of the necessary resources.
Pay special attention to the user data script. Check for any errors in the deployment and address those, if needed. 
After that is completed, you can create the application servers to support traffic to the new environment. 
Task 5: Sub-tasks 
• Obtain and review the CloudFormation template. 
• Upload the template to create the CloudFormation stack. 
• View created resources from the console. 
• View the created launch template. 
Task 5: Requirements and Configuration 
• Open the context (right-click) menu on this 
Task5.yaml
 link, and choose the option to save the CloudFormation template to your computer. 
 Note: You can save this CloudFormation template for future use. If you choose to build it on your AWS account in the future, tailor the configuration to your requirements. 
• Open the downloaded file in a text editor (not a word processor). 
• Upload the template file in CloudFormation. 
• Using the values saved from previous tasks, update the following Parameters values at the time of stack creation: 
o DB Name: Paste the initial database name (from Task 2).  Note: Make sure that you paste the initial database name, not the cluster name
o Database endpoint: Paste the Writer endpoint (from Task 2). 
o Database User Name: Paste the Master username (from Task 2). 
o Database Password: Paste the Master password (from Task 2). 
o WordPress admin username: Defaults to wpadmin. 
o WordPress admin password: Paste the LabPassword value from the left side of these lab instructions. 
o WordPress admin email address: Input a valid email address. 
o Instance Type: Defaults to t3.medium. 
o ALBDnsName: Paste the DNS name value (from Task 4). 
o LatestAL2023AmiId: Leave the default value. 
o WPElasticFileSystemID: Paste the File system ID value (from Task 3). 
Task 5: Proceed to the AWS Management Console 
Task 6: Create the application servers by configuring an Auto Scaling group and a scaling policy 
In this task, you create the WordPress application servers by configuring an Auto Scaling group and a scaling policy. 
Task 6: Scenario 
Now that your template has been deployed, it’s time to create the application servers and auto scaling mechanism. In the previous task, 
you chose to implement auto scaling for the app servers to meet the scaling requirements of the project plan. 
Create an Auto Scaling group and scaling policy. Verify that the instance status is healthy, and test the load balancer availability. 
Hand the environment over to the engineering teams to validate full functionality when the unit test is complete, and ask them for feedback. 
When the team is satisfied, ask them to migrate the WordPress site to the AWS environment and test the app functionality. 
![image](https://github.com/user-attachments/assets/6a1b4582-6960-4d2c-8b82-d23ed369326f)
Task 6: Sub-tasks 
• Create an Auto Scaling group. 
• Verify the target groups and test the application server through the load balancer. 
• Log in to WordPress and explore. 
Task 6: Requirements and Configuration 
• Use the Launch template from the previous task. 
• Use the LabVPC for network settings. 
• Use the Application subnets in both Availability Zones. 
• Use the existing load balancer and target groups created earlier. 
• Use the Elastic Load Balancing (ELB) health check and set the health check grace period to 300 seconds. 
• Turn on Enable group metrics collection within CloudWatch. 
• Configure the group size: 
o Desired capacity: 2 
o Minimum capacity: 2 
o Maximum capacity: 4 
• Configure Scaling policies with Target tracking scaling policy and use the defaults. 
• Notifications are out of scope for the lab and do not need to be configured. 
• Verify that the Auto Scaling group has launched your EC2 instances. 
• Test the application using the Application Load Balancer DNS name
















