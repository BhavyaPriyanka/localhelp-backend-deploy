# Complete Runtime Flow

```text
Terraform Apply
│
├── Read Data Sources
│   ├── VPC ID
│   ├── Private Subnets
│   ├── Backend SG
│   ├── ALB Listener ARN
│   └── Latest Base AMI
│
├── Generate RSA Key Pair
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
│   ├── Upload backend.sh
│   ├── Execute Script
│   ├── Install Packages
│   ├── Clone Repository
│   ├── Build Application
│   ├── Configure Services
│   └── Backend Ready
│
├── Stop Builder Instance
│
├── Create EBS Snapshot
│
├── Register AMI
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
Application Load Balancer
    │
    ▼
Listener Rule
    │
    ▼
Target Group
    │
    ▼
Healthy EC2 Instance
    │
    ▼
Backend Application (Port 8080)
```

# Final Architecture

```
Terraform
    │
    ▼
Builder EC2
    │
    ├── Install Software
    ├── Configure Backend
    └── Create Golden AMI
            │
            ▼
      Launch Template
            │
            ▼
     Auto Scaling Group
            │
            ▼
      Backend EC2 Instances
            │
            ▼
        Target Group
            │
            ▼
Application Load Balancer
            │
            ▼
          End Users
```

This deployment pattern is called the **Golden AMI Pattern**.

- Builder EC2 is temporary.
- Software installation happens only once.
- Golden AMI is created.
- Builder EC2 is terminated.
- Production EC2 instances are launched from the Golden AMI through the Auto Scaling Group.
- ALB sends traffic only to healthy backend instances.
