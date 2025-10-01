# Kubernetes EKS Project 

This project on Ubuntu machine running on vmware installed on macOS.   
Let's prepare ubuntu machine 
### Step 1: Install Kubectl, ekctl, AWS CLI  

Install following on your ubuntu machine 

1. kubectl – A command line tool for working with Kubernetes clusters. For more information, see Installing or updating kubectl.

2. eksctl – A command line tool for working with EKS clusters that automates many individual tasks. For more information, see Installing or updating.

3. AWS CLI – A command line tool for working with AWS services, including Amazon EKS. For more information, see Installing, updating, and uninstalling the AWS CLI in the AWS Command Line Interface User Guide.  

4. After installing the AWS CLI, we recommend that you also configure it. For more information, see Quick configuration with aws configure in the AWS Command Line Interface User Guide.  
Run command  

```bash
aws configure
```
It will ask you  
a. Aws access key id  
b. Secret access key  
You can get these from security credential on aws portal. 


### Step 2: Create EKS Cluster
```bash
Eksctl create cluster --name my-cluster --region region-code --fargate
```
Change cluster name and region according to yours.  
We use --fargate with eksctl create cluster so that the cluster runs workloads on AWS Fargate (serverless containers) instead of managing EC2 worker nodes manually.

you need to configure kubectl to work with EKS cluster  
Run command with your cluster name
   ```bash
    aws eks update-kubeconfig --name your-cluster-name
   ```
### Step 3: Create Fargate Profile  
```bash
Create fargate profile
eksctl create fargateprofile \
    --cluster demo-cluster \
    --region us-east-1 \
    --name alb-sample-app \  (name of app)
    --namespace game-2048 (namespace name)
```

It tells EKS which namespaces’ pods should run on Fargate.
Without it, your pods won’t start on Fargate.   

### Step 4: Deploy Deployment, service and ingress 
```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.5.4/docs/examples/2048/2048_full.yaml)
```
Run commands to check  
```bash
Kubectl get pods -n game-2048
Kubectl get svc -n game-2048 #(external-ip empty)
Kubectl get ingress -n game-2048 #(address field empty)
```
### Step 5: Install AWS ALB Controller and IAM policies  
1. Download IAM policy for the controller
```bash
curl -O https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.11.0/docs/install/iam_policy.json
```

2. Create IAM Policy in AWS
```bash
aws iam create-policy \
  --policy-name AWSLoadBalancerControllerIAMPolicy \
  --policy-document file://iam_policy.json
```
3. Create IAM role
``` bash
eksctl create iamserviceaccount \
  --cluster=<your-cluster-name> \
  --namespace=kube-system \
  --name=aws-load-balancer-controller \
  --role-name AmazonEKSLoadBalancerControllerRole \
  --attach-policy-arn=arn:aws:iam::<your-aws-account-id>:policy/AWSLoadBalancerControllerIAMPolicy \
  --approve
```

4. Deploy ALB controller
Add helm repo
```bash
helm repo add eks https://aws.github.io/eks-charts
```
Update the repo
```bash
helm repo update eks
```
Install
```bash
helm install aws-load-balancer-controller eks/aws-load-balancer-controller -n kube-system \
  --set clusterName=<your-cluster-name> \
  --set serviceAccount.create=false \
  --set serviceAccount.name=aws-load-balancer-controller \
  --set region=<your-region> \
  --set vpcId=<your-vpc-id>
```
Verify that the deployments are running.
```bash
kubectl get deployment -n kube-system aws-load-balancer-controller
```
