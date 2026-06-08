# AWS
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

![](../images/bootc_aws.png)

## Included contents

### Playbooks
|Name     |Role Used|Description|
|:--------|:--------|:----------|
|`create_networks.yml`|N/A|Create required AWS network resources.|
|`delete_networks.yml`|N/A|Delete AWS network resources created in `create_networks` playbook.|
|`create_s3_resources.yml`|N/A|Create required S3 resources.|
|`delete_s3_resources.yml`|N/A|Delete S3 resources created in `create_s3_resources` playbook.|
|`create_aap_vm.yml`|[roles.aap](../roles/aap/README.md)|Create an AWS instance and set up Ansible Automation Controller.|
|`delete_aap_vm.yml`|N/A|Delete the instance created in `create_aap_vm` playbook.|
|`create_builder_vm.yml`|[roles.builder](../roles/builder/README.md)|Create an AWS instance and set up builder server.|
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

### group_vars/all.yml — required values
Open `group_vars/all.yml` and fill in every field marked `# REQUIRED`:
```
aws_builder_instance_ami: ami-07c8a8c166f570f4a # REQUIRED: e.g. RHEL-9.4_HVM_GA-20240827-x86_64-0-Access2-GP3
aws_aap_instance_ami: ami-07c8a8c166f570f4a # REQUIRED: e.g. RHEL-9.4_HVM_GA-20240827-x86_64-0-Access2-GP3
```

## Usage

### 1. Create required network resources
This playbook need to be run at the beginning.
```
$ ansible-playbook create_networks.yml
```

### 2. Create required S3 resources
`aws-role.json` and `aws-policy.json` need to be located in `files` directory.

This playbook should be run before performing a demo.
```
$ ansible-playbook create_s3_resources.yml
```

### 3. Create Ansible Automation Platform
This playbook can run after running `create_networks` playbook.
```
$ ansible-playbook create_aap_vm.yml
```

And, the following variables are prompted at run-time. Also refer to [roles.aap](../roles/aap/README.md) for the role details.
```
aws_keypair_name # Your AWS key pair name corresponding to the private key
rhsm_username # Your Red Hat login name
rhsm_passwd # Password for your Red Hat login
aap_admin_passwd # Password for your AAP admin user
aap_pg_passwd # PostgreSQL password for your AAP deployment
```

### 4. Create Builder VM
This playbook can run after running `create_networks` playbook.
```
$ ansible-playbook create_builder_vm.yml
```

And, the following variables are prompted at run-time. Also refer to [roles.builder](../roles/builder/README.md) for the role details.
```
aws_keypair_name # Your AWS key pair name corresponding to the private key
rhsm_username # Your Red Hat login name
rhsm_passwd # Password for your Red Hat login
```

### 5. Clean up the environment
All the delete resource playbooks corresponding to each create resource playbook are avaialble. Those playbooks can run assuming related variables have already set previously.
```
$ ansible-playbook delete_builder_vm.yml
$ ansible-playbook delete_aap_vm.yml
$ ansible-playbook delete_s3_resources.yml
$ ansible-playbook delete_networks.yml
```