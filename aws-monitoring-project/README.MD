# AWS EC2 Monitoring with CloudWatch + SNS (Terraform + Manual Setup)

## Project Overview

This project demonstrates how to deploy an AWS EC2 instance using Terraform and set up monitoring using AWS CloudWatch alarms with SNS email notifications.

It combines:
- Infrastructure as Code (Terraform)
- AWS EC2 provisioning
- CloudWatch monitoring
- SNS email alerts

---

## Architecture

Terraform → EC2 Instance → CloudWatch Alarm → SNS Topic → Email Notification

---

## Technologies Used

- AWS EC2  
- AWS CloudWatch  
- AWS SNS (Simple Notification Service)  
- Terraform  
- Ubuntu / Amazon Linux  
- Git & GitHub  

---

## Features

- EC2 instance created using Terraform  
- Security group configured for SSH and HTTP  
- CloudWatch alarm monitors CPU usage  
- SNS sends email notifications  
- Manual AWS console setup for monitoring  

---

## Setup Instructions

### 1. Initialize Terraform
```bash
terraform init