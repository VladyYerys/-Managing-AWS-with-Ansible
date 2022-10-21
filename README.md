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
