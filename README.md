# bootc-rhel-management-setup
This repo includes ansible playbooks and roles for setting up [a demo environment](https://github.com/yukshimizu/bootc-rhel-management-demo) of managing RHEL bootc images with Red Hat Ansible Automation Platform on AWS EC2.

## Environment to be set up
- Single VPC
- Single subnet
- Single route table
- Single gateway
- Single S3 bucket
- Single Ansible Automation Controller
- Single Builder VM

The setup looks like the following:

![](bootc-rhel-management-setup.png)

## Included contents
### Roles
|Name     |Description|
|:--------|:----------|
|aac      |A role to create an Ansible Automation Controller on AWS EC2.|
|builder  |A role to create a Builder VM on AWS EC2.|

### Playbooks
|Name     |Role Used|Description|
|:--------|:--------|:----------|
|`create_networks.yml`|N/A|Create required AWS network resources.|
|`delete_networks.yml`|N/A|Delete AWS network resources created in `create_networks` playbook.|
|`create_s3_resources.yml`|N/A|Create required S3 resources.|
|`delete_s3_resources.yml`|N/A|Delete S3 resources created in `create_s3_resources` playbook.|
|`create_aac_vm.yml`|[roles.aac](roles/aac/README.md)|Create an AWS instance and set up Ansible Automation Controller.|
|`create_aac_demo.yml`|N/A|Create the Demo environment on Ansible Automation Controller.|
|`delete_aac_vm.yml`|N/A|Delete the instance created in `create_aac_vm` playbook.|
|`create_builder_vm.yml`|[roles.builder](roles/builder/README.md)|Create an AWS instance and set up builder server.|
|`delete_builder_vm.yml`|N/A|Delete the instance created in `create_builder_vm` playbook.|

## Prerequisites
### Basic requirements for Ansible
Any control node:
- ansible core 2.15+

Ansible collections:
- amazon.aws
- awx.awx
- redhat.rhel_system_roles

In order for functioning Ansible EC2, you need to install Python Boto3 library.
```
# pip3 install boto3
```
### ansible.cfg
Update the following line with your EC2 private key file.
```
[default]
private_key_file = "path to EC2 private key file"
```
### Environment variables
In this setup, you should set the following environment variables on your control node.
```
$ export AWS_DEFAULT_REGION=ap-northeast-1
$ export AWS_ACCESS_KEY_ID=AKIAIOSFODNN7EXAMPLE
$ export AWS_SECRET_ACCESS_KEY=wJatrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
```

### Subscriptions
Basically, the demo environment requires to get access to Red Hat gold image, so you must have a matching Red Hat product subscription and must connect their cloud provider accounts to Red Hat.
Please refer to the [Cloud Access user interface](https://access.redhat.com/management/cloud) or Cloud Sources on [cloud.redhat.com](https://cloud.redhat.com/) as described in [Chapter 2, Getting started with Cloud Access](https://access.redhat.com/documentation/en-us/subscription_central/2023/html/red_hat_cloud_access_reference_guide/getting-started-with-ca_cloud-access).

Required subscriptions:
- Red Hat Enterprise Linux for x86_64
- Red Hat Ansible Automation Platform Subscription

## Usage
### Create required network resources
These variables should be set in group_vars beforehand.
```
aws_vpc: demo_vpc
aws_vpc_cidr_block: 10.0.0.0/16 # adjust with your preference
aws_vpc_subnet_name: demo_subnet
aws_subnet_cidr_block: 10.0.1.0/24 # adjust with your preference
aws_igw_name: demo_gtw
aws_routetable_name: demo_rtb
aws_securitygroup_name: demo_sg

purpose: demo
```

This playbook need to be run at the beginning.
```
$ ansible-playbook create_networks.yml
```

### Create required S3 resources
These variables should be set in group_vars beforehand.
```
aws_s3_bucket: rhel9-bootc
aws_iam_role: vmimport # requires to align with aws-role.json
aws_iam_policy: vmimport # requires to align with aws-policy.json

purpose: demo
```
Also `aws-role.json` and `aws-policy.json` need to be located in `files` directory.

This playbook should be run before performing a demo.
```
$ ansible-playbook create_s3_resources.yml
```

### Create Ansible Automation Controller
These variables should be set in group_vars beforehand.
```
aws_vpc_subnet_name: demo_subnet
aws_securitygroup_name: demo_sg
aws_aac_instance_ami: ami-07c8a8c166f570f4a # ami of RHEL-9.4_HVM_GA-20240827-x86_64-0-Access2-GP3
aws_aac_instance_size: t2.xlarge # should not be modified

aac_vm_type: aac # should not be modified
aac_vm_name: aac01

purpose: demo
```

And, the following variables are prompted at run-time. Also refer to [roles.aac](roles/aac/README.md) for the role details.
```
aws_keypair_name # Your AWS key pair name corresponding to the private key
rhsm_username # Your Red Hat login name
rhsm_passwd # Password for your Red Hat login
aac_admin_passwd # Password for your AAC admin user
aac_pg_passwd # PostgreSQL password for your AAC deployment
```

This playbook can run after running `create_networks` playbook.
```
$ ansible-playbook create_aac_vm.yml
```

### Create Builder VM
These variables should be set in group_vars beforehand.
```
aws_vpc_subnet_name: demo_subnet
aws_securitygroup_name: demo_sg
aws_builder_instance_ami: ami-07c8a8c166f570f4a # ami of RHEL-9.4_HVM_GA-20240827-x86_64-0-Access2-GP3
aws_builder_instance_size: t2.medium # can be bigger instance size

builder_vm_type: builder # should not be modified
builder_vm_name: builder01

purpose: demo
```

And, the following variables are prompted at run-time. Also refer to [roles.builder](roles/builder/README.md) for the role details.
```
aws_keypair_name # Your AWS key pair name corresponding to the private key
rhsm_username # Your Red Hat login name
rhsm_passwd # Password for your Red Hat login
```

This playbook can run after running `create_networks` playbook.
```
$ ansible-playbook create_builder_vm.yml
```

### Clean up the environment
All the delete resource playbooks corresponding to each create resource playbook are avaialble. Those playbooks can run assuming related variables have already set previously.
