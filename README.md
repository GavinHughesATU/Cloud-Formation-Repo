# Cloud-Formation-Repo
An assignment repo focusing on the concepts of three tier architectures and using S3 as a data store for static web pages.

1a:
A static website was created based on this Tutorial:
https://docs.aws.amazon.com/AmazonS3/latest/userguide/HostingWebsiteOnS3Setup.html

The implementation follows the S3 Static Website Hosting via CloudFormation tutorial but was simplified to use a basic HTML/CSS website rather than Angular. 
Additional resource tags, a custom bucket name, and an error page were added to improve resource management and usability.
Versioning was enabled on the S3 bucket to maintain previous versions of website files.


Using:
Amazon S3
AWS CloudFormation
HTML
CSS

LLM promts used through CoPilot:
"Write a basic HTML/CSS website displaying a heading and a paragraph" - Pages were then edited to add my details and a error page was also added.
"Suggest tags for an S3 bucket hosting a static website." - Bit of an overkill of tags suggested, so cut it back to ones I thought important.


1b: 3 tier architecture for a web tier, application tier and database tier.
LLM prompt through CoPilot: 
Create a basic AWS CloudFormation YAML template for a three-tier architecture.

The template should include:
- One VPC
- A Web Tier EC2 instance
- An Application Tier EC2 instance
- A MySQL RDS database
- Security Groups for each tier

The original template provided the basic architecture components. Several additional features were implemented to improve flexibility, security, and maintainability.

- Parameters
The original template hard-coded all values. Parameters make the template reusable and configurable without editing the YAML. 
AWS recommends parameters for creating flexible templates. (https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/parameters-section-structure.html)
InstanceType: t2.micro -> InstanceType: !Ref InstanceType
LatestAmi, means the user must manually supply during stack creation.

- Secure Database Credentials
Database credentials were parameterised to avoid hard-coding sensitive information within the template.
AWS security best practice recommends avoiding hard-coded credentials.
MasterUserPassword: Password123 -> MasterUserPassword: !Ref DatabasePassword
Additionally, NoEcho: true was enabled to prevent the password from being displayed in the CloudFormation console.

- Public Subnet
A Public Subnet was created within the VPC to host internet-facing resources. The subnet provides a range of IP addresses that can be assigned to EC2 instances and allows the Web Tier to be separated from other parts of the network. 
This improves network organisation and reflects how resources are typically deployed in AWS environments.
The subnet uses the CIDR range 10.0.1.0/24, which is a subset of the VPC address range 10.0.0.0/16. 
This follows the Amazon VPC networking model, where subnets are used to organise resources into smaller network segments.(https://docs.aws.amazon.com/vpc/latest/userguide/configure-subnets.html)

- Security Group Rules
SecurityGroupIngress was added to the original template to allow traffic, it defines the inbound traffic rules for an AWS security group, 
specifying which protocols, ports, and source IP addresses are allowed to connect to a resource.
SecurityGroupIngress:
  - IpProtocol: tcp
    FromPort: 80
    ToPort: 80
    CidrIp: 0.0.0.0/0
	
- Internet Gateway (IGW)
An Internet Gateway was added to allow resources within the VPC to communicate with the internet. 
The Internet Gateway was attached to the VPC using a VPC Gateway Attachment resource.
(https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Internet_Gateway.html)

- Route Table
A Route Table was added to control how traffic is routed within the VPC. A route was configured so that all internet-bound traffic (0.0.0.0/0) is directed to the Internet Gateway.
The Route Table was then associated with the Public Subnet, allowing resources within the subnet to reach external networks and receive internet traffic
(https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Route_Tables.html)
	
- Tier-to-Tier Communication
This makes the architecture behave like a true three-tier system.
So only the Web Tier is internet-facing. The Application Tier only accepts traffic from the Web Tier and the Database only accepts traffic coming from the Application Tier.
Application Tier: SourceSecurityGroupId: !Ref WebSecurityGroup

- DBSubnetGroup
A DB Subnet Group was added to define the subnets available to the RDS database. 
Database resources in AWS are deployed into subnets, and the DB Subnet Group provides a list of subnets that RDS can use when creating the database instance.
Two subnets were created and added to the subnet group. This reflects a common AWS design pattern where database resources are separated from the web and application tiers and deployed within dedicated network segments.
(https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_VPC.WorkingWithRDSInstanceinaVPC.html)

- Improved Tags
Although the template provided tags, these were improved upon to help identify and organise AWS resources.
Tags:
  - Key: Name
    Value: ApplicationServer
	
- Outputs
Outputs were added to provide useful information once the stack is deployed.
DatabaseEndpoint:
      Description: Database Endpoint
      Value: !GetAtt Database.Endpoint.Address
	  

References used throughout:
https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/parameters-section-structure.html
https://docs.aws.amazon.com/vpc/latest/userguide/what-is-amazon-vpc.html
https://docs.aws.amazon.com/vpc/latest/userguide/configure-subnets.html
https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Internet_Gateway.html
https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Route_Tables.html

XMAL Validator:
https://www.yamllint.com/
