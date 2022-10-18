# Manipulating EC2 Instances with Ansible

![manipulating-ec2-instances-with-ansible](https://user-images.githubusercontent.com/106797604/196349542-5bae7d31-c63e-45b7-a53b-e14774c5f306.png)

# Introduction
EC2 is at the heart of AWS as the primary compute resource on the platform. Ansible provides several modules that allow us to interact with EC2 instances. Being able to provision and manipulate EC2 instances within Ansible allows for infrastructure automation to be built into a deployment strategy. This exercise will allow students to explore the EC2 functionality in Ansible.

We have been tasked with creating automation that will redeploy an EC2 instance from an updated AMI. We will need to stop the currently running instance, tagged with Name: Leo. Then we will need to deploy a single new EC2 instance meeting the following requirements:

Type: t2.micro
AMI: Same as existing instance
Region: us-east-1
Public IP: Yes
VPC Subnet ID: Same as existing instance
Assign the tag Name: New to the new instance
We will need to consult the AWS console (or, alternatively, use Ansible facts) to determine the subnet ID and AMI ID of the existing instance, in order to assign the correct value for the new instance.

# From the Ansible Control node:
- Create the playbook /home/ansible/deploy.yml to perform the following tasks:
           . Stop the EC2 instance tagged Name: Leo.
           . Deploy a new EC2 instance meeting the described properties.
- Run the playbook /home/ansible/deploy.yml
- Validate that our work in the AWS Web Console is correct.

The Ansible control node has been configured and already has Ansible installed. The control node also has a system user named ansible configured with SSH access keys and necessary system privileges.

An IAM user ansible has been created on the provided AWS sandbox account. The access keys for the ansible IAM user are stored in /home/ansible/keys.sh and /home/ansible/keys.yml for whichever authentication method we prefer. The ansible IAM user has appropriate permissions to perform the required task.

The default Ansible inventory has been configured to include the Ansible control host as localhost.

# Instructions
We have been tasked with creating automation that will redeploy an EC2 instance from an updated AMI. We will need to stop the currently running instance, tagged with Name: Leo. Then we will need to deploy a single new EC2 instance meeting the following requirements:

Type: t2.micro
AMI: Same as existing instance
Region: us-east-1
Public IP: Yes
VPC Subnet ID: Same as existing instance
Assign the tag Name: New to the new instance
We will need to consult the AWS console (or, alternatively, use Ansible facts) to determine the subnet ID and AMI ID of the existing instance, in order to assign the correct value for the new instance.

From the Ansible Control node:

Create the playbook /home/ansible/deploy.yml to perform the following tasks:

Stop the EC2 instance tagged Name: Leo.
Deploy a new EC2 instance meeting the described properties.
Run the playbook /home/ansible/deploy.yml

Validate that our work in the AWS Web Console is correct.

The Ansible control node has been configured and already has Ansible installed. The control node also has a system user named ansible configured with SSH access keys and necessary system privileges.

An IAM user ansible has been created on the provided AWS sandbox account. The access keys for the ansible IAM user are stored in /home/ansible/keys.sh and /home/ansible/keys.yml for whichever authentication method we prefer. The ansible IAM user has appropriate permissions to perform the required task.

The default Ansible inventory has been configured to include the Ansible control host as localhost.

# Logging In
Use the hands-on lab page to get the public IP of the cloud server we need to log into (making sure to use cloud_user as a username), then switch to the ansible user. The password for the two users is the same.

# Create and Edit /home/ansible/deploy.yml and Add Ansible Tasks to Stop the Existing EC2 Instance, by Tag, Then Deploy a New EC2 Instance That Meets the Specification Described in the Instructions.
Create and edit the playbook (/home/ansible/deploy.yml) so that it resembles the following:

- hosts: localhost
  gather_facts: no
  vars_files:
    - /home/ansible/keys.yml
  tasks:
    - name: Get Subnet ID and AMI ID from existing server.
      ec2_instance_facts:
        aws_access_key: "{{ AWS_ACCESS_KEY_ID }}"
        aws_secret_key: "{{ AWS_SECRET_ACCESS_KEY }}"
        region: "{{ AWS_REGION }}"
        filters:
          tag:Name: Leo
      register: ec2_facts

    - name: Stop Leo Instance
      ec2:
        aws_access_key: "{{ AWS_ACCESS_KEY_ID }}"
        aws_secret_key: "{{ AWS_SECRET_ACCESS_KEY }}"
        ec2_region: us-east-1
        state: stopped
        instance_tags:
          Name: Leo

    - name: Deploy new EC2 Instance
      ec2:
       aws_access_key: "{{ AWS_ACCESS_KEY_ID }}"
       aws_secret_key: "{{ AWS_SECRET_ACCESS_KEY }}"
       ec2_region: us-east-1
       instance_type: t2.micro
       image: "{{ ec2_facts.instances[0].image_id }}"
       assign_public_ip: yes
       vpc_subnet_id: "{{ ec2_facts.instances[0].subnet_id }}"
       instance_tags:
         Name: New
# Run the Playbook /home/ansible/deploy.yml to Perform the Required Tasks, Then Log into the AWS Console to Validate that Everything Works
Run the following command:
ansible-playbook /home/ansible/deploy.yml
Log into the AWS Console, and in the EC2 Dashboard (find it by searching for EC2 in the Find Services search box) confirm the new instance's existence and state.
It might be best to wait a bit before checking. Once everything is finished processing though, we'll see a Leo instance that's stopped, and a new one called New that is running.

# Conclusion
We've finished. We were able to do exactly what people required of us, which was to create an Ansible playbook that fires up an EC2 instance named New after stopping one named Leo. Congratulations!

# Create and Edit `/home/ansible/deploy.yml` and Add Ansible Tasks to Stop the Existing EC2 Instance, by Tag, Then Deploy a New EC2 Instance That Meets the Specification Described in the Instructions.
After logging into the EC2 instance, run su - ansible to become the ansible user. The password is the same as it is for cloud_user.

Create and edit the playbook (/home/ansible/deploy.yml) so that it resembles the following:

- hosts: localhost
  gather_facts: no
  vars_files:
    - /home/ansible/keys.yml
  tasks:
    - name: Get Subnet ID and AMI ID from existing server.
      ec2_instance_facts:
        aws_access_key: "{{ AWS_ACCESS_KEY_ID }}"
        aws_secret_key: "{{ AWS_SECRET_ACCESS_KEY }}"
        region: "{{ AWS_REGION }}"
        filters:
          tag:Name: Leo
      register: ec2_facts

    - name: Stop Leo Instance
      ec2:
        aws_access_key: "{{ AWS_ACCESS_KEY_ID }}"
        aws_secret_key: "{{ AWS_SECRET_ACCESS_KEY }}"
        ec2_region: us-east-1
        state: stopped
        instance_tags:
          Name: Leo

    - name: Deploy new EC2 Instance
      ec2:
       aws_access_key: "{{ AWS_ACCESS_KEY_ID }}"
       aws_secret_key: "{{ AWS_SECRET_ACCESS_KEY }}"
       ec2_region: us-east-1
       instance_type: t2.micro
       image: "{{ ec2_facts.instances[0].image_id }}"
       assign_public_ip: yes
       vpc_subnet_id: "{{ ec2_facts.instances[0].subnet_id }}"
       instance_tags:
         Name: New

# Run the Playbook `/home/ansible/deploy.yml` to Perform the Required Tasks, Then Log into the AWS Console to Validate that Everything Works
Run the following command:
ansible-playbook /home/ansible/deploy.yml
Log into the AWS Console, and in the EC2 Dashboard (find it by searching for EC2 in the Find Services search box) confirm the new instance's existence and state.
It might be best to wait a bit before checking. Once everything is finished processing though, we'll see a Leo instance that's stopped, and a new one called New that is running.
