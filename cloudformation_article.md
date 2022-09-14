## AWS CloudFormation Series: Create an EC2 Instance
While learning about [CloudFormation](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/Welcome.html), I noticed there are not a lot of beginner-friendly articles available. Hence, the reason for this series of mine.

In this series, I aim to cover foundational concepts around AWS EC2 instances and services to provide value to people new to CloudFormation.

### What is CloudFormation
I refer to AWS CloudFormation as [Terraform](https://registry.terraform.io/) for AWS services.

According to the official documentation,
"It is a service that helps you model and set up your AWS resources so that you can spend less time managing those resources and more time focusing on your applications that run in AWS."

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

Thanks for your time, look forward to other articles in this series.



