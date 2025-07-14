# CloudFormation Template: EC2 Instance with Elastic IP and SSH Access

This AWS CloudFormation template deploys an Amazon EC2 instance with an attached Elastic IP address and a security group allowing SSH access from a configurable CIDR block.

## Features

- Launches a single EC2 instance with Amazon Linux 2 (via SSM parameter for the latest AMI).
- Attaches an Elastic IP to the instance.
- Configurable EC2 instance type.
- SSH access controlled by a user-specified CIDR range.
- Parameterized EC2 KeyPair for secure SSH login.

---

## Template Parameters

| Parameter     | Description                                                        | Default                                                              | Example                         |
|----------------|--------------------------------------------------------------------|----------------------------------------------------------------------|---------------------------------|
| `InstanceType` | EC2 instance type to launch                                        | `t3.small`                                                           | `t2.micro`, `m5.large`, etc.    |
| `KeyName`      | Name of an existing EC2 KeyPair for SSH access                     | *(required)*                                                          | `my-ec2-keypair`                |
| `SSHLocation`  | Allowed CIDR range for SSH (port 22) access                        | `0.0.0.0/0`                                                           | `203.0.113.0/24`                |
| `LatestAmiId`  | SSM parameter for the latest Amazon Linux 2 AMI                    | `/aws/service/ami-amazon-linux-latest/amzn2-ami-hvm-x86_64-gp2`      | *(do not typically override)*   |

---

## Resources Created

- **AWS::EC2::Instance** – The EC2 instance with the selected type and Amazon Linux 2 AMI.
- **AWS::EC2::SecurityGroup** – Allows SSH access (port 22) from the specified CIDR.
- **AWS::EC2::EIP** – Allocates an Elastic IP address.
- **AWS::EC2::EIPAssociation** – Associates the Elastic IP with the EC2 instance.

---

## Outputs

| Output              | Description                                 |
|----------------------|---------------------------------------------|
| `InstanceId`         | The ID of the created EC2 instance         |
| `InstanceIPAddress`  | The Elastic IP address assigned to the instance |

---

## Deployment Example (AWS CLI)

```bash
aws cloudformation deploy \
  --template-file ec2-instance.yaml \
  --stack-name my-ec2-stack \
  --parameter-overrides \
      KeyName=my-ec2-keypair \
      InstanceType=t2.micro \
      SSHLocation=203.0.113.0/24 \
  --capabilities CAPABILITY_NAMED_IAM
