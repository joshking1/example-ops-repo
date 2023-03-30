# example-ops-repo
To configure the environmental variables AWS_ACCESS_KEY_ID, AWS_SECRET_ACCESS_KEY, and AWS_REGION in Ansible, you can do the following:

Create a file named aws_credentials.yml in the same directory as your playbook, and add the following lines to the file:
vbnet

aws_access_key_id: <your-access-key-id>
aws_secret_access_key: <your-secret-access-key>
aws_region: <your-aws-region>

Replace <your-access-key-id>, <your-secret-access-key>, and <your-aws-region> with your actual AWS access key ID, secret access key, and region, respectively.

In your playbook, use the include_vars module to load the aws_credentials.yml file:

yaml

- name: Load AWS credentials
  include_vars:
    file: aws_credentials.yml
    
Then, you can use the loaded variables in your playbook by referencing them with {{ aws_access_key_id }}, {{ aws_secret_access_key }}, and {{ aws_region }}, respectively:
yaml

- name: Example task using AWS credentials
  ec2_instance:
    instance_type: t2.micro
    image: ami-0c94855ba95c71c99
    region: "{{ aws_region }}"
  environment:
    AWS_ACCESS_KEY_ID: "{{ aws_access_key_id }}"
    AWS_SECRET_ACCESS_KEY: "{{ aws_secret_access_key }}"
    AWS_REGION: "{{ aws_region }}"
    
This will configure the environmental variables for the tasks that require AWS authentication.

Create a playbook to create aws ec2 and use include_vars
Sure, here's an example playbook that creates an EC2 instance in AWS and uses include_vars to load AWS credentials:
yaml

---
- name: Create EC2 instance
  hosts: localhost
  connection: local
  gather_facts: no

  vars:
    instance_name: my-ec2-instance
    instance_type: t2.micro
    ami_id: ami-0c94855ba95c71c99
    subnet_id: subnet-12345678
    security_group_id: sg-12345678

  tasks:
    - name: Load AWS credentials
      include_vars:
        file: aws_credentials.yml

    - name: Create EC2 instance
      ec2:
        key_name: my-key-pair
        instance_type: "{{ instance_type }}"
        image: "{{ ami_id }}"
        vpc_subnet_id: "{{ subnet_id }}"
        assign_public_ip: yes
        security_group_ids: "{{ security_group_id }}"
        instance_tags:
          Name: "{{ instance_name }}"
      register: ec2_instance

    - name: Show instance IP address
      debug:
        var: ec2_instance.instances[0].public_ip_address

In this example playbook:
The instance_name, instance_type, ami_id, subnet_id, and security_group_id variables are set to the desired values for the EC2 instance.
The include_vars module is used to load AWS credentials from a file named aws_credentials.yml.
The ec2 module is used to create the EC2 instance, with the specified instance type, AMI ID, subnet ID, security group ID, and instance tags.
The register keyword is used to store the results of the ec2 task in a variable named ec2_instance.
The debug module is used to display the public IP address of the created EC2 instance.
Note that in this example playbook, the hosts parameter is set to localhost and the connection parameter is set to local. This means that the playbook is executed locally, rather than on a remote host. You can modify these parameters to suit your needs, depending on whether you want to run the playbook locally or on a remote host.
