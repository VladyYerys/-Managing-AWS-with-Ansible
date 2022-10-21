# Ansible Configuration

![Instal BOTO3](https://user-images.githubusercontent.com/106797604/197080954-504a70fd-8036-476a-9a6f-02259f917ceb.png)

### User data:
![Screenshot_1](https://user-images.githubusercontent.com/106797604/197081545-fb30aa69-5277-475f-aebe-9a6efbddc692.png)

- #!/bin/bash
- sudo yum update -y
- sudo yum install epel-release
- sudo amazon-linux-extras install ansible2 -y
- sudo yum install -y python-boto python-boto3

### Inventory Considerations

![Screenshot_2](https://user-images.githubusercontent.com/106797604/197082921-eb2caf98-ee10-4de6-bba2-12011620d296.png)

### amazon.aws.aws_ec2 inventory – EC2 inventory source
![Screenshot_3](https://user-images.githubusercontent.com/106797604/197083388-121b2429-ab67-4873-8c7e-a34cee43e608.png)


# Accessing the AWS Console with Ansible
![Screenshot_4](https://user-images.githubusercontent.com/106797604/197083882-82669d14-36c1-4b75-89b4-3a479664752e.png)

### Working with `ssh-agent` 
![Screenshot_5](https://user-images.githubusercontent.com/106797604/197177012-fdccd23e-c1dd-455d-883f-f351ee2d9144.png)
![Screenshot_6](https://user-images.githubusercontent.com/106797604/197182613-059b4982-d855-43a9-91b4-b839dea54ba5.png)


### Understanding AWS Console Access 
![Screenshot_7](https://user-images.githubusercontent.com/106797604/197183448-85cd080c-ec72-4715-8e2e-0dcbb4ef8244.png)

![Screenshot_8](https://user-images.githubusercontent.com/106797604/197185502-a0c17a98-ed6f-4bd2-b607-fce845fa44e7.png)


### Configuring IAM Users for Ansble
![Screenshot_9](https://user-images.githubusercontent.com/106797604/197185891-f7855df4-63f2-424d-8c29-a99f52d0da29.png)
![Screenshot_10](https://user-images.githubusercontent.com/106797604/197187603-a88b46f1-a3c8-4403-bb52-6509b9c3c29b.png)


### Configuring IAM Access Keys
![Screenshot_11](https://user-images.githubusercontent.com/106797604/197187816-69e6596a-6bb8-4812-b193-2a18e7493f7c.png)

![Screenshot_12](https://user-images.githubusercontent.com/106797604/197191722-544c7f39-cf89-4cc2-b47e-f1579d1a43d6.png)

Create a very simple playbook name is ec2-change-state.yml
add:
```
---
- hosts: all
  gather_facts: yes
  vars_files:
    - keys.yml
  tasks:
    - name: Change instances state by tag
      ec2:
        aws_access_key: "{{ AWS_ACCESS_KEY_ID }}"
        aws_secret_key: "{{ AWS_SECRET_ACCESS_KEY }}"
        ec2_region: "{{ AWS_REGION }}"
        state: stopped
        intance tags:
           Name: Linux1
```

![Screenshot_13](https://user-images.githubusercontent.com/106797604/197193293-021bf9f6-62d9-4ff6-92bb-7e184596039e.png)

![Screenshot_14](https://user-images.githubusercontent.com/106797604/197204853-3b0dacb2-f02c-44ba-9f63-12469e482e72.png)



