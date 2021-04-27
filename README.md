# README

Deploy Kubernetes cluster on AWS EKS with Terraform  

* Create an EKS cluster with Terraform.
* Integrate the ALB as the Ingress controller as part of the cluster setup.
* Modularize the code to provision copies of the cluster (one for each environment: dev, staging and production).
* Make the instance type customizable for each environment.
    
This is the complete implementation of the infrastructure explained here:
https://learnk8s.io/terraform-eks

### Some Random Notes

* Updated provider and module versions

* Updated Helm repo address of AWS Load Balancer Controller  
Used -> helm repo add eks https://aws.github.io/eks-charts  
instead of -> helm repo add incubator http://storage.googleapis.com/kubernetes-charts-incubator

* Since the ALB Ingress Controller runs as a Pod in one of the Nodes, all the Nodes should have permissions to describe, modify, etc. the Application Load Balancer.  
The following file contains the necessary IAM Policy for the worker nodes:
```
wget https://raw.githubusercontent.com/kubernetes-sigs/aws-alb-ingress-controller/main/docs/install/iam_policy.json
```

* Install the TargetGroupBinding CRDs AWS Load Balancer Controller:
```
kubectl apply -k "github.com/aws/eks-charts/stable/aws-load-balancer-controller//crds?ref=master"
```

* Install the AWS Load Balancer controller:
```
helm install ingress eks/aws-load-balancer-controller --set autoDiscoveryAwsRegion=true --set autoDiscoveryAwsVpcID=true --set clusterName=my-cluster -n kube-system
```

* Additional tools needed:  
aws-iam-authenticator

### TODO

* `terraform destroy` could not succeed to delete ALB and the associated resources during my tests. This seems like a bug, needs to be investigated further.