### Deploy Deployment, service and ingress 
```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.5.4/docs/examples/2048/2048_full.yaml)
```
Run commands to check  
```bash
Kubectl get pods -n game-2048
Kubectl get svc -n game-2048 #(external-ip empty)
Kubectl get ingress -n game-2048 #(address field empty)
```
