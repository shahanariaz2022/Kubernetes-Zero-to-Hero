Create EKS Cluster
```bash
Eksctl create cluster --name my-cluster --region us-east-1 --fargate
```
Change cluster name and region according to yours.  
We use --fargate with eksctl create cluster so that the cluster runs workloads on AWS Fargate (serverless containers) instead of managing EC2 worker nodes manually.
