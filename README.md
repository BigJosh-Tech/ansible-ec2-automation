Ansible EC2 Automation Project
End-to-end infrastructure automation using Ansible on AWS — covering EC2 provisioning, passwordless SSH authentication, and conditional instance management.

Project Structure
ansible-project/
├── ansible.cfg
├── inventory.ini
├── vault.pass
├── group_vars/
│   └── all.yml
├── ec2_create.yaml
├── ec2_passwordless.yaml
└── ec2_stop.yaml

What This Project Does
- ec2_create.yaml - Creates 3 EC2 instances using Ansible loops
- ec2_passwordless.yaml - Sets up passwordless SSH on all instances
- ec2_stop.yaml -   Shuts down Ubuntu instances only using conditionals

Prerequisites

WSL (Ubuntu) or any Linux machine
AWS account with IAM credentials
Ansible, boto3, botocore installed
amazon.aws collection installed
An existing AWS key pair (.pem file)

Quick Start

1. Clone the Repository
git clone https://github.com/yourusername/ansible-ec2-automation.git
cd ansible-ec2-automation

2. Install dependencies
sudo apt update && sudo apt install ansible python3-pip -y
pip3 install boto3 botocore --break-system-packages
ansible-galaxy collection install amazon.aws

3. Set up vault credentials
echo "yourpassword" > vault.pass
chmod 600 vault.pass
ansible-vault create group_vars/all.yml

4. Run the playbooks in order
# Create instances
ansible-playbook ec2_create.yaml

# Setup passwordless auth (one time only)
ansible-playbook ec2_passwordless.yaml --private-key ~/.ssh/yourkey.pem

# Verify connection
ansible all -m ping

# Shutdown Ubuntu instances only
ansible-playbook ec2_stop.yaml

Key Concepts Demonstrated

- Ansible Loops — provision multiple EC2 instances in a single task
- Ansible Vault — encrypt AWS credentials, never hardcode secrets
- Idempotency — playbooks are safe to run multiple times
- gather_facts + when — conditionally target Ubuntu instances only
- authorized_key module — passwordless SSH across all nodes

Security Notes

- Never commit vault.pass to Git — add it to .gitignore
- Never commit your .pem file to Git
- Always encrypt sensitive credentials using Ansible Vault

Add this to your .gitignore:
vault.pass
*.pem
group_vars/all.yml

Full Walkthrough
Read the full step by step guide on Medium 👉 [https://medium.com/@onijoshua044]

Author
Joshua

LinkedIn: [linkedin.com/in/joshua-oni-oluwafemi]
GitHub: [https://github.com/BigJosh-Tech]

Then push to GitHub:
git init
git add .
git commit -m "Initial commit - Ansible EC2 automation project"
git remote add origin https://github.com/yourusername/ansible-ec2-automation.git
git push -u origin main
