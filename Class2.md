# Docker App 3 Tier -> Docker Hub
# Secretes and config map
# Docker and secrets
# CPV PVC statef
# INgress
# RBAC


Source code: https://github.com/UnpredictablePrashant/LearningK8s
Docker Image: https://hub.docker.com/r/prashantdey/appk8stutorial/tags

eksctl create cluster --name sample-cluster-cls2 --version 1.36 --region us-east-2 --nodegroup-name standard-workers --node-type t3.medium --nodes 2 --nodes-min 2 --nodes-max 3
eksctl create cluster --name sample-cluster-cls2 --region ap-south-1 --nodegroup-name standard-workers --node-type t3.medium --nodes 2 --nodes-min 2 --nodes-max 3

aws eks --region ap-south-1 update-kubeconfig --name sample-cluster-cls2

