# Ansible Automation Platform - AWS Deployment

Automated deployment of Ansible Automation Platform (AAP) on AWS infrastructure using Ansible playbooks.

## Quick Start

```bash
# 1. Configure credentials
cp env-vars.sample env-vars.sh
vim env-vars.sh                    # Update Red Hat registry credentials
source env-vars.sh

# 2. Install dependencies
ansible-galaxy collection install -r requirements.yml

# 3. Add AAP setup bundle
# Place your AAP bundle in files/ directory:
# files/ansible-automation-platform-containerized-setup-bundle-*.tar.gz

# 4. Deploy everything
ansible-playbook playbooks/deploy-aap.yml
```

## Prerequisites

- Python 3.8+ with Ansible Core 2.14+
- AWS CLI configured with credentials
- AWS Access Key ID and Secret Access Key (with EC2, VPC, and IAM permissions)
- AWS Session Token (if using temporary credentials)
- Red Hat subscription with registry access
- AAP containerized setup bundle

## Project Structure

```
aws-aap-container/
├── env-vars.sh                      # Environment configuration
├── playbooks/
│   ├── deploy-aap.yml              # Complete deployment
│   ├── aws/
│   │   ├── create_infrastructure.yml
│   │   └── teardown_infrastructure.yml
│   └── aap/
│       ├── install.yml
│       ├── pre-install.yml
│       └── post-install.yml
└── files/                          # Place AAP bundle here
```

## Deployment Options

### Option 1: Single Command (Recommended)
```bash
ansible-playbook playbooks/deploy-aap.yml
```

### Option 2: Step by Step
```bash
# Create AWS infrastructure
ansible-playbook playbooks/aws/create_infrastructure.yml

# Install AAP
ansible-playbook playbooks/aap/install.yml
```

### Option 3: Individual Components
```bash
# AWS infrastructure only
ansible-playbook playbooks/aws/create_infrastructure.yml

# AAP components individually
ansible-playbook playbooks/aap/pre-install.yml
ansible-playbook playbooks/aap/install.yml
ansible-playbook playbooks/aap/post-install.yml
```

## Configuration

Edit `env-vars.sh` for basic settings:

```bash
# Required - Red Hat registry credentials
export INSTALLER_REGISTRY_USERNAME="your-username"
export INSTALLER_REGISTRY_PASSWORD="your-password"

# Optional - AWS settings
export AWS_REGION="us-east-1"
export AWS_INSTANCE_TYPE="t3.xlarge"
export INSTANCE_NAME="aap-containerized"

# Optional - AAP settings
export INSTALLER_ADMIN_PW="ansible123"
export AAP_INCLUDE_CONTROLLER="true"
export AAP_INCLUDE_EDA_CONTROLLER="false"
export AAP_INCLUDE_AUTOMATION_HUB="false"
```

## Access

After deployment:
- **Web Interface:** `https://<public-ip>`
- **Username:** `admin`
- **Password:** Value from `INSTALLER_ADMIN_PW`

Public IP address is displayed in the completion message.

## Cleanup

```bash
ansible-playbook playbooks/aws/teardown_infrastructure.yml
```

## Troubleshooting

**Bundle not found:**
```bash
ls files/ansible-automation-platform-containerized-setup-bundle-*.tar.gz
```

**Registry authentication:**
```bash
podman login registry.redhat.io
```

**AWS permissions:**
```bash
aws sts get-caller-identity
```

**Installation logs:**
```bash
ssh -i files/<instance-name>-key.pem ec2-user@<public-ip>
sudo podman ps -a
``` 