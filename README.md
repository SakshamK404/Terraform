# Terraform

This Terraform script orchestrates the setup of various AWS resources, encompassing a Virtual Private Cloud (VPC), subnets, security groups, EC2 instances, and an S3 bucket. Let's delve into each component:

1. **Provider Configuration**:
```hcl
provider "aws" {
	region = "us-east-1"
}
```
This segment instructs Terraform to interface with AWS services, specifically operating within the "us-east-1" region.

2. **Backend Configuration**:
```hcl
terraform {
  backend "s3" {
    bucket = "terraform-backend-spovedd"
    key    = "terraform.tfstate"
    region = "us-east-1"
  }
}
```
It configures Terraform to store its state data in an S3 bucket labeled "terraform-backend-spovedd" situated in the "us-east-1" region. Such setup facilitates effective state management and concurrency control in collaborative environments.

3. **VPC Provisioning**:
```hcl
resource "aws_vpc" "default" {
  cidr_block = "10.0.0.0/16"
  enable_dns_hostnames = true
}
```
This segment orchestrates the creation of a Virtual Private Cloud (VPC) spanning the IP range "10.0.0.0/16", with DNS hostnames enabled within the VPC for enhanced networking capabilities.

4. **Subnet Definition**:
```hcl
resource "aws_subnet" "public-subnet" {
  vpc_id            = "${aws_vpc.default.id}"
  cidr_block        = "10.0.1.0/24"
  availability_zone = "us-east-1a"
}
```
It establishes a public subnet delineated by the CIDR block "10.0.1.0/24" within the specified availability zone, encapsulated within the previously configured VPC.

5. **Security Group Configuration**:
```hcl
resource "aws_security_group" "sgweb" {
  name        = "vpc_test_web"
  description = "Allow incoming HTTP connections & SSH access"

  ingress {
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  // Additional ingress and egress rules...
}
```
This segment defines a security group named "vpc_test_web," permitting inbound traffic on ports 80 (HTTP), 443 (HTTPS), 22 (SSH), and ICMP (Ping) from any source.

6. **EC2 Instance Deployment**:
```hcl
resource "aws_instance" "web" {
	ami                    = "ami-032930428bf1abbff"
	instance_type          = "t2.micro"
	subnet_id              = "${aws_subnet.public-subnet.id}"
	vpc_security_group_ids = ["${aws_security_group.sgweb.id}"]
}
```
It provisions an EC2 instance leveraging the specified Amazon Machine Image (AMI) and instance type, deploying it within the designated public subnet. The instance is associated with the predefined security group for robust access control.

7. **S3 Bucket Setup**:
```hcl
resource "aws_s3_bucket" "b" {
  bucket = "my-tf-test-bucket-fhgfgg"
  acl    = "private"

  tags = {
    Name        = "My bucket"
    Environment = "Dev"
  }
}
```
This section creates an S3 bucket titled "my-tf-test-bucket-fhgfgg" with restricted access and incorporates descriptive tags for easy identification.

In essence, this Terraform script automates the configuration of foundational AWS infrastructure components, facilitating networking, security, compute, and storage functionalities.
