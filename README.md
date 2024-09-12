# How to Automate AWS EC2 Instance Creation with Ansible

![Ansible](https://github.com/user-attachments/assets/9ad52531-4b35-425f-98db-b387b40882d8)

In this tutorial, we will automate the creation of an AWS EC2 instance using Ansible. Ansible is a powerful automation tool that can be used to manage cloud resources easily. Follow along as we set up the required configuration and playbook.

# Prerequisites

AWS Account: You need access to your AWS account.
AWS CLI: Installed and configured with your credentials.
Ansible: Installed on your local machine or remote server.
Boto3: Python library to interact with AWS APIs. Install it with pip install boto3.

# Create an AWS IAM Access Key

# Step 1: Sign in to the AWS Management Console
Log in to your AWS Management Console using your credentials.

# Step 2: Navigate to IAM
In the search bar, type IAM and select IAM to open the Identity and Access Management dashboard.

# Step 3: Select Users
On the left-hand side menu, click Users.

# Step 4: Choose a User
Select the user for whom you want to create the access key. If you don’t have a user, create one by clicking Add User and following the prompts.

# Step 5: Create Access Key
Click the Security credentials tab.
Scroll down to the Access keys section.
Click the Create access key.

# Step 6: Download Access Key
AWS will generate an Access Key ID and Secret Access Key.
Download the credentials or copy them. You will not be able to view the secret key again after closing the window.

# Step 7: Use the Access Key
Use the Access Key ID and Secret Access Key to configure the AWS CLI, SDKs, or tools like Ansible, Terraform, etc.

![IAM-access-key](https://github.com/user-attachments/assets/d4fda9ae-2f85-4f5f-8876-12816d982ddb)

# Ansible Setup

# Step 1: Set Up Ansible Environment

First, ensure you have a directory for your Ansible configuration and playbooks:

```
sudo apt update
sudo apt install ansible
mkdir ansible-ec2-automation
cd ansible-ec2-automation
```

# Step 2: Define AWS Credentials

# Update AWS CLI and Dependencies

The easiest way to resolve dependency issues is by updating awscli, botocore, and related libraries.
```
sudo apt update
sudo apt install awscli
pip3 install boto3 botocore
```

# Verify Installation

verify if AWS CLI works properly:
```
aws --version
```
 
 # Set Up AWS CLI Credentials If you haven’t already, you can configure the AWS CLI on your machine using the following command:
```
ansible-galaxy collection install amazon.aws:==3.0.0

aws configure

AWS_ACCESS_KEY_ID=your_access_key
AWS_SECRET_ACCESS_KEY=your_secret_key
AWS_REGION=your_region

```

# Step 3: Create Ansible Inventory

Define your inventory in a file called inventory:
```
localhost ansible_connection=local
```

# Step 4: Write Ansible Playbook
Now, create the ec2_create.yml playbook. This playbook will create an EC2 instance using the Ansible EC2 module.

# ec2_create.yml
```
---
- name: Create EC2 instance
  hosts: localhost
  gather_facts: False
  tasks:
    - name: Launch EC2 instance
      amazon.aws.ec2_instance:
        key_name: "{{ key_name }}"
        instance_type: "{{ instance_type }}"
        image_id: "{{ ami_id }}"
        region: "{{ region }}"
        count: 1
        vpc_subnet_id: "{{ subnet_id }}"  # Specify your subnet ID
        security_group: "{{ security_group }}"  # Security group for SSH access
        tags:
          Name: "Ansible-Instance"
          Environment: "Production"
        state: present
        wait: true
        wait_timeout: 600
      register: ec2

    - name: Show EC2 instance details
      debug:
        var: ec2
```

# Vars.yml
```
key_name: "Ansible-Playbook"                                                 # Replace with your SSH key pair name
instance_type: "t2.micro"                                                    # Specify the instance type
ami_id: "ami-0474411b350de35fb"                                              # Replace with the AMI ID of your region
region: "ap-southeast-2"                                                     # Specify the AWS region
subnet_id: "subnet-053fd32f2556e7a23"                                        # Replace with your subnet ID
security_group: "sg-0c48f554ef219fbb5"                                       # Replace with your security group ID
```

# inventory.ini 
```
[localhost]
localhost ansible_connection=local
```

# Run the Ansible Playbook
To execute the playbook, run the following command from the directory where you created the files:
```
ansible-playbook -i inventory.ini ec2-playbook.yml -e @vars.yml
```
![Ansible-Playbook-1](https://github.com/user-attachments/assets/6a5519e1-f776-4480-aca5-ef526912b39b)

![Ansible-playbook-2](https://github.com/user-attachments/assets/86708046-50ce-4c19-92e8-a47cc2d80bdb)

# AWS EC2 Instance 

![Aws-ec2-ready-state](https://github.com/user-attachments/assets/57649197-45b3-4eb0-844f-1bf88c36f4e6)

# Connect through SSH Client

![Ssh-client-ec2](https://github.com/user-attachments/assets/33eba23c-2938-4c7a-aa80-e38021404fdc)


```
ssh -i "Ansible-Playbook.pem" ec2-user@ec2-3-104-74-16.ap-southeast-2.compute.amazonaws.com
```

![SSH](https://github.com/user-attachments/assets/a29bf3c3-61a7-49ad-a6d2-c9332a8361fc)
