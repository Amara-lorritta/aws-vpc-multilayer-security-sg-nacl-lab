## **AWS VPC Multi-Layered Security Lab**

VPC • EC2 • Security Groups • Network ACLs 

This lab demonstrates how I implemented multi-layered security in an AWS VPC by combining Security Groups (instance-level security) and Network ACLs (subnet-level security). I launched two EC2 instances—one in a public subnet and one in a private subnet—and validated traffic flow using layered access controls.

## **Overview**

In this lab, I created a custom VPC, attached an Internet Gateway, designed public and private subnets, configured route tables, and applied both Security Groups and Network ACLs. This setup reflects AWS best practices for defense-in-depth, where traffic must be explicitly allowed at multiple layers to reach resources.

## **Tools Used**

Amazon VPC — custom networking and isolation

Amazon EC2 — compute instances for testing access

Security Groups — stateful, instance-level firewall

Network ACLs (NACLs) — stateless, subnet-level firewall

AWS Management Console — resource creation and testing

## **Architecture Diagram**

<img width="589" height="400" alt="security-comparison" src="https://github.com/user-attachments/assets/a472de81-14d2-41c6-b022-205200974f1a" />


## **Architecture Summary**

Custom VPC: whizlabs_VPC (10.0.0.0/16)

Internet Gateway attached

Public Subnet: 10.0.1.0/24 (public EC2)

Private Subnet: 10.0.2.0/24 (private EC2)

Security Group applied to both EC2 instances

Network ACL applied to both subnets

## **Implementation Steps**
```bash
############################################################
# 1. SIGN IN TO AWS MANAGEMENT CONSOLE
############################################################
# - Click Open Console from lab portal
# - Use provided IAM Username + Password

############################################################
# 2. CREATE A NEW VPC
############################################################
# VPC → Your VPCs → Create VPC
#   - Name: MY_VPC
#   - IPv4 CIDR: 10.0.0.0/16
#   - IPv6: None
#   - Tenancy: Default

############################################################
# 3. CREATE & ATTACH INTERNET GATEWAY
############################################################
# VPC → Internet Gateways → Create
#   - Name: My_IGW
# Attach IGW to My_VPC

############################################################
# 4. CREATE PUBLIC & PRIVATE SUBNETS
############################################################
# Public Subnet:
#   - Name: public_subnet
#   - CIDR: 10.0.1.0/24

# Private Subnet:
#   - Name: private_subnet
#   - CIDR: 10.0.2.0/24

############################################################
# 5. CREATE ROUTE TABLES & ASSOCIATIONS
############################################################
# Public Route Table:
#   - Name: public_route
#   - Route: 0.0.0.0/0 → My_IGW
#   - Associate with public_subnet

# Private Route Table:
#   - Name: private_route
#   - No internet route
#   - Associate with private_subnet

############################################################
# 6. CREATE SECURITY GROUP
############################################################
# EC2 → Security Groups → Create
#   - Name: My_securitygroup
#   - VPC: My_VPC
# Inbound Rules:
#   - SSH (22) → Anywhere IPv4
#   - All ICMP - IPv4 → Anywhere IPv4

############################################################
# 7. CREATE & CONFIGURE NETWORK ACL
############################################################
# VPC → Network ACLs → Create
#   - Name: My_NACL
#   - VPC: My_VPC

# Inbound Rules:
#   Rule 100 → SSH (22) → 0.0.0.0/0 → ALLOW
#   Rule 200 → ALL ICMP - IPv4 → 0.0.0.0/0 → ALLOW

# Outbound Rules:
#   Rule 100 → ALL ICMP - IPv4 → 0.0.0.0/0 → ALLOW
#   Rule 200 → TCP 1024-65535 → 0.0.0.0/0 → ALLOW

# Associate NACL with:
#   - public_subnet
#   - private_subnet

############################################################
# 8. LAUNCH EC2 INSTANCES
############################################################
# Public EC2:
#   - Name: public_instance
#   - Subnet: public_subnet
#   - Public IP: Enabled
#   - Security Group: My_securitygroup
#   - Key Pair: MyKey.pem

# Private EC2:
#   - Name: private_instance
#   - Subnet: private_subnet
#   - Public IP: Disabled
#   - Security Group: My_securitygroup
#   - Key Pair: MyKey.pem

############################################################
# 9. TEST CONNECTIVITY
############################################################
# SSH into public_instance
# Ping private_instance private IP:
#   ping <private_instance_ipv4>
# ICMP response confirms layered security works
############################################################
```

## **Important Screenshots**

VPC & Subnets Overview

<img width="1907" height="210" alt="image" src="https://github.com/user-attachments/assets/50762618-6bfc-4812-922b-275253450fbb" />

Network ACL Rules

<img width="1602" height="418" alt="image" src="https://github.com/user-attachments/assets/4bc94e4b-0b4b-4c7a-8ee2-2bad085d3aa0" />
<img width="1623" height="426" alt="image" src="https://github.com/user-attachments/assets/92daecc5-881e-4809-a936-bdce4d1f15a9" />

Security Group Inbound Rules

<img width="1651" height="410" alt="image" src="https://github.com/user-attachments/assets/4584b54f-c626-45dd-b632-12c11b243e02" />

EC2 Connectivity Test

<img width="1174" height="535" alt="image" src="https://github.com/user-attachments/assets/79841a06-c91a-4414-93db-7c10f7b45ca6" />

## **What Actually Happened**

I created a custom VPC and segmented it into public and private subnets to separate internet-facing and internal resources. After attaching an Internet Gateway and configuring route tables, only the public subnet had direct internet access.

I then applied Security Groups to control traffic at the EC2 instance level and Network ACLs to control traffic at the subnet level. Because Network ACLs are stateless, I had to explicitly allow inbound and outbound rules for traffic to succeed. When both layers allowed ICMP traffic, I was able to successfully ping the private EC2 instance from the public EC2 instance, confirming that multi-layered security was working as expected.

## **Key Takeaways**

Security Groups are stateful and apply at the instance level

Network ACLs are stateless and apply at the subnet level

Traffic must be allowed by both SGs and NACLs to succeed

Public and private subnet separation reduces attack surface

Multi-layered security is a core AWS networking best practice

## **Author**

Amarachi Emeziem

Cloud Security Engineer
