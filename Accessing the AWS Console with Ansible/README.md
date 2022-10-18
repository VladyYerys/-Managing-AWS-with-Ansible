# Accessing the AWS Console with Ansible

![Accessing-the-AWS-console-withAnsible](https://user-images.githubusercontent.com/106797604/196348011-ebd2cbb9-c639-4223-9cb0-5b424d8ef269.png)


# Introduction
If our goal is configuring AWS using Ansible, the first thing that we need to do is configure our Ansible control node for the job, and provide appropriate credentials. In this hands-on lab, we will configure a new IAM user in the AWS console to allow Ansible to connect to the console programmatically. The credentials need to be protected, so they will be supplied by means of an encrypted Ansible vault.

We have been tasked with verifying Ansible connectivity to our company's fledgling AWS account. We need to create a dedicated IAM user for interacting with Ansible. We must also configure our Ansible control server to work with the AWS console, by installing necessary software packages and credential files. Once the configurations are completed, we can test connectivity with a provided playbook.

# Using the AWS console:
- Create a new IAM user called ansible with a pair of keys for programmatic access.
- Assign the Ansible user the role AmazonEC2ReadOnlyAccess to allow EC2 fact access.

# Working on the Ansible Control node:
- Install necessary boto and boto3 packages on the Ansible control node.
- Configure an Ansible Vault with the access ID key, secret key, and AWS region.
- Run the provided playbook to validate that the ansible user and vault are correctly configured.

The Ansible control node has been configured for us, and Ansible is already installed there. The control node also has a system user named ansible configured with SSH access keys and all of the necessary system privileges. The default inventory has been configured to include the Ansible control host as localhost.

# The Scenario
We have been tasked with verifying Ansible connectivity to our company's fledgling AWS account. We need to create a dedicated IAM user for interacting with Ansible. We must also configure our Ansible control server to work with the AWS console, by installing necessary software packages and credential files. Once the configurations are completed, we can test connectivity with a provided playbook.

Using the AWS console:

Create a new IAM user called ansible with a pair of keys for programmatic access.
Assign the Ansible user the role AmazonEC2ReadOnlyAccess to allow EC2 fact access.
Working on the Ansible Control node:

Install necessary boto and boto3 packages on the Ansible control node.
Configure an Ansible Vault with the access ID key, secret key, and AWS region.
Run the provided playbook to validate that the ansible user and vault are correctly configured.
The Ansible control node has been configured for us, and Ansible is already installed there. The control node also has a system user named ansible configured with SSH access keys and all of the necessary system privileges. The default inventory has been configured to include the Ansible control host as localhost.

# Create a User
We've got to create a New IAM User Called ansible with Programmatic Access Keys and the AmazonEC2ReadOnlyAccess Role.

Log into the AWS console using the provided AWS URL and cloud_user account.
Search for IAM in the Find Services search box, and select the IAM that shows up in the pop up box.
Select Users in the left menu.
Click Add User at the top of the page.
Provide the username ansible and check the box next to Programmatic access for access type.
Click Next: Permissions.
Select Attach existing policies directly and search for AmazonEC2ReadOnlyAccess using the filter policies search box.
Check the box next to AmazonEC2ReadOnlyAccess.
Click Next: Tags, then Next: Review, and lastly, after ensuring your configurations are correct, click Create user.
Click Show under Secret access key to reveal the secret access key for the ansible user.
Important! Copy the Access key ID and Secret access key to a place where you may access them later, like a text file.

# Edit keys.yml and Encrypt It
In the terminal, where we are already logged into the control node as cloud_user become the ansible user with a quick sudo -i -u ansible. Remember, this password is the same as the cloud_user one.

The /home/ansible/keys.yml file needs editing. We have to swap the place holder data with the ansible IAM user's access key, secret key, and appropriate AWS region. Then we've got to encrypt the file using ansible-vault. The vault should use the password "I love ansible".

Log into the Ansible control node as the ansible user.

Open /home/ansible/keys.yml using a text editor such as Vim, and replace each place holder with the appropriate value.
We may want to check in the AWS EC2 dashboard and make sure we get the right availability zone. It should be us-east-1. Disregard the letter that appears after that much of the AZ when entering it into the yml file
Run ansible-vault encrypt /home/ansible/keys.yml using the password "I love ansible".

# Installing Boto
Next, we've got to install the necessary boto and boto3 packages on the Ansible control node. We should still be the ansible user, so run this to install the boto packages on the Ansible control node: - sudo yum install -y python-boto python-boto3

# Test by Running a Playbook
Run the following command as the ansible user:
ansible-playbook --ask-vault-pass /home/ansible/test-aws-connection.yml

# Conclusion
The fact that our test playbook outputs all of that data about our EC2 instances means that we have in fact gotten Ansible talking to AWS. Our user was configured properly, and that we provided our keys correctly, using our Ansible vault. Congratulations!

# Create a new IAM user called `ansible` with programmatic access keys and the *AmazonEC2ReadOnlyAccess* role.
Log into the AWS console using the provided AWS URL and cloud_user account.
Search for IAM in the Find Services search box, and select the IAM that shows up in the pop up box.
Select Users in the left menu.
Click Add User at the top of the page.
Provide the username ansible and check the box next to Programmatic access for access type.
Click Next: Permissions.
Select Attach existing policies directly and search for AmazonEC2ReadOnlyAccess using the filter policies search box.
Check the box next to AmazonEC2ReadOnlyAccess.
Click Next: Tags, then Next: Review, and lastly, after ensuring your configurations are correct, click Create user.
Click Show under Secret access key to reveal the secret access key for the ansible user.
Important! Copy the Access key ID and Secret access key to a place where you may access them later, like a text file.

# Edit the file `/home/ansible/keys.yml` and replace the place holders with the ansible IAM user's access key, secret key, and appropriate AWS region. Then encrypt the file using `ansible-vault`. The vault should use the password "I love ansible".
Log into the Ansible control node as the ansible user.

Open /home/ansible/keys.yml using a text editor such as Vim, and replace each place holder with the appropriate value.
Run ansible-vault encrypt /home/ansible/keys.yml using the password "I love ansible".

# Install the necessary boto and boto3 packages on the Ansible control node.
Log into the Ansible control node as the ansible user.
Run the following command to install the boto packages on the Ansible control node:
sudo yum install -y python-boto python-boto3

# Run the provided playbook to validate that your `ansible` user and vault are correctly configured.

Log into the Ansible control node as the ansible user.
Run the following command:
ansible-playbook --ask-vault-pass /home/ansible/test-aws-connection.yml
