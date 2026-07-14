Terraform Apply
│
├── Read Data Sources
│   ├── VPC ID
│   ├── Private Subnets
│   ├── Backend SG
│   ├── ALB Listener ARN
│   └── Latest Base AMI
│
├── Generate RSA Key
│
├── Upload Public Key to AWS
│
├── Launch Builder EC2
│   ├── Create ENI
│   ├── Attach Security Group
│   ├── Create 40GB gp3 Root EBS
│   ├── Attach Key Pair
│   └── Boot Linux
│
├── SSH into Builder
│   ├── Copy backend.sh
│   ├── Execute Script
│   ├── Install Packages
│   ├── Clone Code
│   ├── Build Application
│   ├── Configure Services
│   └── Backend Ready
│
├── Stop Builder Instance
│
├── Create AMI
│   └── Snapshot Root Disk
│
├── Terminate Builder Instance
│
├── Create Target Group
│
├── Create Launch Template
│   ├── Golden AMI
│   ├── Instance Type
│   ├── Security Group
│   └── Tags
│
├── Create Auto Scaling Group
│   ├── Desired = 1
│   ├── Launch Instance
│   ├── Register Target Group
│   └── Health Check
│
├── Attach Scaling Policy
│   └── CPU Target = 10%
│
└── Create ALB Listener Rule
    │
    ▼
Client
   │
   ▼
ALB
   │
   ▼
Listener Rule
   │
   ▼
Target Group
   │
   ▼
Healthy EC2 (from ASG)
   │
   ▼
Backend Application (Port 8080)
