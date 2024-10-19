# Configure the AWS Provider

provider "aws" {
region = "us-west-2"
}

# Create a VPC

resource "aws_vpc" "example_vpc" {
cidr_block = "10.0.0.0/16"

tags = {
Name = "example-vpc"
}
}

# Create a subnet

resource "aws_subnet" "example_subnet" {
vpc_id = aws_vpc.example_vpc.id
cidr_block = "10.0.1.0/24"

tags = {
Name = "example-subnet"
}
}

# Create a security group

resource "aws_security_group" "example_sg" {
name = "example-sg"
description = "Allow inbound SSH traffic"
vpc_id = aws_vpc.example_vpc.id

ingress {
description = "SSH from anywhere"
from_port = 22
to_port = 22
protocol = "tcp"
cidr_blocks = ["0.0.0.0/0"]
}

egress {
from_port = 0
to_port = 0
protocol = "-1"
cidr_blocks = ["0.0.0.0/0"]
}

tags = {
Name = "allow_ssh"
}
}

# Create an EC2 instance

resource "aws_instance" "example_instance" {
ami = "ami-0c55b159cbfafe1f0" # Amazon Linux 2 AMI (HVM), SSD Volume Type
instance_type = "t2.micro"
subnet_id = aws_subnet.example_subnet.id

vpc_security_group_ids = [aws_security_group.example_sg.id]

tags = {
Name = "example-instance"
}
}

# Output the public IP of the instance

output "instance_public_ip" {
description = "Public IP address of the EC2 instance"
value = aws_instance.example_instance.public_ip
}
