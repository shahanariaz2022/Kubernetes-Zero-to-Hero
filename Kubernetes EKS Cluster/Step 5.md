### Create Fargate Profile  
```bash
eksctl create fargateprofile \
    --cluster my-cluster \
    --region us-east-1 \
    --name alb-sample-app \  
    --namespace game-2048 
```
Name of App = alb-sample-app
Namespace name = game-2048 
It tells EKS which namespaces’ pods should run on Fargate.
Without it, your pods won’t start on Fargate.   
