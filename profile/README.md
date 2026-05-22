<div align="center">

# ☁️ SimpliCloud

**Production-grade cloud infrastructure on AWS — built end-to-end with Terraform IaC, event-driven serverless pipelines, multi-AZ networking, and KMS-encrypted secrets.**

</div>

---

## What Is This?

SimpliCloud is a full-stack AWS infrastructure project spanning three repositories that work together as a single system:

- **Terraform provisions the entire network layer** — VPC, subnets across availability zones, security groups, load balancers, auto-scaling groups, Route53 DNS, and ACM-managed SSL certificates.
- **A Node.js REST API** runs on EC2 within that infrastructure, backed by RDS (PostgreSQL), S3 object storage, and GitHub Actions CI/CD with automated AMI deployments.
- **A serverless pipeline** handles email verification via SNS → Lambda → SES, with delivery status tracked in DynamoDB and all secrets encrypted via KMS.

---

## Architecture

```
Internet → Route53 → ACM (SSL) → Application Load Balancer
                                            │
                              ┌─────────────┘
                              │
                   ┌──────────▼──────────┐        ┌────────────────────┐
                   │   EC2 (webapp)       │──────▶ │  RDS PostgreSQL     │
                   │   Auto-Scaling Group │        │  Multi-AZ           │
                   │   (multi-AZ)         │        └────────────────────┘
                   └──────────┬──────────┘
                              │
                   ┌──────────▼──────────┐        ┌────────────────────────────┐
                   │   S3 Bucket          │        │  Serverless Pipeline        │
                   │   (KMS encrypted)    │        │  SNS → Lambda → SES        │
                   └─────────────────────┘        │           ↓                │
                                                  │        DynamoDB             │
                                                  └────────────────────────────┘

  Terraform (tf-aws-infra) provisions ALL of the above
  GitHub Actions CI/CD deploys on every merge to main
```

---

## Repositories

| Repo | Description | Stack |
|------|-------------|-------|
| [**tf-aws-infra**](https://github.com/SimpliCloud/tf-aws-infra) | Terraform modules for the entire AWS network layer — VPC, multi-AZ subnets, security groups, ALB, auto-scaling, Route53, and ACM SSL | HCL / Terraform |
| [**webapp**](https://github.com/SimpliCloud/webapp) | Node.js REST API deployed on EC2 with RDS, S3, KMS encryption, and automated GitHub Actions CI/CD | Node.js / AWS |
| [**serverless**](https://github.com/SimpliCloud/serverless) | Event-driven email verification pipeline: SNS → Lambda → SES with DynamoDB delivery tracking and KMS-encrypted secrets | AWS Lambda / Node.js |

---

## Tech Stack

**Cloud Infrastructure**
`AWS EC2` `AWS RDS` `AWS S3` `AWS Lambda` `AWS SNS` `AWS SES` `AWS DynamoDB` `AWS KMS` `AWS ACM` `AWS Route53` `AWS ALB`

**IaC & CI/CD**
`Terraform` `GitHub Actions`

**Application**
`Node.js` `PostgreSQL`

**Security**
`KMS Encryption` `SSL/TLS` `IAM Roles & Policies` `Security Groups`

---

## Key Engineering Decisions

- **Multi-AZ by default** — EC2 auto-scaling group and RDS span multiple availability zones for fault tolerance.
- **KMS encryption throughout** — S3 objects, RDS snapshots, and Lambda environment variables all encrypted with customer-managed KMS keys.
- **Immutable deployments** — GitHub Actions builds a new AMI on every push to main; the auto-scaling group rolls to the new AMI without SSH access.
- **Event-driven email pipeline** — decoupled from the webapp via SNS so failures in email delivery never affect API availability.
- **Terraform state isolation** — separate state files per environment to prevent blast radius from infrastructure changes.

---

<div align="center">

Built by [Vatsal Naik](https://github.com/vatsalnaik) · MS Information Systems, Northeastern University

</div>
