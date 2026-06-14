
```

eksctl create cluster --name sample-cluster-cls3 --region ap-south-1 --nodegroup-name standard-workers --node-type t3.medium --nodes 2 --nodes-min 2 --nodes-max 3


aws eks --region ap-south-1 update-kubeconfig --name sample-cluster-cls3


kubectl config get-contexts

```

> <img width="1394" height="304" alt="image" src="https://github.com/user-attachments/assets/c6c51681-61b1-4d06-8f22-fe4fb0fec5c5" />


Amazon EBS CSI Driver for Storage Class ( Add-on of Cluster created above)


> <img width="1514" height="853" alt="image" src="https://github.com/user-attachments/assets/25def4ff-d03a-4a0f-b547-8d783c3ff57d" />






# Must delete at the end of the class

```

eksctl delete cluster --name sample-cluster-cls3 --region ap-south-1

```


