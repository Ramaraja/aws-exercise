### **CloudFormation Script to Create an EC2 Instance with EBS and Security Group**

This script will:
1. Create a security group allowing SSH access.
2. Launch an EC2 instance using the specified AMI and instance type.
3. Attach an additional EBS volume to the instance.

---

### **CloudFormation YAML Template**

```yaml
AWSTemplateFormatVersion: "2010-09-09"
Description: CloudFormation Template to create an EC2 instance, an EBS volume, and a Security Group.

Resources:
  # Security Group
  InstanceSecurityGroup:
    Type: "AWS::EC2::SecurityGroup"
    Properties:
      GroupDescription: "Allow SSH access"
      VpcId: !Ref DefaultVPC
      SecurityGroupIngress:
        - IpProtocol: "tcp"
          FromPort: 22
          ToPort: 22
          CidrIp: "0.0.0.0/0"  # Update with a restricted IP for security

  # EC2 Instance
  EC2Instance:
    Type: "AWS::EC2::Instance"
    Properties:
      InstanceType: "t2.micro"  # Update as needed
      KeyName: "your-key-name"  # Replace with your key pair name
      ImageId: !Ref "AWS::Region" # Replace with AMI ID or leave dynamic
      SecurityGroupIds:
        - !Ref InstanceSecurityGroup
      BlockDeviceMappings:
        - DeviceName: "/dev/xvda"  # Root volume
          Ebs:
            VolumeSize: 8  # Root volume size in GiB
            VolumeType: "gp2"
        - DeviceName: "/dev/xvdf"  # Additional EBS volume
          Ebs:
            VolumeSize: 10  # Size in GiB
            VolumeType: "gp2"
      Tags:
        - Key: "Name"
          Value: "DemoEC2Instance"

  # EBS Volume (Optional - Explicit Volume Creation Example)
  AdditionalEBSVolume:
    Type: "AWS::EC2::Volume"
    Properties:
      Size: 10  # Size in GiB
      VolumeType: "gp2"
      AvailabilityZone: !GetAtt EC2Instance.AvailabilityZone
      Tags:
        - Key: "Name"
          Value: "AdditionalEBSVolume"

  # Attach the Additional EBS Volume to the EC2 Instance
  VolumeAttachment:
    Type: "AWS::EC2::VolumeAttachment"
    Properties:
      VolumeId: !Ref AdditionalEBSVolume
      InstanceId: !Ref EC2Instance
      Device: "/dev/xvdg"  # Device name in EC2

Parameters:
  DefaultVPC:
    Description: "Default VPC ID"
    Type: "AWS::EC2::VPC::Id"
    Default: "vpc-xxxxxxxx"  # Replace with your default VPC ID

Outputs:
  InstancePublicIP:
    Description: "Public IP address of the EC2 instance"
    Value: !GetAtt EC2Instance.PublicIp
    Export:
      Name: "EC2InstancePublicIP"

  EBSVolumeId:
    Description: "ID of the additional EBS volume"
    Value: !Ref AdditionalEBSVolume
    Export:
      Name: "EBSVolumeId"
```


### **Steps to Use the Template**

1. **Save the YAML File**:
   - Save the script as `ec2-ebs-security-group.yaml`.

2. **Deploy via AWS Management Console**:
   - Go to the **CloudFormation Console**.
   - Click **Create Stack** > **Upload a template file** and select the YAML file.
   - Provide the necessary parameters (e.g., VPC ID, Key Pair Name).

3. **Deploy via AWS CLI** (Optional):
   - Use the following command:
     ```bash
     aws cloudformation create-stack \
       --stack-name EC2WithEBSAndSG \
       --template-body file://ec2-ebs-security-group.yaml \
       --parameters ParameterKey=DefaultVPC,ParameterValue=vpc-xxxxxxxx
     ```

4. **Verify Resources**:
   - Navigate to **EC2 Console** > **Instances** to find the instance.
   - Check the attached EBS volume under the instance details.

5. **Test Access**:
   - Use the instance's public IP to SSH:
     ```bash
     ssh -i your-key.pem ec2-user@<public-ip>
     ```
   - Verify that the additional EBS volume is attached:
     ```bash
     lsblk
     ```
---

### **CloudFormation Template: EC2 Instances with ELB and Auto Scaling Group**

This CloudFormation template will:
1. Launch two EC2 instances.
2. Create an Elastic Load Balancer (ELB) to distribute traffic.
3. Set up an Auto Scaling Group (ASG) with the desired capacity of 2 instances.


### **CloudFormation YAML Template**

