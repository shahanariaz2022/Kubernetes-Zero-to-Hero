### Create Fargate Profile  
```bash
Create fargate profile
eksctl create fargateprofile \
    --cluster my-cluster \
    --region us-east-1 \
    --name alb-sample-app \  (name of app)
    --namespace game-2048 (namespace name)
```

It tells EKS which namespaces’ pods should run on Fargate.
Without it, your pods won’t start on Fargate.   
