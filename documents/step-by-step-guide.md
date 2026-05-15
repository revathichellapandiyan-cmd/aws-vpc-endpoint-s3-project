# AWS VPC Endpoint Project — Step-by-Step Guide

## Project Title

**AWS VPC Endpoint — Secure Private EC2 Access to S3**

---

## Project Overview

This project demonstrates how to create a secure AWS architecture where a private EC2 instance accesses Amazon S3 without using the public internet, NAT Gateway, or public IP address.

The private EC2 instance connects to Amazon S3 through an **S3 Gateway VPC Endpoint**.

---

## Architecture Flow

```text
User Laptop
   ↓ SSH
Public EC2 Bastion Host
   ↓ SSH using Private IP
Private EC2 Instance
   ↓
S3 Gateway VPC Endpoint
   ↓
Amazon S3
```

---

## AWS Services Used

- Amazon VPC
- Public Subnet
- Private Subnet
- Internet Gateway
- Route Tables
- Security Groups
- Amazon EC2
- Amazon S3
- IAM Role
- S3 Gateway VPC Endpoint

---

## CIDR Configuration

| Resource | CIDR |
|---|---|
| VPC | 11.0.0.0/16 |
| Public Subnet | 11.0.1.0/24 |
| Private Subnet | 11.0.2.0/24 |

---

## Step 1: Create VPC

1. Open the AWS Management Console.
2. Go to **VPC**.
3. Click **Create VPC**.
4. Choose **VPC only**.
5. Enter the following details:

| Field | Value |
|---|---|
| Name tag | My-VPC |
| IPv4 CIDR block | 11.0.0.0/16 |

6. Click **Create VPC**.

---

## Step 2: Create Public Subnet

1. Go to **VPC → Subnets**.
2. Click **Create subnet**.
3. Select the VPC created earlier.
4. Enter the following details:

| Field | Value |
|---|---|
| Subnet name | Public-Subnet |
| Availability Zone | Select any AZ |
| IPv4 subnet CIDR block | 11.0.1.0/24 |

5. Click **Create subnet**.

### Enable Auto-Assign Public IP

1. Select **Public-Subnet**.
2. Click **Actions → Edit subnet settings**.
3. Enable **Auto-assign public IPv4 address**.
4. Click **Save**.

---

## Step 3: Create Private Subnet

1. Go to **VPC → Subnets**.
2. Click **Create subnet**.
3. Select the same VPC.
4. Enter the following details:

| Field | Value |
|---|---|
| Subnet name | Private-Subnet |
| Availability Zone | Same as public subnet |
| IPv4 subnet CIDR block | 11.0.2.0/24 |

5. Click **Create subnet**.

> Do not enable public IP for the private subnet.

---

## Step 4: Create Internet Gateway

1. Go to **VPC → Internet Gateways**.
2. Click **Create internet gateway**.
3. Enter the name:

```text
My-IGW
```

4. Click **Create internet gateway**.
5. Select the internet gateway.
6. Click **Actions → Attach to VPC**.
7. Select **My-VPC**.
8. Click **Attach internet gateway**.

---

## Step 5: Create Public Route Table

1. Go to **VPC → Route Tables**.
2. Click **Create route table**.
3. Enter the following details:

| Field | Value |
|---|---|
| Name | Public-RT |
| VPC | My-VPC |

4. Click **Create route table**.
5. Open **Public-RT**.
6. Go to **Routes → Edit routes**.
7. Add the following route:

| Destination | Target |
|---|---|
| 0.0.0.0/0 | Internet Gateway |

8. Click **Save changes**.

### Associate Public Subnet

1. Open **Public-RT**.
2. Go to **Subnet associations**.
3. Click **Edit subnet associations**.
4. Select **Public-Subnet**.
5. Click **Save associations**.

---

## Step 6: Create Private Route Table

1. Go to **VPC → Route Tables**.
2. Click **Create route table**.
3. Enter the following details:

| Field | Value |
|---|---|
| Name | Private-RT |
| VPC | My-VPC |

4. Click **Create route table**.

### Associate Private Subnet

1. Open **Private-RT**.
2. Go to **Subnet associations**.
3. Click **Edit subnet associations**.
4. Select **Private-Subnet**.
5. Click **Save associations**.

> Do not add an internet gateway route in the private route table.

---

## Step 7: Create Security Groups

### Public EC2 Security Group

1. Go to **EC2 → Security Groups**.
2. Click **Create security group**.
3. Enter the following details:

| Field | Value |
|---|---|
| Security group name | Public-EC2-SG |
| Description | Allows SSH access to public EC2 |
| VPC | My-VPC |

4. Add inbound rule:

| Type | Port | Source |
|---|---|---|
| SSH | 22 | My IP |

5. Click **Create security group**.

---

### Private EC2 Security Group

1. Go to **EC2 → Security Groups**.
2. Click **Create security group**.
3. Enter the following details:

| Field | Value |
|---|---|
| Security group name | Private-EC2-SG |
| Description | Allows SSH access from public EC2 |
| VPC | My-VPC |

4. Add inbound rule:

| Type | Port | Source |
|---|---|---|
| SSH | 22 | Public-EC2-SG |

5. Click **Create security group**.

> The private EC2 instance should allow SSH only from the public EC2 security group.

