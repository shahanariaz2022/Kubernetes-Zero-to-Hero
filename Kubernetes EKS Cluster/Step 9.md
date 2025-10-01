
### Deploy ALB controller
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
