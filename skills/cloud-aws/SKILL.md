# Cloud AWS/Azure/GCP Skill

## Purpose
Expert cloud infrastructure design, deployment, and management across AWS, Azure, and GCP. Focus on cloud-native architectures, cost optimization, and security.

## AWS Core Services

### Compute
- **EC2**: Instance types, auto scaling, spot instances
- **Lambda**: Serverless functions, cold starts optimization
- **ECS/EKS**: Container orchestration
- **Lightsail**: Simple VPS for small projects

### Storage
- **S3**: Object storage, lifecycle policies, versioning
- **EBS**: Block storage for EC2
- **EFS**: Managed NFS file system
- **Glacier**: Archive storage

### Database
- **RDS**: Managed relational databases
- **DynamoDB**: NoSQL, key-value
- **ElastiCache**: Redis/Memcached caching
- **Aurora**: Serverless relational database
- **Neptune**: Graph database

### Networking
- **VPC**: Virtual private cloud, subnets, routing
- **CloudFront**: CDN, edge caching
- **Route 53**: DNS management
- **API Gateway**: REST/HTTP APIs
- **ALB/NLB**: Load balancing

### Security
- **IAM**: Identity and access management
- **KMS**: Key management service
- **WAF**: Web application firewall
- **Shield**: DDoS protection
- **GuardDuty**: Threat detection

## Azure Core Services

### Compute
- **Virtual Machines**: Windows/Linux VMs
- **Azure Functions**: Serverless compute
- **AKS**: Azure Kubernetes Service
- **App Service**: Web app hosting

### Storage
- **Blob Storage**: Object storage
- **Azure Files**: Managed file shares
- **Disk Storage**: Managed disks

### Database
- **Azure SQL**: Managed SQL Server
- **Cosmos DB**: Multi-model NoSQL
- **Azure Database for PostgreSQL/MySQL**
- **Azure Cache for Redis**

## GCP Core Services

### Compute
- **Compute Engine**: VMs
- **Cloud Functions**: Serverless
- **GKE**: Google Kubernetes Engine
- **Cloud Run**: Containerized apps

### Storage
- **Cloud Storage**: Object storage
- **Persistent Disk**: Block storage
- **Filestore**: Managed NFS

### Database
- **Cloud SQL**: Managed MySQL/PostgreSQL
- **Firestore**: NoSQL document database
- **Bigtable**: Wide-column NoSQL
- **Spanner**: Global relational database

## Infrastructure as Code

### Terraform
```hcl
resource "aws_instance" "web" {
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = "t3.micro"
  
  tags = {
    Name = "WebServer"
  }
}
```

### AWS CDK (TypeScript)
```typescript
import * as ec2 from 'aws-cdk-lib/aws-ec2';
import * as s3 from 'aws-cdk-lib/aws-s3';

const vpc = new ec2.Vpc(this, 'MainVPC', { maxAzs: 2 });
const bucket = new s3.Bucket(this, 'DataBucket', { versioned: true });
```

## Cost Optimization
- Use Reserved Instances for predictable workloads
- Leverage Spot Instances for batch processing
- Right-size instances based on utilization
- Use S3 Intelligent-Tiering for storage
- Monitor with AWS Cost Explorer
- Set up billing alarms

## Best Practices
- Design for failure (multi-AZ, multi-region)
- Use auto scaling for all services
- Implement proper IAM least privilege
- Enable logging and monitoring (CloudWatch, CloudTrail)
- Use Secrets Manager for credentials
- Implement backup strategies
- Follow Well-Architected Framework
