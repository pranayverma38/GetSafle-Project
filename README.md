# Node.js Application Deployment on AWS (CI/CD + Docker + Terraform)

## Objective

This project sets up a **containerized Node.js REST API** deployed on **AWS** using a **secure, scalable, and automated infrastructure**. It includes:
- Docker containerization
- Infrastructure-as-Code with Terraform
- CI/CD pipeline with GitHub Actions
- Load balancing, monitoring, and security best practices

---

## Application: Node.js REST API

- Built with Express.js
- Sample Endpoints: `/health`, `/users`
- Environment variables are used for configuration (`PORT`, `DB_URL`)
- Unit testing implemented with Jest and Supertest

---

## Docker: Containerization

- **Dockerfile** uses a minimal `node:alpine` base image
- App runs as a **non-root user**
- `docker-compose.yml` (optional) for local development with PostgreSQL
- Exposes port 3000, production-ready

---

## Infrastructure: Terraform on AWS

- **Modules** provision:
  - VPC (public/private subnets)
  - EC2 Auto Scaling Group behind ALB
  - Amazon RDS (PostgreSQL)
  - AWS ECR
  - IAM Roles with least privilege
- Modular structure with reusable components
- All variables customizable via `terraform.tfvars`

---

## CI/CD Pipeline: GitHub Actions

The CI/CD pipeline (in `.github/workflows/ci-cd.yml`) includes:

- **Test**: Runs unit tests on every push
- **Build & Push**: Builds Docker image and pushes to ECR
- **Deploy**: Applies infrastructure with Terraform and deploys to EC2
- **Rollback handling**: Can be extended with Terraform state locking and GitHub Action status checks

---

## High Availability & Load Balancing

This system is designed for **zero-downtime deployments** and **auto-scaling**.

### ✅ Key Features
- **Application Load Balancer (ALB)**: Routes incoming traffic based on health checks
- **Auto Scaling Group**:
  - Automatically scales EC2 instances based on CPU/memory
  - Launches instances in multiple Availability Zones for redundancy
- **Health Checks**:
  - Configured on `/health` endpoint
  - Instances failing health checks are removed from ALB routing

### 📌 Terraform Highlights
`hcl
resource "aws_autoscaling_group" "app_asg" {
  desired_capacity = 2
  max_size         = 4
  min_size         = 1
  ...
}

resource "aws_lb_target_group" "app_tg" {
  health_check {
    path                = "/health"
    healthy_threshold   = 2
    unhealthy_threshold = 2
  }
}`

---

# Monitoring and Alerts

# Tools Used
Amazon CloudWatch: Default monitoring

Optional: Self-hosted Prometheus + Grafana

# Metrics Collected
EC2 Instances: CPU, memory, disk

Application: Request rates, 4xx/5xx error rates

Load Balancer: Latency, Target Health, Request Count

Database (RDS): Connections, CPU usage, slow queries

# Grafana Dashboard
Custom dashboards for:

Request latency

HTTP status code distribution

Memory usage

Can be hosted via:

EC2 + Docker

Amazon Managed Grafana

# Alerts
CloudWatch Alarms:

High CPU usage

5xx error spike

Memory thresholds

Notifications via:

Amazon SNS to Email or Slack webhook

Can integrate with PagerDuty or OpsGenie


