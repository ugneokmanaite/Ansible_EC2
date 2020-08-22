# Launching EC2 instance with Ansible

## Step 1: Create VM 
- Create a Virtual Machine
- In this case we have created an ansible VM

## Step 2: Vagrant up

`vagrant up ansible`

`vagrant ssh ansible`

## Step 3: Install Ansible and EC2 Module dependencies

```
sudo apt update

sudo apt install software-properties-common

sudo apt-add-repository --yes --update ppa:ansible/ansible

sudo apt install ansible
```

` sudo apt-get install tree`


` sudo apt install python`

`sudo apt install python-pip -y`

`pip install boto boto3`

## Step 4: Create SSH key to create the EC2 instance after provisioning
`ssh-keygen -t rsa -b 4096 -f ~/.ssh/my_aws`

## Step 5: Create the Ansible directory structure

```

mkdir -p AWS_Ansible/group_vars/all/
cd AWS_Ansible
touch playbook.yml
```

## Step 6: Create ansible vaut file to store the AWS Access and secret keys 
```
ansible-vault create group_vars/all/pass.yml New Vault password: Confirm New Vault password:
```

## Step 7: Click i in order to insert and enter the following:

```
ec2_access_key: <Your_Access_Key>                                     
ec2_secret_key: <Your_Secret_Key>
```

- to exit press ESC- SHIFT +: - W Q

## Step 8: Nano inside out playbook
 

```
- hosts: localhost
  connection: local
  gather_facts: False

  vars:
    key_name: my_aws
    region: eu-west-1
    image: ami-0347e7b47c4654570 # https://cloud-images.ubuntu.com/locator/ec2/
    id: "web-app"
    sec_group: "{{ id }}-sec"

  tasks:

    - name: Facts
      block:

      - name: Get instances facts
        ec2_instance_facts:
          aws_access_key: "{{ec2_access_key}}"
          aws_secret_key: "{{ec2_secret_key}}"
          region: "{{ region }}"
        register: result

      - name: Instances ID
        debug:
          msg: "ID: {{ item.instance_id }} - State: {{ item.state.name }} - Public DNS: {{ item.public_dns_name }}"
        loop: "{{ result.instances }}"

      tags: always


    - name: Provisioning EC2 instances
      block:

      - name: Upload public key to AWS
        ec2_key:
          name: "{{ key_name }}"
          key_material: "{{ lookup('file', '/home/vagrant/.ssh/my_aws.pub') }}"
          region: "{{ region }}"
          aws_access_key: "{{ec2_access_key}}"
          aws_secret_key: "{{ec2_secret_key}}"

      - name: Create security group
        ec2_group:
          name: "{{ sec_group }}"
          description: "Sec group for app {{ id }}"
          # vpc_id: 12345
          region: "{{ region }}"
          aws_access_key: "{{ec2_access_key}}"
          aws_secret_key: "{{ec2_secret_key}}"
          rules:
            - proto: tcp
              ports:
                - 22
              cidr_ip: 0.0.0.0/0
              rule_desc: allow all on ssh port
        register: result_sec_group

      - name: Provision instance(s)
        ec2:
          aws_access_key: "{{ec2_access_key}}"
          aws_secret_key: "{{ec2_secret_key}}"
          key_name: "{{ key_name }}"
          id: "{{ id }}"
          group_id: "{{ result_sec_group.group_id }}"
          image: "{{ image }}"
          instance_type: t2.micro
          region: "{{ region }}"
          wait: true
          count: 1
          # exact_count: 2
          # count_tag:
          #   Name: App
          # instance_tags:
          #   Name: App

      tags: ['never', 'create_ec2']

```



