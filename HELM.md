# HELM


```
history of helm commands in this session:

helm create sampleapp

helm lint .

helm template sampleapp . // this sampleapp is assigned to Release variable of the Helm which is used in service.yaml and deployment.yaml

helm template nginx-app . // we can also run like this to give release name as 'nginx-app' instead 'sampleapp'

helm install sampleapp . -n helm-learning --create-namespace ///need kubectl installed 

kubectl get all -n helm-learning // te list down all pods running with namespace helm-learning

helm upgrade sampleapp . -n helm-learning --set replicaCount=4 // for upgrade sampleapp version from 2 - 4 

kubectl get pods -n helm-learning // get pods running after upgrade

helm rollback sampleapp 1 -n helm-learning // to rolleback to previous version before replica upgrade above

helm history sampleapp -n helm-learning // to get history of deployment with status

cd ..

helm package .\sampleapp\ //save package as tgz file and then install in other system to run same package "sampleapp-0.1.0.tgz". this name came from Chart.y


```


Practice
```
PS C:\Users\abhis\Harika\K8s\Practice\EKS\EKS-Practice> cd live
PS C:\Users\abhis\Harika\K8s\Practice\EKS\EKS-Practice\live> helm lint .
==> Linting .
Error unable to check Chart.yaml file in chart: CreateFile Chart.yaml: The system cannot find the file specified.

Error: 1 chart(s) linted, 1 chart(s) failed
PS C:\Users\abhis\Harika\K8s\Practice\EKS\EKS-Practice\live> cd .\sampleapp\
PS C:\Users\abhis\Harika\K8s\Practice\EKS\EKS-Practice\live\sampleapp> helm lint .
==> Linting .
[INFO] Chart.yaml: icon is recommended

1 chart(s) linted, 0 chart(s) failed
PS C:\Users\abhis\Harika\K8s\Practice\EKS\EKS-Practice\live\sampleapp> helm template sampleapp .
---
# Source: sampleapp/templates/service.yaml
apiVersion: v1
kind: Service
metadata:
    name: sampleapp-nginx-service
    labels:
        app: sampleapp-nginx
spec:
    type: ClusterIP

    selector:
        app: sampleapp-nginx
    
    ports:
        - protocol: TCP
          port: 80
          targetPort: 80
---
# Source: sampleapp/templates/deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
    name: sampleapp-nginx
    labels:
        app: sampleapp-nginx
spec:
    replicas: 2
    selector:
        matchLabels:
            app: sampleapp-nginx
    template:
        metadata:
            labels:
                app: sampleapp-nginx

        spec:
            containers:
                - name: nginx
                  image: "nginx:latest"
                  
                  ports:
                    - containerPort: 80
                      protocol: TCP
PS C:\Users\abhis\Harika\K8s\Practice\EKS\EKS-Practice\live\sampleapp> helm template nginx-app .
---
# Source: sampleapp/templates/service.yaml
apiVersion: v1
kind: Service
metadata:
    name: nginx-app-nginx-service
    labels:
        app: nginx-app-nginx
spec:
    type: ClusterIP

    selector:
        app: nginx-app-nginx
    
    ports:
        - protocol: TCP
          port: 80
          targetPort: 80
---
# Source: sampleapp/templates/deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
    name: nginx-app-nginx
    labels:
        app: nginx-app-nginx
spec:
    replicas: 2
    selector:
        matchLabels:
            app: nginx-app-nginx
    template:
        metadata:
            labels:
                app: nginx-app-nginx

        spec:
            containers:
                - name: nginx
                  image: "nginx:latest"
                  
                  ports:
                    - containerPort: 80
                      protocol: TCP
PS C:\Users\abhis\Harika\K8s\Practice\EKS\EKS-Practice\live\sampleapp> hemp install sampleapp . -n helm-learning --create-namespace
hemp : The term 'hemp' is not recognized as the name of a cmdlet, function, script file, or operable program. Check the spelling of the name, or if a path was included, verify that the 
path is correct and try again.
At line:1 char:1
+ hemp install sampleapp . -n helm-learning --create-namespace
+ ~~~~
    + CategoryInfo          : ObjectNotFound: (hemp:String) [], CommandNotFoundException
    + FullyQualifiedErrorId : CommandNotFoundException
 
PS C:\Users\abhis\Harika\K8s\Practice\EKS\EKS-Practice\live\sampleapp> helm install sampleapp . -n helm-learning --create-namespace
NAME: sampleapp
LAST DEPLOYED: Tue Jul 21 17:16:55 2026
NAMESPACE: helm-learning
STATUS: deployed
REVISION: 1
TEST SUITE: None
PS C:\Users\abhis\Harika\K8s\Practice\EKS\EKS-Practice\live\sampleapp> kubectl get all -n helm-learning
NAME                                   READY   STATUS    RESTARTS   AGE
pod/sampleapp-nginx-59d84bf566-r49bc   1/1     Running   0          19s
pod/sampleapp-nginx-59d84bf566-rk9vw   1/1     Running   0          19s

NAME                              TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE
service/sampleapp-nginx-service   ClusterIP   10.100.10.152   <none>        80/TCP    20s

NAME                              READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/sampleapp-nginx   2/2     2            2           20s

NAME                                         DESIRED   CURRENT   READY   AGE
replicaset.apps/sampleapp-nginx-59d84bf566   2         2         2       20s
PS C:\Users\abhis\Harika\K8s\Practice\EKS\EKS-Practice\live\sampleapp> helm upgrade sampleapp .n helm-learning --set replicaCount=4
Error: "helm upgrade" requires 2 arguments

Usage:  helm upgrade [RELEASE] [CHART] [flags]
PS C:\Users\abhis\Harika\K8s\Practice\EKS\EKS-Practice\live\sampleapp> helm upgrade sampleapp . -n helm-learning --set replicaCount=4
Release "sampleapp" has been upgraded. Happy Helming!
NAME: sampleapp
LAST DEPLOYED: Tue Jul 21 17:17:47 2026
NAMESPACE: helm-learning
STATUS: deployed
REVISION: 2
TEST SUITE: None
PS C:\Users\abhis\Harika\K8s\Practice\EKS\EKS-Practice\live\sampleapp> kubectl get pods -n helm-learning
NAME                               READY   STATUS    RESTARTS   AGE
sampleapp-nginx-59d84bf566-6wnrj   1/1     Running   0          20s
sampleapp-nginx-59d84bf566-jc5hj   1/1     Running   0          20s
sampleapp-nginx-59d84bf566-r49bc   1/1     Running   0          72s
sampleapp-nginx-59d84bf566-rk9vw   1/1     Running   0          72s
PS C:\Users\abhis\Harika\K8s\Practice\EKS\EKS-Practice\live\sampleapp> helm rollback sampleapp 1 -n helm-learning
Rollback was a success! Happy Helming!
PS C:\Users\abhis\Harika\K8s\Practice\EKS\EKS-Practice\live\sampleapp> kubectl get pods -n helm-learning         
NAME                               READY   STATUS    RESTARTS   AGE
sampleapp-nginx-59d84bf566-jc5hj   1/1     Running   0          77s
sampleapp-nginx-59d84bf566-rk9vw   1/1     Running   0          2m9s
PS C:\Users\abhis\Harika\K8s\Practice\EKS\EKS-Practice\live\sampleapp> helm history sampleapp -n helm-learning
REVISION        UPDATED                         STATUS          CHART           APP VERSION     DESCRIPTION     
1               Tue Jul 21 17:16:55 2026        superseded      sampleapp-0.1.0 3.5.6           Install complete
2               Tue Jul 21 17:17:47 2026        superseded      sampleapp-0.1.0 3.5.6           Upgrade complete
3               Tue Jul 21 17:19:02 2026        deployed        sampleapp-0.1.0 3.5.6           Rollback to 1   
PS C:\Users\abhis\Harika\K8s\Practice\EKS\EKS-Practice\live\sampleapp> cd ..
PS C:\Users\abhis\Harika\K8s\Practice\EKS\EKS-Practice\live> helm package .\sampleapp\
Successfully packaged chart and saved it to: C:\Users\abhis\Harika\K8s\Practice\EKS\EKS-Practice\live\sampleapp-0.1.0.tgz
PS C:\Users\abhis\Harika\K8s\Practice\EKS\EKS-Practice\live>
```
