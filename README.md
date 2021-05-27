# eks-calico-enterprise

We need to install the eksctl utility for controlling EKS:
https://docs.aws.amazon.com/eks/latest/userguide/eksctl.html

We also need the kubectl utility for controlling Kubernetes:
https://docs.aws.amazon.com/eks/latest/userguide/install-kubectl.html


<img width="1151" alt="Screenshot 2021-05-27 at 11 03 26" src="https://user-images.githubusercontent.com/82048393/119807824-6f56b200-bedb-11eb-8e09-b6ecb76c3a99.png">

Once eksctl is installed, we can run the below command to create our cluster:

```
eksctl create cluster  --name nigel-eks-cluster  --version 1.19  --with-oidc  --without-nodegroup
```

Delete the aws-node daemon set to disable AWS VPC networking for pods
```
kubectl delete daemonset -n kube-system aws-node
```

AWS StorageClass tells Calico Enterprise to use EBS disks for log storage
```
kubectl apply -f https://raw.githubusercontent.com/n1g3ld0uglas/netpolTest/main/sc/ebs.yaml
```

Install the Tigera operator and custom resource definitions
```
kubectl create -f https://docs.tigera.io/manifests/tigera-operator.yaml
```

Install the Prometheus operator and related custom resource definitions
```
kubectl create -f https://docs.tigera.io/manifests/tigera-prometheus-operator.yaml
```

Install your pull secret - referencing the same config.json provided to you by the Tigera sales team
```
kubectl create secret generic tigera-pull-secret --type=kubernetes.io/dockerconfigjson -n tigera-operator --from-file=.dockerconfigjson=config.json
```