---

## Step 8: Launch Public EC2 Instance

1. Go to **EC2 → Instances**.
2. Click **Launch instance**.
3. Enter the following details:

| Field | Value |
|---|---|
| Name | Public-EC2-Bastion |
| AMI | Amazon Linux 2023 |
| Instance type | t2.micro or t3.micro |
| Key pair | Select your key pair |
| VPC | My-VPC |
| Subnet | Public-Subnet |
| Auto-assign public IP | Enable |
| Security group | Public-EC2-SG |

4. Click **Launch instance**.

---

## Step 9: Launch Private EC2 Instance

1. Go to **EC2 → Instances**.
2. Click **Launch instance**.
3. Enter the following details:

| Field | Value |
|---|---|
| Name | Private-EC2 |
| AMI | Amazon Linux 2023 |
| Instance type | t2.micro or t3.micro |
| Key pair | Same key pair |
| VPC | My-VPC |
| Subnet | Private-Subnet |
| Auto-assign public IP | Disable |
| Security group | Private-EC2-SG |

4. Click **Launch instance**.

---

## Step 10: Create S3 Bucket

1. Go to **S3**.
2. Click **Create bucket**.
3. Enter a unique bucket name.

Example:

```text
revathi-vpc-endpoint-demo-bucket
```

4. Keep default settings.
5. Click **Create bucket**.

---

## Step 11: Create IAM Role for S3 Access

1. Go to **IAM → Roles**.
2. Click **Create role**.
3. Choose **AWS service**.
4. Select **EC2** as the use case.
5. Click **Next**.
6. Search and select:

```text
AmazonS3FullAccess
```

7. Click **Next**.
8. Enter role name:

```text
EC2-S3-Access-Role
```

9. Click **Create role**.

### Attach IAM Role to Private EC2

1. Go to **EC2 → Instances**.
2. Select **Private-EC2**.
3. Click **Actions → Security → Modify IAM role**.
4. Select **EC2-S3-Access-Role**.
5. Click **Update IAM role**.

---

## Step 12: Create S3 Gateway VPC Endpoint

1. Go to **VPC → Endpoints**.
2. Click **Create endpoint**.
3. Enter the following details:

| Field | Value |
|---|---|
| Name | S3-VPC-Endpoint |
| Service category | AWS services |
| Service | com.amazonaws.region.s3 |
| Type | Gateway |
| VPC | My-VPC |
| Route table | Private-RT |
| Policy | Full access |

4. Click **Create endpoint**.

---

## Step 13: Connect Public EC2 Using MobaXterm

1. Open **MobaXterm**.
2. Click **Session**.
3. Select **SSH**.
4. Enter the public EC2 public IP in **Remote host**.
5. Tick **Specify username**.
6. Enter username:

```text
ec2-user
```

7. Go to **Advanced SSH settings**.
8. Tick **Use private key**.
9. Select your `.pem` key file.
10. Click **OK**.

Now you are connected to the public EC2 instance.

---

## Step 14: Upload PEM Key to Public EC2

1. In MobaXterm, use the left-side file panel.
2. Upload your `.pem` key file to:

```text
/home/ec2-user/
```

3. Run this command:

```bash
chmod 400 key.pem
```

Example:

```bash
chmod 400 priya.pem
```

---

## Step 15: Connect Private EC2 from Public EC2

From the public EC2 terminal, run:

```bash
ssh -i key.pem ec2-user@PRIVATE-EC2-PRIVATE-IP
```

Example:

```bash
ssh -i priya.pem ec2-user@11.0.2.106
```

Type:

```text
yes
```

Now you are connected to the private EC2 instance.

---

## Step 16: Test S3 Access from Private EC2

Run:

```bash
aws s3 ls
```

Create a test file:

```bash
echo "Hello from Private EC2" > test.txt
```

Upload the file to S3:

```bash
aws s3 cp test.txt s3://your-bucket-name/
```

Example:

```bash
aws s3 cp test.txt s3://revathi-vpc-endpoint-demo-bucket/
```

Check the uploaded file:

```bash
aws s3 ls s3://your-bucket-name/
```

---

## Step 17: Project Validation

The project is successful if:

- Public EC2 is accessible from your laptop.
- Private EC2 is accessible only through Public EC2.
- Private EC2 has no public IP.
- Private EC2 can access Amazon S3.
- S3 access works through the Gateway VPC Endpoint.

---

## Step 18: Correct Deletion Order

Delete the resources in this order:

1. Terminate EC2 instances.
2. Delete VPC Endpoint.
3. Delete NAT Gateway, if created.
4. Release Elastic IP, if created.
5. Delete route tables.
6. Detach and delete Internet Gateway.
7. Delete security groups.
8. Delete subnets.
9. Delete VPC.
10. Empty and delete S3 bucket.

---

## Project Outcome

Successfully created a secure AWS architecture where a private EC2 instance accesses Amazon S3 using an S3 Gateway VPC Endpoint without public internet access.

---

## Skills Demonstrated

- AWS VPC networking
- Public and private subnet configuration
- Bastion host setup
- EC2 SSH connection
- Security group configuration
- IAM role attachment
- S3 bucket access
- VPC Gateway Endpoint setup
- Secure cloud architecture design
