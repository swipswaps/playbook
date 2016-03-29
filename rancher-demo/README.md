# rancher-demo playbook

This playbook builds 3 instances: A NAT instance, a public Rancher instance, and a private Rancher instance..

There are two playbooks:  ```bastion_host.yml``` and ```bastion_host_advanced.yml```. The advanced script regenerates the SSH fingerprint for each host.

Example run:

```
ansible-playbook rancher-demo.yml
```

# ansible-vault

The Ansible vault file is kept under ```host_vars/127.0.0.1/vault.yml```.  It has the following structure:

```
vault:
  admin_user: username
  ami_ssh_fingerprint: "ecdsa-sha2-nistp256 blahblahblahblahfingerprint" # AMI fingerprint.  Needs to be manually retreived ahead of time.
  aws_secret_key: SECRET_KEY
  aws_access_key: ACCESS_KEY
  ip: 'your_remote_ip_address' # where you are connecting from
  key_pair ssh_private_key_name # needs uploaded to AWS key pair
  region: us-west-2 # or east or whereever
  ssh_users: |
    list of ssh public keys for users you want to provide access to
  # SSH key infoconfiguration
  ansible_ssh_key_file: "{{ ansible_env.HOME }}/.ssh/ssh_private_key_name"
  ansible_ssh_key_contents: |
    -----BEGIN RSA PRIVATE KEY-----
    KEYDATA
    -----END RSA PRIVATE KEY-----
```

Embedding the SSH key in the Ansible Vault keeps the entire project very modular.


# AWS policy

The following is the AWS policy used to run this playbook.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "ec2:DescribeInstances",
                "ec2:DescribeImages",
                "ec2:DescribeKeyPairs",
                "ec2:DescribeSecurityGroups",
                "ec2:DescribeAvailabilityZones",
                "ec2:RunInstances",
                "ec2:TerminateInstances",
                "ec2:CreateImage",
                "ec2:CreateTags",
                "ec2:StopInstances",
                "ec2:StartInstances"
            ],
            "Resource": "*"
        }
    ]
}
```