# AWS VPC Endpoint — Secure Private EC2 Access to S3

## Project Overview

This project demonstrates how to securely connect a private EC2 instance to Amazon S3 using an S3 Gateway VPC Endpoint.

The private EC2 instance accesses Amazon S3 without:
- Public Internet
- NAT Gateway
- Public IP

Instead, communication happens privately through a VPC Endpoint.

---

## Architecture Diagram

![Architecture](architecture/vpc-endpoint-diagram.png)

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
- VPC Gateway Endpoint

---

## Architecture Flow

```text
Laptop
   ↓ SSH
Public EC2 Bastion Host
   ↓ SSH
Private EC2
   ↓
S3 Gateway VPC Endpoint
   ↓
Amazon S3
```

---

## CIDR Configuration

| Resource | CIDR |
|---|---|
| VPC | 11.0.0.0/16 |
| Public Subnet | 11.0.1.0/24 |
| Private Subnet | 11.0.2.0/24 |

---

## Security Group Rules

### Public EC2 Security Group

| Type | Port | Source |
|---|---|---|
| SSH | 22 | My IP |

### Private EC2 Security Group

| Type | Port | Source |
|---|---|---|
| SSH | 22 | Public EC2 Security Group |

---

## Steps Performed

1. Created custom VPC
2. Created public and private subnets
3. Attached Internet Gateway
4. Configured route tables
5. Created security groups
6. Launched public EC2 instance
7. Launched private EC2 instance
8. Connected private EC2 using Bastion Host
9. Created Amazon S3 bucket
10. Configured S3 Gateway VPC Endpoint
11. Attached IAM role to EC2
12. Tested S3 access from private EC2

---

## Commands Used

### Connect Public EC2

```bash
ssh -i key.pem ec2-user@PUBLIC-IP
```

### Connect Private EC2

```bash
ssh -i key.pem ec2-user@PRIVATE-IP
```

### Check S3 Access

```bash
aws s3 ls
```

### Upload File to S3

```bash
aws s3 cp test.txt s3://bucket-name/
```

---

## Project Outcome

Successfully enabled secure private access from EC2 to Amazon S3 without internet access using VPC Gateway Endpoint.

---

## Skills Demonstrated

- AWS Networking
- VPC Configuration
- Public and Private Subnet Design
- Bastion Host Configuration
- EC2 Connectivity
- IAM Role Management
- Security Group Configuration
- S3 Access Management
- VPC Endpoint Configuration
- Cloud Security Best Practices

---

## Project Status

Completed Successfully
