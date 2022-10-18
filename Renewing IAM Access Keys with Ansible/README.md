# Renewing IAM Access Keys with Ansible

![renewing-iam-access-keys-with-ansible](https://user-images.githubusercontent.com/106797604/196441846-c1bd4c5b-9ff1-4a74-9b7b-e15079fb2543.png)

# Introduction
Rotating AWS access keys is an important part of an overall security strategy. Ansible can help us manage this process. In this exercise, we will see how to work with AWS IAM user keys using Ansible.

# Additional Resources
Our boss heard about another company's AWS environment being compromised after IAM user credentials were stolen. In order to protect our company's AWS environment, our boss has tasked us with creating automation that updates IAM keys for a provided user. The company is heavily invested in Ansible, so we have been asked to create the automation using Ansible.

We have been provided an Ansible Control node and a sandbox AWS environment.

Start work on the Ansible Control node:

Create the playbook /home/ansible/keyUpdate.yml to perform the following tasks:
Look up the current IAM access key for the testuser IAM user.
Remove the old access id key and secret access key.
Create new keys for the testuser IAM user.
Write the new keys to /home/ansible/newkey.txt.
Run the playbook /home/ansible/keyUpdate.yml.
The Ansible control node has been configured and already has Ansible installed. The control node also has a system user named ansible configured with SSH access keys and necessary system privileges.

An IAM user ansible has been created on the provided AWS sandbox account. The access keys for the ansible IAM user are stored in /home/ansible/keys.sh and /home/ansible/keys.yml for whichever authentication method we prefer. The ansible IAM user has appropriate permissions to perform the required task.

The default Ansible inventory has been configured to include the Ansible control host as localhost.

# Instructions
Our boss heard about another company's AWS environment being compromised after IAM user credentials were stolen. In order to protect our company's AWS environment, our boss has tasked us with creating automation that updates IAM keys for a provided user. The company is heavily invested in Ansible, so we have been asked to create the automation using Ansible.

We have been provided an Ansible Control node and a sandbox AWS environment.

Start work on the Ansible Control node:

Create the playbook /home/ansible/keyUpdate.yml to perform the following tasks:
Look up the current IAM access key for the testuser IAM user.
Remove the old access id key and secret access key.
Create new keys for the testuser IAM user.
Write the new keys to /home/ansible/newkey.txt.
Run the playbook /home/ansible/keyUpdate.yml.
The Ansible control node has been configured and already has Ansible installed. The control node also has a system user named ansible configured with SSH access keys and necessary system privileges.

An IAM user ansible has been created on the provided AWS sandbox account. The access keys for the ansible IAM user are stored in /home/ansible/keys.sh and /home/ansible/keys.yml for whichever authentication method we prefer. The ansible IAM user has appropriate permissions to perform the required task.

The default Ansible inventory has been configured to include the Ansible control host as localhost.

# Logging In
Use the hands-on lab page to get the public IP of the cloud server we need to log into (making sure to use cloud_user as a username), then switch to the ansible user. The password for the two users is the same.

# Create /home/ansible/keyUpdate.yml and Add an Ansible Play that Removes the Old Access Keys for the IAM User testuser and Creates a New Set, Stored in /home/ansible/newkey.txt
Create the playbook and edit it such that it resembles the following:

- hosts: localhost
  gather_facts: no
  vars_files:
    - keys.yml
  tasks:
    - name: Get access key
      iam:
        aws_access_key: "{{ AWS_ACCESS_KEY_ID }}"
        aws_secret_key: "{{ AWS_SECRET_ACCESS_KEY }}"
        region: "{{ AWS_REGION}}"
        iam_type: user
        name: testuser
        state: present
      register: iam_info

    - name: Remove original key
      iam:
        aws_access_key: "{{ AWS_ACCESS_KEY_ID }}"
        aws_secret_key: "{{ AWS_SECRET_ACCESS_KEY }}"
        region: "{{ AWS_REGION}}"
        iam_type: user
        name: testuser
        state: update
        access_key_ids: "{{ iam_info.user_meta.access_keys[0].access_key_id }}"
        access_key_state: remove

    - name: Create new key
      iam:
        aws_access_key: "{{ AWS_ACCESS_KEY_ID }}"
        aws_secret_key: "{{ AWS_SECRET_ACCESS_KEY }}"
        region: "{{ AWS_REGION}}"
        iam_type: user
        name: testuser
        state: update
        access_key_state: create
      register: new_key

    - name: Store new access key information
      lineinfile:
        create: yes
        path: /home/ansible/newkey.txt
        mode: 0600
        line: "{{ new_key.created_keys[0].access_key_id }}"

    - name: Store new secret key information
      lineinfile:
        path: /home/ansible/newkey.txt
        line: "{{ new_key.created_keys[0].secret_access_key }}"

# Run the /home/ansible/keyUpdate.yml Playbook to Perform the Required Tasks
Run the following command:
ansible-playbook /home/ansible/keyUpdate.yml

# Conclusion
If all goes well, we should see a newkey.txt file in our home directory. Run cat newkey.txt to see that it in fact has new keys in it. We're done. Congratulations! home/ansible/newkey.txt mode: 0600 line: "{{ new_key.created_keys[0].access_key_id }}"

  - name: Store new secret key information
    lineinfile:
      path: /home/ansible/newkey.txt
      line: "{{ new_key.created_keys[0].secret_access_key }}"

## Run the `/home/ansible/keyUpdate.yml` Playbook to Perform the Required Tasks

- Run the following command:
- `ansible-playbook /home/ansible/keyUpdate.yml`

# 1.Create `/home/ansible/keyUpdate.yml` and Add an Ansible Play that Removes the Old Access Keys for the IAM User `testuser` and Creates a New Set, Stored in `/home/ansible/newkey.txt`

After logging into the EC2 instance, run su - ansible to become the ansible user. The password is the same as it is for cloud_user.

Create the playbook and edit it such that it resembles the following:

- hosts: localhost
  gather_facts: no
  vars_files:
    - keys.yml
  tasks:
    - name: Get access key
      iam:
        aws_access_key: "{{ AWS_ACCESS_KEY_ID }}"
        aws_secret_key: "{{ AWS_SECRET_ACCESS_KEY }}"
        region: "{{ AWS_REGION}}"
        iam_type: user
        name: testuser
        state: present
      register: iam_info

    - name: Remove original key
      iam:
        aws_access_key: "{{ AWS_ACCESS_KEY_ID }}"
        aws_secret_key: "{{ AWS_SECRET_ACCESS_KEY }}"
        region: "{{ AWS_REGION}}"
        iam_type: user
        name: testuser
        state: update
        access_key_ids: "{{ iam_info.user_meta.access_keys[0].access_key_id }}"
        access_key_state: remove

    - name: Create new key
      iam:
        aws_access_key: "{{ AWS_ACCESS_KEY_ID }}"
        aws_secret_key: "{{ AWS_SECRET_ACCESS_KEY }}"
        region: "{{ AWS_REGION}}"
        iam_type: user
        name: testuser
        state: update
        access_key_state: create
      register: new_key

    - name: Store new access key information
      lineinfile:
        create: yes
        path: /home/ansible/newkey.txt
        mode: 0600
        line: "{{ new_key.created_keys[0].access_key_id }}"

    - name: Store new secret key information
      lineinfile:
        path: /home/ansible/newkey.txt
        line: "{{ new_key.created_keys[0].secret_access_key }}"

# 2.Run the `/home/ansible/keyUpdate.yml` Playbook to Perform the Required Tasks

Run the following command:
ansible-playbook /home/ansible/keyUpdate.yml
