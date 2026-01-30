### CloudFormation Basics

CloudFormation is a tool that uses templates to create, update & delete infrastructure in AWS in a consistent & repeatable way. Templates can be defined in either YAML or JSON format.

Resources must be mentioned to specify what resources are used. Template format version is not mandatory. Description: it is free text. If template format version is mentioned, description must directly follow.

Below CloudFormation template has been used to create a stack and launch an EC2 instance with Session Manager. Once everything is created, delete the stack.

```
Parameters:
  LatestAmiId:
    Type: 'AWS::SSM::Parameter::Value<AWS::EC2::Image::Id>'
    Default: '/aws/service/ami-amazon-linux-latest/al2023-ami-kernel-default-x86_64'
  SSHandWebLocation:
    Description: The IP address range that can be used to SSH to the EC2 instances
    Type: String
    MinLength: '9'
    MaxLength: '18'
    Default: 0.0.0.0/0
    AllowedPattern: '(\d{1,3})\.(\d{1,3})\.(\d{1,3})\.(\d{1,3})/(\d{1,2})'
    ConstraintDescription: must be a valid IP CIDR range of the form x.x.x.x/x. Default is 0.0.0.0/0 and is less safe.
Resources:
  EC2Instance:
    Type: AWS::EC2::Instance
    Properties:
      InstanceType: "t3.micro"
      ImageId: !Ref LatestAmiId
      IamInstanceProfile: !Ref SessionManagerInstanceProfile
      SecurityGroups:
        - !Ref InstanceSecurityGroup
  InstanceSecurityGroup:
    Type: 'AWS::EC2::SecurityGroup'
    Properties:
      GroupDescription: Enable SSH access via port 22 and 80
      SecurityGroupIngress:
        - IpProtocol: tcp
          FromPort: '22'
          ToPort: '22'
          CidrIp: !Ref SSHandWebLocation
        - IpProtocol: tcp
          FromPort: '80'
          ToPort: '80'
          CidrIp: !Ref SSHandWebLocation
  SessionManagerRole:
    Type: 'AWS::IAM::Role'
    Properties:
      AssumeRolePolicyDocument:
        Version: 2012-10-17
        Statement:
          - Effect: Allow
            Principal:
              Service:
              - ec2.amazonaws.com
            Action:
              - 'sts:AssumeRole'
      Path: /
      ManagedPolicyArns:
        - "arn:aws:iam::aws:policy/AmazonSSMManagedInstanceCore"
  SessionManagerInstanceProfile:
    Type: 'AWS::IAM::InstanceProfile'
    Properties:
      Path: /
      Roles:
        - !Ref SessionManagerRole
Outputs:
  InstanceId:
    Description: InstanceId of the newly created EC2 instance
    Value: !Ref EC2Instance
  AZ:
    Description: Availability Zone of the newly created EC2 instance
    Value: !GetAtt 
      - EC2Instance
      - AvailabilityZone
  PublicDNS:
    Description: Public DNSName of the newly created EC2 instance
    Value: !GetAtt 
      - EC2Instance
      - PublicDnsName
  PublicIP:
    Description: Public IP address of the newly created EC2 instance
    Value: !GetAtt 
      - EC2Instance
      - PublicIp
```

---

## Quick Exam Checklist – Day 2 (SAA-C03)

| Topic | Key point |
|-------|-----------|
| **CloudFormation** | IaC; YAML or JSON templates; creates/updates/deletes stacks. |
| **Template sections** | Parameters, Resources (required), Outputs; Description, Metadata optional. |
| **Resources** | Must declare all resources; use intrinsic functions (!Ref, !GetAtt). |
| **Parameters** | SSM Parameter types (e.g. AWS::SSM::Parameter::Value\<AWS::EC2::Image::Id\>); constraints. |
| **Outputs** | Expose values (e.g. InstanceId, PublicIP) after stack creation. |
| **IAM in CFN** | Roles, Instance Profiles; trust policy for ec2.amazonaws.com. |

---

## Important Exam Points – Day 2

- **CloudFormation** = Infrastructure as Code; **Resources** section is mandatory.
- **!Ref** returns the value (e.g. parameter value, resource ID); **!GetAtt** returns attributes (e.g. AvailabilityZone, PublicIp).
- Use **SSM Parameter for latest AMI** to avoid hardcoding AMI IDs (e.g. /aws/service/ami-amazon-linux-latest/...).
- **Instance Profile** links an IAM role to EC2; required for instance to assume the role (e.g. SSM ManagedInstanceCore).
- Stack **rollback** on failure; understand **dependent resources** and **creation order**.
