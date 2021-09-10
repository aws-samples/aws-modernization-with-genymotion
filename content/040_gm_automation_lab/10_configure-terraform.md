---
title: "Configure devices to start with Terraform"
chapter: true
weight: 10
---

# Configure devices to start with Terraform

## Introduction to Terraform

Terraform is an infrastructure as code (IaC) tool that will help you ease the deployment of Genymotion Android virtual devices. You are still free to use aws-ec2 command line tools if you want but we won't cover this here.

Terraform is already installed by default on the Cloud9 Workspace we created for this workshop.
When you will create your own testing environment, you will of course need to [download](https://www.terraform.io/downloads.html) and [install Terraform](https://www.terraform.io/intro/getting-started/install.html)

You also don't need to provide your AWS credentials since you are already authenticated in the Workspace.

You need to create a `genymotion.tf` for Terraform to spawn and access instances within your project. To do so, create a `genymotion.tf` file in your environment folder (`~/environment`), copy and paste the content of our template below:

<script src="https://gist.github.com/lilinor/15f944da5e43aa8c863b44701c366071.js"></script>

Then, modify it as explained below:

## 1. Set the aws region

You need to set on which region to spawn the devices. Please choose the same aws region you chose to deploy your workspace. In this lab, we use `us-east-1`

```
provider "aws" {
  region = "us-east-1"
}
```

## 2. Set the Security Group

You need to set up the security group of the device you start to safely run the tests. Only the ingress ports for Cloud9 workspace are opened, so that the adb port is accessible securely and not subject to attacks.

1. Get the public IP address of your workspace from the ec2 admin panel or with this command : `dig +short myip.opendns.com @resolver1.opendns.com`.

2. Change the IP in **ingress/cidr_blocks** parameter with your public IP:

```
resource "aws_security_group" "allow_all_from_cloud9_instance" {
  name        = "allow_all"
  description = "Allow all traffic from cloud9 instance"

  ingress {
      description      = "allow all from cloud9"
      from_port        = 0
      to_port          = 0
      protocol         = "-1"
      cidr_blocks      = ["your_instance_public_ip/32"]
  }

  egress {
      from_port        = 0
      to_port          = 0
      protocol         = "-1"
      cidr_blocks      = ["0.0.0.0/0"]
      ipv6_cidr_blocks = ["::/0"]
  }

  tags = {
    Name = "allow_all_from_cloud9_instance"
  }
}
```

For example, if your instance public ip is 10.11.12.13:

```
resource "aws_security_group" "allow_all_from_cloud9_instance" {
  name        = "allow_all"
  description = "Allow all traffic from cloud9 instance"

  ingress {
      description      = "allow all from cloud9"
      from_port        = 0
      to_port          = 0
      protocol         = "-1"
      cidr_blocks      = ["10.11.12.13/32"]
  }
```

## 3. Set the Genymotion Virtual Devices to run

In this section, you can set the various settings of the devices to start:

```
resource "aws_instance" "example" {
  count = "2"
  ami = "ami-0e2ddff71d3da44a7"
  instance_type = "m6g.medium"
  tags = {
    Name = "gm-workshop-${count.index}"
  }
  #security group
  vpc_security_group_ids = ["${aws_security_group.allow_all_from_cloud9_instance.id}"]
  
}
```

- **count** is the number of instances you wish to start in parallel. In this example, 2 instances are spawned simultaneously.

- **ami** is the AMI ID of the Genymotion device to start. It can be a public image or a custom device with application dependencies already installed - please refer to this [link](https://docs.genymotion.com/paas/latest/02_Getting_Started/021_AWS.html#create-custom-images-templates) if you wish to know on how to create a custom image. In this example, we are going to use a public Genymotion image.
To get an AMI ID, in AWS EC2 Admin panel, go to Images/AMIs, select Public images and filter with genymotion. You will then get all the AMI IDs of the Genymotion devices available.
<img src="/images/gm-lab/ami.png">
The nomenclature is : genymotion-ami-ANDROID_VERSION-GENYMOTION_VERSION-TYPE. In our example, we take the genymotion-ami-8.0-v11.0-ARMxxxx device which is an Android 8.0 ARM-based device with Genymotion 11.0 version. If it is an x86-based device, it is blank.

- **instance_type** is the [instance type](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-types.html#AvailableInstanceTypes) you wish to use for your instances.

{{% notice warning %}}
Make sure to use an instance type that matches your AMI CPU architecture: standard types for x86_64 AMIs and ARM version types for ARM64 AMIs.
{{% /notice %}}

- In **tags/Name**, set a label for each device started. In our template, we use `gm-workshop-${count.index}` - so each instance will be named "gm-workshop-" followed by an incremented number.

- **vpc_security_group_ids** defines the security group id. This should be left unchanged.
