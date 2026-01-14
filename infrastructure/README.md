# IMPORTANT WARNING 
**DO NOT** use this infrastructure for any production loads. The cluster and associated resources declared here are intentionally insecure. This is acceptable for a throwaway excercise-cluster, but by means ready for prolonged usage. Examples are: 

 - All cluster-users are admin. This is great for teaching as participants can do and see everything. This also means that any participant can destroy or compromise the entire cluster with no audit-logs. 
 - Cluster-resources like ArgoCD and Grafana also only have admin login, see risk and gains above. 
 - The container-registry allows _anyone_ to pull your images. This avoid tedious logins for participants, but also opens up all your containers for the world to see
 - All code is open-source, including infrastructure and config. 
 - Logs are stored in-cluster and can therefore just disappear if you re-deploy the right things
 - ACR push is authenticated via a simple and static password. This prevents easy key-rotation and does not use any modern best practises. But it lets participants easily push images to ACR too. 
 - Terraform uses local state. This is practical as it lets terraform use your cli-credetials and run commands as you. So you don't need to configure RBAC between Terraform Cloud and Azure. However, if you delete the `.terraform`-folder, you will need to manually tear down the cluster. 

 
# The Variant LAB Cluster
## Using the cluster
### Prerequisites 
 - azure-cli, kubectl, terraform and terraform login installed 
 - An active Azure subscription in which you have access to deploy resources to 
 - run `terraform init` in `infrasctructure/`

### Create the cluster 

   1. Copy `settings.tf.template` to `settings.tf` 
   2. Fill out your desired config. 
   3. Run `terraform apply` 


Then, to get the config you need: 
**Kubectl setup:**
```bash
az aks get-credentials --resource-group <see terraform output> --name <cluster name, see terraform output>
```

**Grafana login:**
Username: admin,
Password: see command:
```bash
kubectl -n monitoring get secret grafana -o jsonpath="{.data.admin-password}" | base64 -d ; echo
```

**ArgoCD login:**
Username: admin,
Password: 
```bash
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d ; echo   
```


### Teardown 
`terraform destroy`


## Useful commands for kubectl:
**Create a deployment template**
```bash
kubectl create deployment <deployment-name> --image <image-name>  --dry-run=client -o yaml > file-name.yml
```

**Apply/deploy resources from a yml file**
```bash
kubectl apply -f file-name.yml
```

**Expose a deployment to the public web in Azure k8s (AKS)**
```bash
kubectl expose deploy/<deployment-name> --type=LoadBalancer --port=80 --name=<name-of-service-this-step-creates>
```

