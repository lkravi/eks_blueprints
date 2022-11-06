# EKS Blueprints Demo with GitOps(ArgoCD)

### Deploy
```
terraform init
terraform plan --var-file inputs.tfvars
terraform apply --var-file inputs.tfvars
```

## Architecture Diagram
![alt text](https://raw.githubusercontent.com/lkravi/eks_blueprints/main/static/eks-bp.png)

### Access CLuster
Run update-kubeconfig command:
```
aws eks --region us-east-1 update-kubeconfig --name eks-bp-demo
```

### Access ArgoCD UI
```
kubectl port-forward svc/argo-cd-argocd-server 8080:443 -n argocd
```

Get the Argo Admin password form secrets manager
```
aws secretsmanager get-secret-value --secret-id argocd --region us-east-1
```


### Destroy
```
terraform destroy -target=module.eks_blueprints_kubernetes_addons -auto-approve
terraform destroy -target=module.eks_blueprints -auto-approve
terraform destroy -target=module.vpc -auto-approve
terraform destroy -auto-approve
```

### Github Actions
There are 3 workflows setup for the Infra provisioning, PR review and destroy.
* Once you open a PR for main branch.
    ** PR review workflow will run and validate the PR.
    ** Once you merge the PR, It will Run the Infra provisioning workflow.
* Lastly you can trigger Terraform Destroy Workflow to destroy all the resources. 


### Observability

We are using Prometheus and Grafana for the Observability.

To access Prometheus server
```
kubectl port-forward -n prometheus deploy/prometheus-server 8080:9090
```

To Get Grafana Admin password
```
kubectl get secret --namespace grafana grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo
```

TO access Grafana Dashboard
```
kubectl port-forward service/grafana 8080:80 -n grafana
```

Once you login to the Grafana Dashboard you can add Prometheus as the Data Source. 
Then you can import predefined dashboards from grafana.com or you can create your own one.
* Kubernetes Cluster Dashboard - 6417

### Related Repos
- Workloads https://github.com/lkravi/eks_blueprints_workloads
- Demo App https://github.com/lkravi/eks_blueprints_demo_app

