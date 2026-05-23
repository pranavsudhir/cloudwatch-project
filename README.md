# 🚀 AWS Cloud Monitoring with Terraform, CloudWatch & SNS

A complete DevOps monitoring project that provisions an EC2 instance using Terraform, monitors CPU utilization via CloudWatch, and sends email alerts through SNS.

---

## 📐 Architecture
Terraform → EC2 Instance → CloudWatch Alarm → SNS Topic → Email Alert
---

## 🛠️ Prerequisites

| Tool | Purpose |
|------|---------|
| AWS Account | Cloud provider |
| Terraform | Infrastructure provisioning |
| Git | Version control |
| AWS CLI | Credential configuration |
| SSH key (.pem) | EC2 access |

---

## 📁 Project Structure
aws-project/
├── main.tf               # EC2 + Security Group
├── variables.tf          # Input variable declarations
├── terraform.tfvars      # Actual values (gitignored)
└── .gitignore
---

## ⚙️ Setup & Deployment

### Step 1 — Configure AWS credentials

```bash
aws configure
```

Enter your:
- AWS Access Key ID
- AWS Secret Access Key
- Region: `ap-south-1`
- Output format: `json`

---

### Step 2 — Create project folder

```bash
mkdir aws-project
cd aws-project
```

---

### Step 3 — Terraform files

**`variables.tf`**
```hcl
variable "ami_id" {
  description = "AMI ID for the EC2 instance"
  type        = string
}

variable "key_name" {
  description = "Name of the SSH key pair"
  type        = string
}

variable "ssh_cidr" {
  description = "CIDR block allowed to SSH into the instance"
  type        = string
}
```

**`main.tf`**
```hcl
provider "aws" {
  region = "ap-south-1"
}

resource "aws_security_group" "web_sg" {
  name = "cloudwatch-sg"

  ingress {
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = [var.ssh_cidr]
  }

  ingress {
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
}

resource "aws_instance" "web" {
  ami                    = var.ami_id
  instance_type          = "t3.micro"
  key_name               = var.key_name
  vpc_security_group_ids = [aws_security_group.web_sg.id]

  tags = {
    Name = "CloudWatch-EC2"
  }
}
```

**`terraform.tfvars`** ← never commit this file
```hcl
ami_id   = "ami-0f58b397bc5c1f2e8"
key_name = "your-key-name"
ssh_cidr = "YOUR_IP/32"
```

**`.gitignore`**
---

### Step 4 — Deploy infrastructure

```bash
terraform init
terraform apply
```

Type `yes` when prompted.

---

### Step 5 — SSH into EC2

```bash
# Ubuntu AMI
ssh -i your-key.pem ubuntu@<public-ip>

# Amazon Linux AMI
ssh -i your-key.pem ec2-user@<public-ip>
```

Get the public IP from: **AWS Console → EC2 → Instances → Public IPv4**

---

### Step 6 — Install stress tool (inside EC2)

```bash
sudo apt update
sudo apt install stress -y
```

---

## 📢 CloudWatch + SNS Alert Setup

### Create SNS Topic

1. Go to **AWS Console → SNS → Topics → Create topic**
2. Name: `ec2-alert`
3. Add subscription:
   - Protocol: `Email`
   - Endpoint: your email address
4. Check your inbox and click **Confirm subscription**

### Create CloudWatch Alarm

1. Go to **AWS Console → CloudWatch → Alarms → Create alarm**
2. Select metric: **EC2 → Per-Instance Metrics → CPUUtilization**
3. Choose your instance
4. Set threshold: **CPU > 70%** for **1 minute**
5. Attach SNS topic: `ec2-alert`

---

## 🧪 Testing

Run this inside the EC2 instance to spike CPU:

```bash
stress --cpu 2 --timeout 300
```

### Expected result

| Check | Status |
|-------|--------|
| CPU spikes to ~100% | ✅ |
| CloudWatch alarm → ALARM state | ✅ |
| SNS notification triggered | ✅ |
| Email alert received | ✅ |

---


---

## 📄 License

MIT
