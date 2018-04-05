## Packer demo on AWS with Ansible as Provisioner

This example shows how to use Packer to pre-bake AWS AMI with your desired configuration.

Ansible is used as provisioner to configure the image.

This setup uses *Amazon Linux* operating system.

Basic operations workflow:
- launch a temporary EC2 instance
- copy **scripts** and **ansible** folders to an instance
- install Ansible
- use Ansible to create a user and to install packages  
- uninstall Ansible
- delete copied folders

## Installation

Requires [Packer](https://www.packer.io) and optionally AWS CLI for managing credentials.

```
git clone https://github.com/dmitrijsf/packer-ansible-aws-demo.git
cd packer-ansible-aws-demo
```

## Usage

**Copy your public key to ansible/files**

Note that public key name shoud be *id_rsa.pub* or Ansible *group_vars* will need to be adjusted.

```
❯ ssh-keygen
Generating public/private rsa key pair.
Enter file in which to save the key (/Users/username/.ssh/id_rsa): keys/id_rsa
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in keys/id_rsa.
Your public key has been saved in keys/id_rsa.pub.

❯ cp keys/id_rsa.pub ansible/files
```

**Set desired AWS credentials**

In this example I am using [**aws-vault**](https://github.com/99designs/aws-vault) to work with desired profile.

Other options to set [AWS credentials for Packer](https://www.packer.io/docs/builders/amazon.html).

```
❯ aws-vault add home
Enter Access Key ID: your-aws-access-key-id
Enter Secret Access Key: your-aws-access-key+
Added credentials to profile "home" in vault

# launches subshell with desired AWS environment variables
❯ aws-vault --debug exec -- home
```

**Validate and Build with Packer**

```
❯ packer -v
1.1.3

❯ packer validate template_ami.json
Template validated successfully.

❯ packer build template_ami.json
amazon-ebs output will be in this color.

==> amazon-ebs: Prevalidating AMI Name: packer-ami 1517899734
    amazon-ebs: Found Image ID: ami-d834aba1
    -- cut --
==> Builds finished. The artifacts of successful builds are:
--> amazon-ebs: AMIs were created:
eu-west-1: ami-35472e4c
```

**Launch EC2 instance**

Now you can launch an EC2 instance from the new AMI.

Creating a key pair in AWS is not required as we have set it up with ansible.

```
❯ ssh -i keys/id_rsa manager@ec2-34-245-80-175.eu-west-1.compute.amazonaws.com
-- cut --
Enter passphrase for key 'keys/id_rsa':

       __|  __|_  )
       _|  (     /   Amazon Linux AMI
      ___|\___|___|

https://aws.amazon.com/amazon-linux-ami/2017.09-release-notes/
[manager@ip-172-31-47-55 ~]$
```
