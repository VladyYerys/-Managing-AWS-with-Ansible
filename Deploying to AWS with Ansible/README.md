# Deploying to AWS with Ansible


![deploying-to-aws-with-ansible](https://user-images.githubusercontent.com/106797604/196446681-790da984-b276-4c15-9a18-8adb6aa6434d.png)

# Introduction
This exercise provides a simple yet realistic task of deploying a basic website to dynamically provisioned AWS infrastructure. We will work with multiple AWS components through Ansible, and also perform basic web server configuration against a Linux host with Ansible. This exercise will help demonstrate a portion of the power provided by rolling cloud provisioning into deployment automation.

# Additional Resources
In an effort to get senior management on board with using AWS for web content, we have been asked to produce a quick proof of concept. We need to demonstrate how quickly we can stand up a new website using EC2 for compute and S3 for content. A colleague has started the work but taken ill. We can pick up where they left off.

We have been provided an Ansible Control node and a sandbox AWS environment.

We'll start our work on the Ansible Control node:

Run the provided playbook /home/ansible/get-environment-details.yml to complete another provided file, /home/ansible/env_vars.yml with environment-specific configuration information.
Edit /home/ansible/env_vars.yml and update the placeholder value for the BUCKET variable. It should be a unique S3 bucket name that meets the S3 naming restrictions.
Create the playbook /home/ansible/deploy.yml to perform the following tasks:
Using the provided SSH key for the ansible system user in /home/ansible/.ssh/id_rsa.pub, create a new AWS key pair named ansible_keypair.
Create a new EC2 instance that meets the following requirements:
Use the subnet, security group, and AMI defined in /home/ansible/env_vars.yml.
Use ansible_keypair, created in the initial task, as the instance login key pair.
Set the instance type to t2.micro.
The instance should be deployed with a public IP.
The instance should have a tag with the key type and value web.
We only need a single instance for the purpose of the proof of concept.
Create a new S3 bucket meeting the following requirements:
The bucket should be named after the BUCKET variable in /home/ansible/env_vars.yml.
The bucket should have public read permissions.
Upload the provided image /home/ansible/webimage.png to the new S3 bucket with an object name of /webimage.png
The playbook will need to configure the new EC2 instance as a web server in another play in the same playbook.
The following tasks must be performed on the EC2 instance after it is provisioned:
Install the httpd package using yum.
Start and enable the httpd package.
Deploy the provided template /home/ansible/index.html.j2 to /var/www/html/index.html. We will need to include the variable file /home/ansible/env_vars.yml in the play to successfully deploy the template.
Run the playbook /home/ansible/deploy.yml to build the environment.
Verify the work by loading http://<PUBLIC_IP_ADDRESS_OF_NEW_EC2_INSTANCE>/index.html in a web browser. If we did everything correctly, we should see a statement and an image in a browser.
The Ansible control node has been configured, and Ansible is installed. The control node also has a system user named ansible configured with SSH access keys and necessary system privileges.

An IAM user called ansible has been created on the provided AWS sandbox account. The access keys for the ansible IAM user are stored in /home/ansible/keys.sh and /home/ansible/keys.yml for whichever authentication method we prefer. The ansible IAM user has appropriate permissions to perform the required task.

The default Ansible inventory has been configured to include the Ansible control host as localhost.


# Solution
Log in to the provided lab server using the credentials provided:

ssh cloud_user@<PUBLIC_IP_ADDRESS>
Become the ansible user:

su - ansible
Note: When copying and pasting code into Vim from the lab guide, first enter :set paste (and then i to enter insert mode) to avoid adding unnecessary spaces and hashes.

# Run the Provided Playbook to Collect Necessary Environment Details
Run the following command:

ansible-playbook /home/ansible/get-environment-details.yml
This playbook populates our /home/ansible/env_vars.yml with the necessary values.

# Replace the placeholder in /home/ansible/env_vars.yml
Open /home/ansible/env_vars.yml:

vim /home/ansible/env_vars.yml
Change the value placeholder to a globally unique S3 bucket name of your choice.

Save and exit the file by pressing Escape followed by :wq.

# Create a Playbook and Add a Play
Create the playbook:

vim /home/ansible/deploy.yml
Edit it such that it resembles the following:

- hosts: localhost
  gather_facts: no
  vars_files:
    - /home/ansible/env_vars.yml
  tasks:
    - name: Create AWS key pair using Ansible's key.
      local_action: ec2_key
      args:
        region: "{{ AWS_REGION }}"
        name: ansible_keypair
        key_material: "{{ lookup('file', '/home/ansible/.ssh/id_rsa.pub') }}"

    - name: Provision instances
      local_action: ec2
      args:
        region: "{{ AWS_REGION }}"
        instance_type: t2.micro
        group: "{{ SEC_GROUP_NAME }}"
        keypair: ansible_keypair
        image: "{{ AMI_ID }}"
        assign_public_ip: yes
        vpc_subnet_id: "{{ DEFAULT_VPC_SUBNET }}"
        wait: yes
        instance_tags:
          type: web
        count: 1
      register: ec2

    - name: Add host to inventory
      add_host:
        hostname: "{{ item.public_ip }}"
        groupname: webservers
        ansible_ssh_common_args: "-o StrictHostKeyChecking=no"
        ansible_ssh_private_key_file: /home/ansible/.ssh/id_rsa
      loop: "{{ ec2.instances }}"

    - name: Create S3 Bucket
      local_action: aws_s3
      args:
        bucket: "{{ BUCKET_NAME }}"
        mode: create
        permission: public-read

    - name: Add file to bucket
      local_action: aws_s3
      args:
        bucket: "{{ BUCKET_NAME }}"
        mode: put
        object: /webimage.png
        src: /home/ansible/webimage.png
        permission: public-read

# Add Another Play to Further Configure the New EC2 Instance
Add another play to /home/ansible/deploy.yml that resembles the following:

- hosts: webservers
  gather_facts: no
  vars_files:
    - /home/ansible/env_vars.yml
  remote_user: ec2-user
  tasks:
    - name: Wait for SSH to come up
      wait_for_connection:
        delay: 5
        timeout: 90

    - name: Collect instance facts
      ec2_metadata_facts:

    - name: Install HTTPD
      become: yes
      yum:
        name: httpd
        state: present

    - name: Start HTTPD
      become: yes
      service:
        name: httpd
        state: started
        enabled: yes

    - name: Deploy content
      become: yes
      template:
        src: /home/ansible/index.html.j2
        dest: /var/www/html/index.html

# Run the Playbook
Source our keys.sh file:

source keys.sh
Run the following command:

ansible-playbook /home/ansible/deploy.yml
It may take about 5–10 minutes to finish.

# 1.Run the Provided Playbook `/home/ansible/get-environment-details.yml` to Collect Necessary Environment Details
After logging into the EC2 instance, become the ansible user:

su - ansible
The password is the same as it is for cloud_user.

Run the following command:

ansible-playbook /home/ansible/get-environment-details.yml

# 2.Replace the word "placeholder" in `/home/ansible/env_vars.yml` with a Unique S3 Bucket Name
Open /home/ansible/env_vars.yml with a text editor.
Change the value placeholder to a unique S3 bucket name of your choosing.
Be sure to stick to the S3 naming conventions (https://docs.aws.amazon.com/AmazonS3/latest/dev/BucketRestrictions.html for details).

# 3.Create a Playbook and Add a Play per the Lab Instructions
Create /home/ansible/deploy.yml
Add an Ansible play that configures your EC2 key pair, EC2 instance, and S3 bucket.
Use the provided variable file for required parameter configuration.

# 4.Add Another Play to Further Configure the New EC2 Instance
Add another play that will:
Configure the new EC2 instance.
Install the httpd package
Start and enable the httpd service.
Deploy the provided template file into /var/www/html.
Use /home/ansible/env_vars.yml for required parameter configuration values.

# 5.Run `/home/ansible/deploy.yml` to Perform the Required Tasks
Run the following command:

ansible-playbook /home/ansible/deploy.yml

# Conclusion
If the playbook ran with no errors, then we can fetch our instance's web content, using its public IP listed in the final output in the terminal, with:

curl <PUBLIC_IP_ADDRESS>/index.html
If all goes well, we've got a running web page. Congratulations!


