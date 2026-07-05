# DevOps CI/CD Skill

## Purpose
Expert DevOps practices, CI/CD pipeline design, infrastructure automation, and deployment strategies. Covers GitHub Actions, GitLab CI, Jenkins, and more.

## CI/CD Platforms

### GitHub Actions
```yaml
name: CI/CD Pipeline

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'npm'
      - run: npm ci
      - run: npm run test:coverage
      - uses: actions/upload-artifact@v4
        with:
          name: coverage
          path: coverage/

  deploy:
    needs: test
    if: github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Deploy to AWS
        uses: aws-actions/configure-aws-credentials@v4
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: us-east-1
      - run: aws s3 sync ./dist s3://my-bucket
```

### GitLab CI
```yaml
stages:
  - test
  - build
  - deploy

test:
  stage: test
  image: node:20
  script:
    - npm ci
    - npm run test
  coverage: '/Lines\s*:\s*(\d+\.?\d*)%/'

build:
  stage: build
  image: docker:latest
  services:
    - docker:dind
  script:
    - docker build -t $CI_REGISTRY_IMAGE:$CI_COMMIT_SHA .
    - docker push $CI_REGISTRY_IMAGE:$CI_COMMIT_SHA

deploy_production:
  stage: deploy
  only:
    - main
  script:
    - kubectl set image deployment/myapp myapp=$CI_REGISTRY_IMAGE:$CI_COMMIT_SHA
```

## Deployment Strategies

### Blue-Green Deployment
- Two identical environments (blue and green)
- Zero-downtime deployment
- Quick rollback capability
- Traffic switch at load balancer level

### Canary Deployment
- Gradual rollout to small percentage of users
- Monitor metrics before full deployment
- Automated rollback on errors
- Feature flags for control

### Rolling Update
- Gradual replacement of instances
- Configurable batch size
- Health checks between batches
- Minimal downtime

## Infrastructure Automation

### Terraform
```hcl
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }
  backend "s3" {
    bucket = "terraform-state"
    key    = "prod/terraform.tfstate"
    region = "us-east-1"
  }
}

resource "aws_ecs_cluster" "main" {
  name = "production"
  
  setting {
    name  = "containerInsights"
    value = "enabled"
  }
}
```

### Ansible Playbook
```yaml
---
- hosts: webservers
  become: yes
  vars:
    app_version: "1.2.3"
  
  tasks:
    - name: Update apt cache
      apt:
        update_cache: yes
        cache_valid_time: 3600
    
    - name: Install Docker
      apt:
        name: docker.io
        state: present
    
    - name: Deploy application
      docker_container:
        name: myapp
        image: "myregistry/myapp:{{ app_version }}"
        state: started
        restart_policy: unless-stopped
        ports:
          - "80:3000"
```

## Monitoring & Observability

### Prometheus + Grafana
```yaml
# prometheus.yml
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: 'node'
    static_configs:
      - targets: ['localhost:9100']
  
  - job_name: 'app'
    metrics_path: '/metrics'
    static_configs:
      - targets: ['app:3000']
```

## Best Practices
- Implement trunk-based development
- Use feature flags for progressive delivery
- Automate testing at every stage
- Implement proper secret management
- Use infrastructure as code for all resources
- Monitor everything (logs, metrics, traces)
- Practice chaos engineering
- Implement proper backup and disaster recovery