```yaml
AWSTemplateFormatVersion: "2010-09-09"
Description: Create 2 EC2 instances with an ELB and Auto Scaling Group.

Parameters:
  KeyName:
    Description: "Name of an existing EC2 KeyPair to enable SSH access"
    Type: "AWS::EC2::KeyPair::KeyName"

  VPCId:
    Description: "The VPC ID where resources will be deployed"
    Type: "AWS::EC2::VPC::Id"

  SubnetIds:
    Description: "Subnets for ELB and ASG (comma-separated)"
    Type: "List<AWS::EC2::Subnet::Id>"

  InstanceType:
    Description: "EC2 instance type"
    Type: "String"
    Default: "t2.micro"
    AllowedValues:
      - t2.micro
      - t2.small
      - t2.medium
      - t3.micro
      - t3.small
    ConstraintDescription: "Must be a valid EC2 instance type."

Resources:
  # Security Group for EC2 Instances
  EC2SecurityGroup:
    Type: "AWS::EC2::SecurityGroup"
    Properties:
      GroupDescription: "Allow HTTP and SSH"
      VpcId: !Ref VPCId
      SecurityGroupIngress:
        - IpProtocol: "tcp"
          FromPort: 22
          ToPort: 22
          CidrIp: "0.0.0.0/0"
        - IpProtocol: "tcp"
          FromPort: 80
          ToPort: 80
          CidrIp: "0.0.0.0/0"

  # Launch Configuration
  LaunchConfiguration:
    Type: "AWS::AutoScaling::LaunchConfiguration"
    Properties:
      InstanceType: !Ref InstanceType
      KeyName: !Ref KeyName
      SecurityGroups:
        - !Ref EC2SecurityGroup
      ImageId: !Ref "AWS::Region"  # Replace with a specific AMI ID or dynamic selection
      UserData:
        Fn::Base64: !Sub |
          #!/bin/bash
          yum update -y
          yum install -y httpd
          systemctl start httpd
          systemctl enable httpd
          echo "<h1>Welcome to EC2 Instance $(hostname)</h1>" > /var/www/html/index.html

  # Auto Scaling Group
  AutoScalingGroup:
    Type: "AWS::AutoScaling::AutoScalingGroup"
    Properties:
      VPCZoneIdentifier: !Ref SubnetIds
      LaunchConfigurationName: !Ref LaunchConfiguration
      MinSize: "2"
      MaxSize: "4"
      DesiredCapacity: "2"
      HealthCheckType: "EC2"
      HealthCheckGracePeriod: 300
      TargetGroupARNs:
        - !Ref ApplicationTargetGroup

  # Load Balancer
  ApplicationLoadBalancer:
    Type: "AWS::ElasticLoadBalancingV2::LoadBalancer"
    Properties:
      Name: "DemoELB"
      Scheme: "internet-facing"
      Subnets: !Ref SubnetIds
      SecurityGroups:
        - !Ref EC2SecurityGroup
      Type: "application"
      IpAddressType: "ipv4"

  # Target Group for ELB
  ApplicationTargetGroup:
    Type: "AWS::ElasticLoadBalancingV2::TargetGroup"
    Properties:
      Name: "DemoTargetGroup"
      Port: 80
      Protocol: "HTTP"
      VpcId: !Ref VPCId
      HealthCheckPath: "/"
      TargetType: "instance"

  # Listener for ELB
  ApplicationListener:
    Type: "AWS::ElasticLoadBalancingV2::Listener"
    Properties:
      LoadBalancerArn: !Ref ApplicationLoadBalancer
      Port: 80
      Protocol: "HTTP"
      DefaultActions:
        - Type: "forward"
          TargetGroupArn: !Ref ApplicationTargetGroup

Outputs:
  LoadBalancerDNSName:
    Description: "DNS name of the Load Balancer"
    Value: !GetAtt ApplicationLoadBalancer.DNSName

  AutoScalingGroupName:
    Description: "Name of the Auto Scaling Group"
    Value: !Ref AutoScalingGroup
```

### **Steps to Deploy**

1. **Save the Template**:
   - Save the above YAML file as `ec2-elb-asg-cloudformation.yaml`.

2. **Deploy via AWS Management Console**:
   - Go to the **CloudFormation Console**.
   - Select **Create Stack** > **Upload a template file** and upload the YAML file.
   - Provide the parameters:
     - **KeyName**: Your EC2 KeyPair name.
     - **VPCId**: The VPC ID where the resources should be deployed.
     - **SubnetIds**: A comma-separated list of public subnet IDs.
     - **InstanceType**: (Optional) The instance type for the EC2 instances.

3. **Deploy via AWS CLI** (Optional):
   ```bash
   aws cloudformation create-stack \
     --stack-name EC2ELBASGStack \
     --template-body file://ec2-elb-asg-cloudformation.yaml \
     --parameters ParameterKey=KeyName,ParameterValue=my-key \
                  ParameterKey=VPCId,ParameterValue=vpc-xxxxxxxx \
                  ParameterKey=SubnetIds,ParameterValue='subnet-xxxxxxxx,subnet-yyyyyyyy' \
                  ParameterKey=InstanceType,ParameterValue=t2.micro
   ```

4. **Verify Resources**:
   - Navigate to the **EC2 Console** to confirm two instances are running.
   - Go to the **Load Balancer Console** to view the ELB DNS name.
   - Test the setup by accessing the ELB DNS name in a browser.

5. **Scaling Test**:
   - Modify the desired capacity of the Auto Scaling Group to scale the instances up or down.

---
