# Working with AMIs Using Ansible

![working-with-amis-using-ansible](https://user-images.githubusercontent.com/106797604/196351297-e59c6654-9990-4abd-834b-f4ad4b576653.png)


# Introduction
Image creation in AWS provides for both simplified system management and improved deployment performance. Ansible can be leveraged to automate AMI upkeep, and we will be doing just that in this exercise!

# Additional Resources
In an effort to save some time, you have decided to automate the process for maintaining AMIs using Ansible. You have a deployed EC2 instance on which the AMI is to be based. The instance is configured for access from your Ansible control node using the hostname "node1".

Prior to creating the AMI, the playbook needs to make the following changes to "node1":

Install the latest updates using the yum module.
Use the lineinfile module to update the file /home/ansible/image.txt with the line "Image updated <current.date.use.ansible.facts>".
From the Ansible Control node:

Create the playbook /home/ansible/updateAMI.yml to perform the following tasks:
Update all packages on node1 using the yum module.
Insert the line "Image updated <current.date>" into the file on /home/ansible/image.txt on "node1".
Stop the EC2 instance for node1 using AWS.
Create a new AMI based on node1.
Write the AMI ID to the file /home/ansible/ami.txt.
Run the playbook /home/ansible/updateAMI.yml.
Verify your work in the AWS Web Console.
The Ansible control node has been configured for you and has already had Ansible installed. The control node also has a system user named ansible configured with ssh access keys and necessary system privileges.

An IAM user called ansible has been created on the provided AWS sandbox account. The access keys for the ansible IAM user are stored in /home/ansible/keys.sh and /home/ansible/keys.yml for which ever authentication method you prefer. The ansible IAM user has appropriate permissions to perform the required task.

The default Ansible inventory has been configured to include a the Ansible control host as 'localhost' and "node1".

# Instructions
In an effort to save some time, you have decided to automate the process for maintaining AMIs using Ansible. You have a deployed EC2 instance on which the AMI is to be based. The instance is configured for access from your Ansible control node using the hostname node1.

Prior to creating the AMI, the playbook needs to make the following changes to node1:

Install the latest updates using yum.
Use the lineinfile module to update the file /home/ansible/image.txt with the line Image updated <current.date.use.ansible.facts>.
From the Ansible Control node:

Create the playbook /home/ansible/updateAMI.yml to perform the following tasks:
Update all packages on node1 using the yum module.
Insert the line "Image updated <current.date>" into the file on /home/ansible/image.txt on "node1".
Stop the EC2 instance for node1 using AWS.
Create a new AMI based on node1.
Write the AMI ID to the file /home/ansible/ami.txt.
Run the playbook /home/ansible/updateAMI.yml.
Verify your work in the AWS Web Console.
The Ansible control node has been configured and already has Ansible installed. The control node also has a system user named ansible configured with SSH access keys and necessary system privileges.

An IAM user ansible has been created on the provided AWS sandbox account. The access keys for the ansible IAM user are stored in /home/ansible/keys.sh and /home/ansible/keys.yml for whichever authentication method we prefer. The ansible IAM user has appropriate permissions to perform the required task.

The default Ansible inventory has been configured to include the Ansible control host as localhost.

# Logging In
Use the hands-on lab page to get the public IP of the cloud server we need to log into (making sure to use cloud_user as a username), then switch to the ansible user. The password for the two users is the same.

# Create /home/ansible/updateAMI.yml, and Add an Ansible Play that Updates the Software on node1 and Updates /home/ansible/image.txt
In our solution, we'll complete the required steps and add a task for collecting facts from EC2 metadata, which will help us collect the instance ID of node1.

Edit the playbook such that it resembles the following:

- hosts: node1
  become: yes
  tasks:
    - name: Update packages
      yum:
        name: "*"
        state: latest
    - name: Update image.txt
      lineinfile:
        path: /home/ansible/image.txt
        line: "Image updated {{ ansible_date_time.date }}"
    - name: Gather facts
      ec2_metadata_facts:

# Edit the /home/ansible/updateAMI.yml and Add an Ansible Play that Collects the instance_id of node1, stops node1, Creates the AMI as Described in the Instructions, and Stores the AMI ID in the File /home/ansible/ami.txt
We will be using the facts we collected in the first play to satisfy the objectives for this task.

Edit the playbook such that it resembles the following:

- hosts: localhost
  tasks:
    - name: Stop node1
      local_action: ec2
      args:
        region: us-east-1
        state: stopped
        instance_id: "{{ hostvars['node1'].ansible_ec2_instance_id }}"
        wait: yes
    - name: Create AMI
      local_action: ec2_ami
      args:
        state: present
        instance_id: "{{ hostvars['node1'].ansible_ec2_instance_id }}"
        name: UpdatedImage
      register: ami_output
    - name: Write AMI info to file
      lineinfile:
        create: yes
        path: /home/ansible/ami.txt
        line: "{{ ami_output.image_id }}"

# Run /home/ansible/updateAMI.yml to Perform the Required Tasks and Then Log into the AWS Console to Verify Your Work
Before we can run this playbook, we've got to set a few environment variables. We can do that by sourcing a file that's sitting in our home directory:

source keys.sh
Now run the following command: ansible-playbook /home/ansible/updateAMI.yml

Log into the AWS Console, and in the EC2 Dashboard (find it by searching for EC2 in the Find Services search box) confirm the new instance's existence and state.

It might be best to wait a bit before checking. Once everything is finished processing though, we'll see a Leo instance that's stopped, and a new one that is running.
We can also look down at AMIs (down in the Images section of the menu on the left side of the screen) and see our new UpdatedImage there.

# Create `/home/ansible/updateAMI.yml`, and Add an Ansible Play that Updates the Software on `node1` Then Updates `/home/ansible/image.txt`, as Described in the Instructions
In our solution, we'll complete the required steps and add a task for collecting facts from EC2 metadata, which will help us collect the instance ID of node1.

Edit the playbook such that it resembles the following:

- hosts: node1
  become: yes
  tasks:
    - name: Update packages
      yum:
        name: "*"
        state: latest
    - name: Update image.txt
      lineinfile:
        path: /home/ansible/image.txt
        line: "Image updated {{ ansible_date_time.date }}"
    - name: Gather facts
      ec2_metadata_facts:

# Edit the `/home/ansible/updateAMI.yml` and Add an Ansible Play that Collects the instance_id of `node1`, stops `node1`, Creates the AMI as Described in the Instructions, and Stores the AMI ID in the File `/home/ansible/ami.txt`
We will be using the facts we collected in the first play to satisfy the objectives for this task.

Edit the playbook such that it resembles the following:

- hosts: localhost
  tasks:
    - name: Stop node1
      local_action: ec2
      args:
        region: us-east-1
        state: stopped
        instance_id: "{{ hostvars['node1'].ansible_ec2_instance_id }}"
        wait: yes
    - name: Create AMI
      local_action: ec2_ami
      args:
        state: present
        instance_id: "{{ hostvars['node1'].ansible_ec2_instance_id }}"
        name: UpdatedImage
      register: ami_output
    - name: Write AMI info to file
      lineinfile:
        create: yes
        path: /home/ansible/ami.txt
        line: "{{ ami_output.image_id }}"

# Run `/home/ansible/updateAMI.yml` to Perform the Required Tasks and Then Log into the AWS Console to Verify Your Work
Change some environment variables:
source keys.sh

Run the playbook:
ansible-playbook /home/ansible/updateAMI.yml

Log into the AWS Console and confirm the new EC2 instance:
- Search for "EC2" in the AWS console search and select the EC2 dashboard.

Confirm the AMI was updated:
- Select AMIs from the menu on the left.

# Conclusion
We've done it. Using Ansible, we've automated the process of updating our AMIs. Congratulations!
