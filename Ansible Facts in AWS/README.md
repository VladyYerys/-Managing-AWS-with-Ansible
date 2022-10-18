# Ansible Facts in AWS

![ansible-facts-in-aws](https://user-images.githubusercontent.com/106797604/196440262-c9006e86-91a3-4cb1-8933-34ab9af06c15.png)

# Introduction
For just about every Ansible module that performs an AWS task, there is a corresponding module for collecting facts regarding the related AWS component. A thorough understanding of the AWS principles in Ansible can help with implementing automation. This exercise promotes exploration of the facts provided for various AWS-related modules.

# Additional Resources
To prepare for a possible security audit, we have been tasked with building some automation that can make a list of firewall rules on a target EC2 instance by tag, as well as show affiliated VPC and Subnet information. We will be developing the automation using Ansible in an AWS sandbox environment. We can gather the needed information from Ansible facts.

This is the information that needs to be in the report:

The VPC ID
The subnet ID affiliated with the VPC
The ip_permissions fact of the Security Group affiliated with an EC2 instance that has the tag Name with the value Leo
From the Ansible Control node:

Create and edit /home/ansible/report.yml to write the required information to the file /home/ansible/report.txt.
The file /home/ansible/report.txt should resemble the following:
  VPC ID: vpc-0058f82290a1b4f91
  Subnet ID: subnet-046390cbfd8fa8747
  Security Group Rule Set: [{u'from_port': 80, u'ip_protocol': u'tcp', u'to_port': 80, u'ip_ranges': [{u'cidr_ip': u'0.0.0.0/0'}], u'prefix_list_ids': [], u'ipv6_ranges': [], u'user_id_group_pairs': [{u'group_id': u'sg-054ddc7992d607a8c', u'user_id': u'041840987519'}]}, {u'from_port': 22, u'ip_protocol': u'tcp', u'to_port': 22, u'ip_ranges': [{u'cidr_ip': u'0.0.0.0/0'}], u'prefix_list_ids': [], u'ipv6_ranges': [], u'user_id_group_pairs': []}, {u'from_port': 443, u'ip_protocol': u'tcp', u'to_port': 443, u'ip_ranges': [{u'cidr_ip': u'0.0.0.0/0'}], u'prefix_list_ids': [], u'ipv6_ranges': [], u'user_id_group_pairs': []}, {u'from_port': -1, u'ip_protocol': u'icmp', u'to_port': -1, u'ip_ranges': [{u'cidr_ip': u'0.0.0.0/0'}], u'prefix_list_ids': [], u'ipv6_ranges': [], u'user_id_group_pairs': []}]
Run the playbook /home/ansible/report.yml to validate your work.
The Ansible control node has been configured and already has Ansible installed. The control node also has a system user named ansible configured with SSH access keys and necessary system privileges.

An IAM user ansible has been created on the provided AWS sandbox account. The access keys for the ansible IAM user are stored in /home/ansible/keys.sh and /home/ansible/keys.yml for whichever authentication method we prefer. The ansible IAM user has appropriate permissions to perform the required task.

The default Ansible inventory has been configured to include the Ansible control host as localhost.

# Instructions
To prepare for a possible security audit, we have been tasked with building some automation that can make a list of firewall rules on a target EC2 instance by tag, as well as show affiliated VPC and Subnet information. We will be developing the automation using Ansible in an AWS sandbox environment. We can gather the needed information from Ansible facts.

This is the information that needs to be in the report:

The VPC ID
The subnet ID affiliated with the VPC
The ip_permissions fact of the Security Group affiliated with an EC2 instance that has the tag Name with the value Leo
From the Ansible Control node:

Create and edit /home/ansible/report.yml to write the required information to the file /home/ansible/report.txt.
The file /home/ansible/report.txt should resemble the following:
  VPC ID: vpc-0058f82290a1b4f91
  Subnet ID: subnet-046390cbfd8fa8747
  Security Group Rule Set: [{u'from_port': 80, u'ip_protocol': u'tcp', u'to_port': 80, u'ip_ranges': [{u'cidr_ip': u'0.0.0.0/0'}], u'prefix_list_ids': [], u'ipv6_ranges': [], u'user_id_group_pairs': [{u'group_id': u'sg-054ddc7992d607a8c', u'user_id': u'041840987519'}]}, {u'from_port': 22, u'ip_protocol': u'tcp', u'to_port': 22, u'ip_ranges': [{u'cidr_ip': u'0.0.0.0/0'}], u'prefix_list_ids': [], u'ipv6_ranges': [], u'user_id_group_pairs': []}, {u'from_port': 443, u'ip_protocol': u'tcp', u'to_port': 443, u'ip_ranges': [{u'cidr_ip': u'0.0.0.0/0'}], u'prefix_list_ids': [], u'ipv6_ranges': [], u'user_id_group_pairs': []}, {u'from_port': -1, u'ip_protocol': u'icmp', u'to_port': -1, u'ip_ranges': [{u'cidr_ip': u'0.0.0.0/0'}], u'prefix_list_ids': [], u'ipv6_ranges': [], u'user_id_group_pairs': []}]
Run the playbook /home/ansible/report.yml to validate your work.
The Ansible control node has been configured and already has Ansible installed. The control node also has a system user named ansible configured with SSH access keys and necessary system privileges.

An IAM user ansible has been created on the provided AWS sandbox account. The access keys for the ansible IAM user are stored in /home/ansible/keys.sh and /home/ansible/keys.yml for whichever authentication method we prefer. The ansible IAM user has appropriate permissions to perform the required task.

The default Ansible inventory has been configured to include the Ansible control host as localhost.

# Logging In
Use the hands-on lab page to get the public IP of the cloud server we need to log into (making sure to use cloud_user as a username), then switch to the ansible user. The password for the two users is the same.

# Create and Edit /home/ansible/report.yml and Add Ansible Tasks That Output the Required Values into report.txt
Create and edit the playbook such that it resembles the following:

- hosts: localhost
  gather_facts: no
  vars_files:
    - /home/ansible/keys.yml
  tasks:
    - name: Get VPC facts
      ec2_vpc_net_facts:
        aws_access_key: "{{ AWS_ACCESS_KEY_ID }}"
        aws_secret_key: "{{ AWS_SECRET_ACCESS_KEY }}"
        region: "{{ AWS_REGION }}"
      register: vpc_facts
    - name: Add line to report.txt
      lineinfile:
        path: /home/ansible/report.txt
        line: "VPC ID: {{ vpc_facts.vpcs[0].vpc_id }}"
        create: yes

    - name: Get VPC Subnet Facts
      ec2_vpc_subnet_facts:
        aws_access_key: "{{ AWS_ACCESS_KEY_ID }}"
        aws_secret_key: "{{ AWS_SECRET_ACCESS_KEY }}"
        region: "{{ AWS_REGION }}"
        filters:
          vpc-id: "{{ vpc_facts.vpcs[0].vpc_id }}"
      register: subnet_facts
    - name: Add line to report.txt
      lineinfile:
        path: /home/ansible/report.txt
        line: "Subnet ID: {{ subnet_facts.subnets[0].subnet_id }}"

    - name: Get EC2 instance facts
      ec2_instance_facts:
        aws_access_key: "{{ AWS_ACCESS_KEY_ID }}"
        aws_secret_key: "{{ AWS_SECRET_ACCESS_KEY }}"
        region: "{{ AWS_REGION }}"
        filters:
          tag:Name: "Leo"
      register: ec2_facts

    - name: Get Security Group facts
      ec2_group_facts:
        aws_access_key: "{{ AWS_ACCESS_KEY_ID }}"
        aws_secret_key: "{{ AWS_SECRET_ACCESS_KEY }}"
        region: "{{ AWS_REGION }}"
        filters:
          group-id: "{{ ec2_facts.instances[0].security_groups[0].group_id }}"
      register: security_group_facts
    - name: Add line to report.txt
      lineinfile:
        path: /home/ansible/report.txt
        line: "Security Group Rule Set: {{ security_group_facts.security_groups[0].ip_permissions }}"

# Run the Modified /home/ansible/report.yml to Validate That the Playbook Successfully Generates the Report
Run the following command:
 - ansible-playbook /home/ansible/report.yml

# 1.Create and Edit `/home/ansible/report.yml` and Add Ansible Tasks That Output the Required Values into `report.txt`

After logging into the EC2 instance, run su - ansible to become the ansible user. The password is the same as it is for cloud_user.

Create and edit the playbook such that it resembles the following:

- hosts: localhost
gather_facts: no
vars_files:
- /home/ansible/keys.yml
tasks:
- name: Get VPC facts
 ec2_vpc_net_facts:
   aws_access_key: "{{ AWS_ACCESS_KEY_ID }}"
   aws_secret_key: "{{ AWS_SECRET_ACCESS_KEY }}"
   region: "{{ AWS_REGION }}"
 register: vpc_facts
- name: Add line to facts.txt
 lineinfile:
   path: /home/ansible/facts.txt
   line: "VPC ID: {{ vpc_facts.vpcs[0].vpc_id }}"

- name: Get VPC Subnet Facts
 ec2_vpc_subnet_facts:
   aws_access_key: "{{ AWS_ACCESS_KEY_ID }}"
   aws_secret_key: "{{ AWS_SECRET_ACCESS_KEY }}"
   region: "{{ AWS_REGION }}"
   filters:
     vpc-id: "{{ vpc_facts.vpcs[0].vpc_id }}"
 register: subnet_facts
- name: Add line to facts.txt
 lineinfile:
   path: /home/ansible/facts.txt
   line: "Subnet ID: {{ subnet_facts.subnets[0].subnet_id }}"

- name: Get EC2 instance facts
 ec2_instance_facts:
   aws_access_key: "{{ AWS_ACCESS_KEY_ID }}"
   aws_secret_key: "{{ AWS_SECRET_ACCESS_KEY }}"
   region: "{{ AWS_REGION }}"
   filters:
     tag:Name: "Leo"
 register: ec2_facts

- name: Get Security Group facts
 ec2_group_facts:
   aws_access_key: "{{ AWS_ACCESS_KEY_ID }}"
   aws_secret_key: "{{ AWS_SECRET_ACCESS_KEY }}"
   region: "{{ AWS_REGION }}"
   filters:
     group-id: "{{ ec2_facts.instances[0].security_groups[0].group_id }}"
 register: security_group_facts
- name: Add line to facts.txt
 lineinfile:
   path: /home/ansible/facts.txt
   line: "Security Group Rule Set: {{ security_group_facts.security_groups[0].ip_permissions }}"

# 2.Run the Modified `/home/ansible/report.yml` to Validate That the Playbook Successfully Generates the Report

Log into the Ansible control node as the ansible user.
Run the following command:
ansible-playbook /home/ansible/report.yml


# Conclusion
Now if we run cat report.txt, we're going to see all of the information that we've been asked to get. We can hand this text file over to who needs it, because we're done. Congratulations!
