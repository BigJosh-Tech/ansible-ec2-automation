Ansible EC2 Automation Project
A hands-on Ansible project demonstrating configuration management and infrastructure automation on AWS. This project covers EC2 instance provisioning, passwordless SSH authentication, and conditional instance management using core Ansible concepts.

Project Overview
TaskTool/Concept UsedCreate 3 EC2 instancesAnsible LoopsSecure AWS credentialsAnsible VaultPasswordless SSH setupauthorized_key moduleShutdown Ubuntu onlygather_facts + when condition

Prerequisites

WSL (Ubuntu on Windows) or any Linux machine
AWS account with IAM user credentials
Ansible installed
Python3, boto3, botocore installed
amazon.aws Ansible collection
An existing AWS key pair (.pem file)


Project Structure
ansible-project/
├── ansible.cfg                  # Project level Ansible configuration
├── inventory.ini                # Hosts grouped by OS
├── vault.pass                   # Vault password file (never commit this)
├── group_vars/
│   └── all.yml                  # Encrypted AWS credentials
├── ec2_create.yaml              # Playbook to create EC2 instances
├── ec2_passwordless.yaml        # Playbook to setup passwordless auth
└── ec2_stop.yaml                # Playbook to shutdown Ubuntu instances only

Setup Instructions
1. Clone the Repository
git clone https://github.com/yourusername/ansible-ec2-automation.git
cd ansible-ec2-automation
2. Install Dependencies
sudo apt update
sudo apt install ansible python3-pip -y
pip3 install boto3 botocore --break-system-packages
ansible-galaxy collection install amazon.aws
3. Create Vault Password File
echo "yourpassword" > vault.pass
chmod 600 vault.pass
4. Create Encrypted AWS Credentials
ansible-vault create group_vars/all.yml
Add inside:
ec2_access_key: "YOUR_AWS_ACCESS_KEY"
ec2_secret_key: "YOUR_AWS_SECRET_KEY"
5. Update ansible.cfg
[defaults]
inventory = inventory.ini
host_key_checking = False
private_key_file = ~/.ssh/id_rsa
remote_user = ubuntu
vault_password_file = vault.pass
deprecation_warnings = False

Usage
Step 1 — Create EC2 Instances
ansible-playbook ec2_create.yaml
This creates:

1 Amazon Linux instance (manage-node-1)
2 Ubuntu instances (manage-node-2, manage-node-3)

Step 2 — Update Inventory
Go to AWS Console → EC2 → Instances, grab the public IPs and update inventory.ini:
[ubuntu]
<ubuntu-instance-1-ip>
<ubuntu-instance-2-ip>

[amazon]
<amazon-linux-ip>

[ubuntu:vars]
ansible_user=ubuntu
ansible_ssh_private_key_file=~/.ssh/id_rsa

[amazon:vars]
ansible_user=ec2-user
ansible_ssh_private_key_file=~/.ssh/id_rsa

Step 3 — Setup Passwordless Authentication
Run once with your .pem file:
ansible-playbook ec2_passwordless.yaml --private-key ~/.ssh/yourkey.pem

Verify passwordless works:
ansible all -m ping
Expected output:
<ip-1> | SUCCESS => {"ping": "pong"}
<ip-2> | SUCCESS => {"ping": "pong"}
<ip-3> | SUCCESS => {"ping": "pong"}

Step 4 — Shutdown Ubuntu Instances Only
ansible-playbook ec2_stop.yaml
Expected output:
Amazon Linux instance  → skipping
Ubuntu instance 1      → changed (shutdown)
Ubuntu instance 2      → changed (shutdown)

Key Concepts Explained
Ansible Loops: Used in ec2_create.yaml to create multiple EC2 instances with a single task instead of repeating code.
Ansible Vault: AWS credentials are encrypted using Ansible Vault so sensitive data is never hardcoded or exposed in the repository.
Idempotency: Running ec2_passwordless.yaml multiple times returns ok instead of changed when the key already exists — this is idempotency in action.
gather_facts + when condition: gather_facts: true collects system information like OS distribution. The when condition uses ansible_facts['distribution'] to target Ubuntu instances only for shutdown.
authorized_key module: Pushes the control node's public key to all managed instances, enabling passwordless SSH authentication.

Security Notes

Never commit vault.pass to Git — add it to .gitignore
Never commit your .pem file to Git
Always encrypt sensitive credentials using Ansible Vault

Add this to your .gitignore:
vault.pass
*.pem
group_vars/all.yml

Author
Joshua

Medium: [https://medium.com/@onijoshua044]
LinkedIn: [linkedin.com/in/joshua-oni-oluwafemi]
GitHub: [https://github.com/BigJosh-Tech]

Then push to GitHub:
git init
git add .
git commit -m "Initial commit - Ansible EC2 automation project"
git remote add origin https://github.com/yourusername/ansible-ec2-automation.git
git push -u origin main
