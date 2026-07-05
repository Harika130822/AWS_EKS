# HELM


history of helm commands in this session:

helm create sampleapp

helm lint .

helm template sampleapp . // this sampleapp is assigned to Release variable of the Helm which is used in service.yaml and deployment.yaml

helm template nginx-app . // we can also run like this to give release name as 'nginx-app' instead 'sampleapp'

hemp install sampleapp . -n helm-learning --create-namespace ///need kubectl installed 

kubectl get all -n helm-learning // te list down all pods running with namespace helm-learning

helm upgrade sampleapp .n helm-learning --set replicaCount=4 // for upgrade sampleapp version from 2 - 4 

kubectl get pods -n helm-learning // get pods running after upgrade

helm rollback sampleapp 1 -n helm-learning // to rolleback to previous version before replica upgrade above

helm history sampleapp -n helm-learning // to get history of deployment with status

cd ..

helm package .\sampleapp\ //save package as tgz file and then install in other system to run same package "sampleapp-0.1.0.tgz". this name came from Chart.y

docker pull prashantdey/bat16a-tm:v1.0
