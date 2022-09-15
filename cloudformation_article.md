## AWS CloudFormation Series: Create an EC2 Instance
While learning about [CloudFormation](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/Welcome.html), I noticed there are not a lot of beginner-friendly articles available. Hence, the reason for this series of mine.

In this series, I aim to cover foundational concepts around AWS EC2 instances and services to help people new to CloudFormation.

### What is CloudFormation
I refer to AWS CloudFormation as [Terraform](https://registry.terraform.io/) for AWS services. [Terraform](https://www.terraform.io/intro) is an Infrastructure as Code (IaC) tool that helps you provision resources across various cloud and on-prem services while CloudFormation is an IaC tool that models, provisions, and manages AWS and thrid-party services.

According to the official documentation,
"CloudFormation is a service that helps you model and set up your AWS resources so that you can spend less time managing those resources and more time focusing on your applications that run in AWS."

### What are EC2 instances and what goes into creating them?
[EC2](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/concepts.html
) instances are AWS's virtual machines. According to AWS, an EC2 instance provides scalable computing capacity in the Amazon Web Services (AWS) Cloud.

To provision an EC2 instance, you need to set up the following:
-   [Virtual Private Cloud](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-vpc.html
): you create a VPC to isolate your EC2 instance within the AWS cloud. 
-   [Security Group](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-security-groups.html
): acts as a virtual firewall for your EC2 instances controlling incoming and outgoing traffic. 
-   [Key Pair](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html
): this consists of a public key and a private key. You use this key when connecting to an EC2 instance.

### Cloudformation's Syntax 
In this section, we'd see hands-on examples of how to provision resources using CloudFormation.

Prerequisites
- Feel free to setup an [IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/getting-started_create-delegated-user.html) user. Grant this user full access to the service ```AWS CloudFormation```.
- Download and configure your [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-quickstart.html) with your login credentials.
```
aws configure
```

#### Creating a VPC
*main.yml*
```bash
Description: 
  Creating a VPC.

Parameters:

  EnvironmentName:
    Description: Prefixed to resources
    Type: String

  VpcCIDR:
    Description: IP range (CIDR notation)
    Type: String
    Default: 10.0.0.0/16

Resources:
  VPC:
    Type: AWS::EC2::VPC
    Properties: 
      CidrBlock: !Ref VpcCIDR
      EnableDnsHostnames: 'true'   
      Tags:
        - Key: Name 
          Value:  !Ref EnvironmentName
```

*parameters.json*
```bash
[
    {
        "ParameterKey": "EnvironmentName",
        "ParameterValue": "cfArticle"
    },
    {
        "ParameterKey": "VpcCIDR",
        "ParameterValue": "10.0.0.0/16"
    }    

]
```

The ```parameters.json``` file, holds the values of variables we defined in the ```main.yml``` file.

To build your stack, run:
```bash
$ aws cloudformation create-stack --stack-name stackname --template-body file://file.yml --parameters file://file.json
{
    "StackId": "arn:aws:cloudformation:us-west-1:5000:stack/stack/xxxx-xxxx-xxxx-xxxxx-xxxxxxx"
}
```
<img width="851" alt="image" src="https://user-images.githubusercontent.com/49791498/189965240-8194ff64-c5a8-4ab0-a743-93a07d5834c2.png">

*CloudFormation Stack*

### CloudFormation Commands
- To create a stack:
```bash
aws cloudformation create-stack \
--stack-name <value> \
--template-body file://file.yml \
--parameters file://file.json \
```

- To update an existing stack
```bash
aws cloudformation update-stack \
--stack-name <value> \
--template-body file://file.yml \
--parameters file://file.json \
```

- To delete a stack
```bash
delete-stack \
--stack-name <value>
```

You can find more CloudFormation commands [here](https://docs.aws.amazon.com/cli/latest/reference/cloudformation/index.html).

**Note**: 
- Deleting a stack, deletes all resources which the stack provisioned.
- After updating or creating your stack, you can validate your script using AWS CloudFormation's designer by:
  - Clicking on 'View in Designer'.
  <img width="696" alt="Screenshot 2022-09-14 at 14 01 25" src="https://user-images.githubusercontent.com/49791498/190161194-fcff3c07-5db5-4fdd-b9a4-28e1fe84d67d.png">

    *View in Designer*
   - Editing and validating your code. Viewing your code in JSON format, clearly shows the error source.

      <img width="726" alt="Screenshot 2022-09-14 at 15 31 31" src="https://user-images.githubusercontent.com/49791498/190184539-7f7e8cb7-5f08-4e81-a7b3-91b22eea57ff.png">

      *Validate button*

#### Conclusion
CloudFormation is your go-to tool when you only want to provision AWS resources. Your CloudFormation scripts can be in either JSON or YAML file formats, however there is caveat where your files cannot exceed 51MB.
If your files exceed 51MB, you would have to created nested stacks.

Thanks for your time, kindly look forward to other articles in this series.

#### Provision an EC2 instance
In the last article, we looked at how to create a VPC with CloudFormation. In this article, we will set up an EC2 instance.

To provision an EC2 instance via the CLI requires setting up the following:
- [VPC](https://docs.aws.amazon.com/vpc/latest/userguide/what-is-amazon-vpc.html): a virtual network where you provision your resources.

- [Internet Gateway](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-ec2-internetgateway.html
): Allocates an internet gateway for use with a VPC. After creating the Internet gateway, you then attach it to a VPC.

- [VPC GateWay Attachment](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-ec2-vpc-gateway-attachment.html
): Attaches an internet gateway, or a virtual private gateway to a VPC, enabling connectivity between the internet and the VPC.

- [Subnet](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-ec2-subnet.html
) (public and private): a logical partition of an IP network into multiple, smaller network segments.

- [Route Table](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-ec2-routetable.html
) (public and private):  a rule called routes that determines where network traffic from your subnet or gateway is directed.

- [Route](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-ec2-route.html
) (public and private): within a VPC, route consists of a single destination prefix in CIDR format and a single next hop.

- [Subnet route table association](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-ec2-subnetroutetableassociation.html
) (public and private): Associates a subnet with a route table.

- [Key Pair](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html): consists of public and private keys which you use to connect to your EC2 instance.

- [Security Group](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html): a firewall which controls inbound and outbound traffic on your EC2 instance.

#### Creating the script
In the ```main.yml``` file from the previous article, add the following:
```bash
  InternetGateway:
      Type: 'AWS::EC2::InternetGateway'
      Properties:
        Tags:
          - Key: Name
            Value: !Ref EnvironmentName
    
    AttachGateway:
      Type: 'AWS::EC2::VPCGatewayAttachment'
      Properties:
        InternetGatewayId: !Ref InternetGateway
        VpcId: !Ref VPC
    
    KeyName:
      Type: 'AWS::EC2::KeyPair'
      Properties:
        KeyName: scaTask
        KeyType: rsa
        Tags:
          - Key: Name
            Value: !Ref EnvironmentName
    
    PublicSubnet:
      Type: 'AWS::EC2::Subnet'
      Properties:
        VpcId: !Ref VPC
        CidrBlock: 10.0.1.0/24
        AvailabilityZone: us-west-1b
        MapPublicIpOnLaunch: true
        Tags:
          - Key: Name
            Value: Public Subnet 
    
    PrivateSubnet:
      Type: 'AWS::EC2::Subnet'
      Properties:
        VpcId: !Ref VPC
        CidrBlock: 10.0.2.0/24
        MapPublicIpOnLaunch: false
        AvailabilityZone: us-west-1b
        Tags:
          - Key: Name
            Value: Private Subnet 

  PublicRouteTable:
    Type: 'AWS::EC2::RouteTable'
    Properties:
      VpcId: !Ref VPC
      Tags:
        - Key: Name
          Value: Public Route Table
  
  PublicRoute:
    Type: 'AWS::EC2::Route'
    Properties:
      RouteTableId: !Ref PublicRouteTable
      DestinationCidrBlock: 0.0.0.0/0
      GatewayId: !Ref InternetGateway
  
  PublicSubnetRouteTableAssociation:
    Type: 'AWS::EC2::SubnetRouteTableAssociation'
    Properties:
      SubnetId: !Ref PublicSubnet
      RouteTableId: !Ref PublicRouteTable  

  PrivateRouteTable:
    Type: 'AWS::EC2::RouteTable'
    Properties:
      VpcId: !Ref VPC
      Tags:
        - Key: Name
          Value: !Ref EnvironmentName
  
  PrivateSubnetRouteTableAssociation:
    Type: 'AWS::EC2::SubnetRouteTableAssociation'
    Properties:
      SubnetId: !Ref PrivateSubnet1
      RouteTableId: !Ref PrivateRouteTable    

  EC2Instance:
    Type: 'AWS::EC2::Instance'
    Properties:
      ImageId: ami-018d291ca9ffc002f
      InstanceType: t2.micro
      SecurityGroupIds:
        - !Ref SecurityGroup
      SubnetId: !Ref PublicSubnet
      KeyName: !Ref KeyName   

  SecurityGroup:
    Type: 'AWS::EC2::SecurityGroup'
    Properties:
      GroupDescription: Security group to allow traffic to and from ports 80 and 443.
      VpcId: !Ref VPC
      SecurityGroupIngress:
        - IpProtocol: tcp
          FromPort: 22
          ToPort: 22
          SourceSecurityGroupId:
            Fn::GetAtt:
            - ELBSecurityGroup
            - GroupId  
        - IpProtocol: tcp
          FromPort: 80
          ToPort: 80
      Tags:
        - Key: Name
          Value: !Ref EnvironmentName             
```

Then run:
```bash
aws cloudformation update-stack \
--stack-name <value> \
--template-body file://file.yml \
--parameters file://file.json \
```

Thanks for your time, kindly look forward to other articles in this series.

#### Nested stacks

