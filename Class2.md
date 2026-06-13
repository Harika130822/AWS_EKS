# Docker App 3 Tier -> Docker Hub
# Secretes and config map
# Docker and secrets
# CPV PVC statef
# INgress
# RBAC


Source code: https://github.com/UnpredictablePrashant/LearningK8s
Docker Image: https://hub.docker.com/r/prashantdey/appk8stutorial/tags

# --version 1.36
eksctl create cluster --name sample-cluster-cls2 --region us-east-2 --nodegroup-name standard-workers --node-type t3.medium --nodes 2 --nodes-min 2 --nodes-max 3
<img width="1468" height="335" alt="image" src="https://github.com/user-attachments/assets/6370685f-ded6-41c7-b5b0-6025b01245fe" />


aws eks --region ap-south-1 update-kubeconfig --name sample-cluster-cls2
kubectl config get-contexts
<img width="1557" height="500" alt="image" src="https://github.com/user-attachments/assets/51649898-e203-4340-ae03-04c2156216b2" />

Application deployment into the cluster
```
PS C:\Users\abhis\Harika\K8s\Class2\LearningK8s> kubectl config get-contexts
CURRENT   NAME                                                              CLUSTER                                                           AUTHINFO                                                          NAMESPACE
          admincliprac@sample-cluster-cls2.ap-south-1.eksctl.io             sample-cluster-cls2.ap-south-1.eksctl.io                          admincliprac@sample-cluster-cls2.ap-south-1.eksctl.io             
          arn:aws:eks:ap-south-1:129373676098:cluster/sample-cluster-3      arn:aws:eks:ap-south-1:129373676098:cluster/sample-cluster-3      arn:aws:eks:ap-south-1:129373676098:cluster/sample-cluster-3      
*         arn:aws:eks:ap-south-1:129373676098:cluster/sample-cluster-cls2   arn:aws:eks:ap-south-1:129373676098:cluster/sample-cluster-cls2   arn:aws:eks:ap-south-1:129373676098:cluster/sample-cluster-cls2   
          docker-desktop                                                    docker-desktop                                                    docker-desktop                                                    
PS C:\Users\abhis\Harika\K8s\Class2\LearningK8s> ls 


    Directory: C:\Users\abhis\Harika\K8s\Class2\LearningK8s


Mode                 LastWriteTime         Length Name                                                                                                                                                  
----                 -------------         ------ ----                                                                                                                                                  
d-----        13-06-2026     11:47                app                                                                                                                                                   
d-----        13-06-2026     11:47                sessions                                                                                                                                              
-a----        13-06-2026     11:47            337 notes.md                                                                                                                                              
-a----        13-06-2026     11:47           4046 README.md                                                                                                                                             


PS C:\Users\abhis\Harika\K8s\Class2\LearningK8s> cd .\sessions\
PS C:\Users\abhis\Harika\K8s\Class2\LearningK8s\sessions> ls


    Directory: C:\Users\abhis\Harika\K8s\Class2\LearningK8s\sessions


Mode                 LastWriteTime         Length Name                                                                                                                                                  
----                 -------------         ------ ----                                                                                                                                                  
d-----        13-06-2026     11:47                01-core-k8s                                                                                                                                           


PS C:\Users\abhis\Harika\K8s\Class2\LearningK8s\sessions> cd .\01-core-k8s      
PS C:\Users\abhis\Harika\K8s\Class2\LearningK8s\sessions\01-core-k8s> ls


    Directory: C:\Users\abhis\Harika\K8s\Class2\LearningK8s\sessions\01-core-k8s


Mode                 LastWriteTime         Length Name                                                                                                                                                  
----                 -------------         ------ ----                                                                                                                                                  
d-----        13-06-2026     11:47                subsessions                                                                                                                                           
-a----        13-06-2026     11:47           2922 README.md                                                                                                                                             


PS C:\Users\abhis\Harika\K8s\Class2\LearningK8s\sessions\01-core-k8s> cd subsessions
PS C:\Users\abhis\Harika\K8s\Class2\LearningK8s\sessions\01-core-k8s\subsessions> ls


    Directory: C:\Users\abhis\Harika\K8s\Class2\LearningK8s\sessions\01-core-k8s\subsessions


Mode                 LastWriteTime         Length Name                                                                                                                                                  
----                 -------------         ------ ----                                                                                                                                                  
d-----        13-06-2026     11:47                01-namespace                                                                                                                                          
d-----        13-06-2026     11:47                02-configmap-secret                                                                                                                                   
d-----        13-06-2026     11:47                03-postgres-deployment-service                                                                                                                        
d-----        13-06-2026     11:47                04-flask-pod                                                                                                                                          
d-----        13-06-2026     11:47                05-flask-deployment                                                                                                                                   
d-----        13-06-2026     11:47                06-flask-services                                                                                                                                     


PS C:\Users\abhis\Harika\K8s\Class2\LearningK8s\sessions\01-core-k8s\subsessions> cd .\01-namespace\
PS C:\Users\abhis\Harika\K8s\Class2\LearningK8s\sessions\01-core-k8s\subsessions\01-namespace> ls


    Directory: C:\Users\abhis\Harika\K8s\Class2\LearningK8s\sessions\01-core-k8s\subsessions\01-namespace


Mode                 LastWriteTime         Length Name                                                                                                                                                  
----                 -------------         ------ ----                                                                                                                                                  
-a----        13-06-2026     11:51             64 01-namespace.yml                                                                                                                                      
-a----        13-06-2026     11:47           1137 README.md                                                                                                                                             


PS C:\Users\abhis\Harika\K8s\Class2\LearningK8s\sessions\01-core-k8s\subsessions\01-namespace> kubectl apply -f .\01-namespace.yml
namespace/app-core unchanged
PS C:\Users\abhis\Harika\K8s\Class2\LearningK8s\sessions\01-core-k8s\subsessions\01-namespace> cd ..
PS C:\Users\abhis\Harika\K8s\Class2\LearningK8s\sessions\01-core-k8s\subsessions> cd .\02-configmap-secret\
PS C:\Users\abhis\Harika\K8s\Class2\LearningK8s\sessions\01-core-k8s\subsessions\02-configmap-secret> ls


    Directory: C:\Users\abhis\Harika\K8s\Class2\LearningK8s\sessions\01-core-k8s\subsessions\02-configmap-secret


Mode                 LastWriteTime         Length Name                                                                                                                                                  
----                 -------------         ------ ----                                                                                                                                                  
-a----        13-06-2026     11:47            136 01-app-configmap.yml                                                                                                                                  
-a----        13-06-2026     11:47            239 02-app-secret.yml                                                                                                                                     
-a----        13-06-2026     11:47           1520 README.md                                                                                                                                             


PS C:\Users\abhis\Harika\K8s\Class2\LearningK8s\sessions\01-core-k8s\subsessions\02-configmap-secret> kubectl apply -f .\01-app-configmap.yml
configmap/app-config created
PS C:\Users\abhis\Harika\K8s\Class2\LearningK8s\sessions\01-core-k8s\subsessions\02-configmap-secret> kubectl apply -f Deployment.yaml .\02-app-secret.yml
error: Unexpected args: [.\02-app-secret.yml]
See 'kubectl apply -h' for help and examples
PS C:\Users\abhis\Harika\K8s\Class2\LearningK8s\sessions\01-core-k8s\subsessions\02-configmap-secret> kubectl apply -f .\02-app-secret.yml                
secret/app-secrets created
PS C:\Users\abhis\Harika\K8s\Class2\LearningK8s\sessions\01-core-k8s\subsessions\02-configmap-secret> cd ..
PS C:\Users\abhis\Harika\K8s\Class2\LearningK8s\sessions\01-core-k8s\subsessions> cd 03
cd : Cannot find path 'C:\Users\abhis\Harika\K8s\Class2\LearningK8s\sessions\01-core-k8s\subsessions\03' because it does not exist.
At line:1 char:1
+ cd 03
+ ~~~~~
    + CategoryInfo          : ObjectNotFound: (C:\Users\abhis\...\subsessions\03:String) [Set-Location], ItemNotFoundException
    + FullyQualifiedErrorId : PathNotFound,Microsoft.PowerShell.Commands.SetLocationCommand
 
PS C:\Users\abhis\Harika\K8s\Class2\LearningK8s\sessions\01-core-k8s\subsessions> cd .\03-postgres-deployment-service\
PS C:\Users\abhis\Harika\K8s\Class2\LearningK8s\sessions\01-core-k8s\subsessions\03-postgres-deployment-service> ls


    Directory: C:\Users\abhis\Harika\K8s\Class2\LearningK8s\sessions\01-core-k8s\subsessions\03-postgres-deployment-service


Mode                 LastWriteTime         Length Name                                                                                                                                                  
----                 -------------         ------ ----                                                                                                                                                  
-a----        13-06-2026     11:47           1718 01-postgres-deployment.yml                                                                                                                            
-a----        13-06-2026     11:47            233 02-postgres-service.yml                                                                                                                               
-a----        13-06-2026     11:47           1728 README.md                                                                                                                                             


PS C:\Users\abhis\Harika\K8s\Class2\LearningK8s\sessions\01-core-k8s\subsessions\03-postgres-deployment-service> kubectl apply -f .\01-postgres-deployment.yml
deployment.apps/postgres created
PS C:\Users\abhis\Harika\K8s\Class2\LearningK8s\sessions\01-core-k8s\subsessions\03-postgres-deployment-service> kubectl apply -f .\02-postgres-service.yml
service/postgres created
PS C:\Users\abhis\Harika\K8s\Class2\LearningK8s\sessions\01-core-k8s\subsessions\03-postgres-deployment-service> cd ..
PS C:\Users\abhis\Harika\K8s\Class2\LearningK8s\sessions\01-core-k8s\subsessions> cd .\04-flask-pod\
PS C:\Users\abhis\Harika\K8s\Class2\LearningK8s\sessions\01-core-k8s\subsessions\04-flask-pod> ls


    Directory: C:\Users\abhis\Harika\K8s\Class2\LearningK8s\sessions\01-core-k8s\subsessions\04-flask-pod


Mode                 LastWriteTime         Length Name                                                                                                                                                  
----                 -------------         ------ ----                                                                                                                                                  
-a----        13-06-2026     11:47           1350 01-flask-pod.yml                                                                                                                                      
-a----        13-06-2026     11:47           1485 README.md                                                                                                                                             


PS C:\Users\abhis\Harika\K8s\Class2\LearningK8s\sessions\01-core-k8s\subsessions\04-flask-pod> kubectl apply -f .\01-flask-pod.yml
pod/flask-pod created
PS C:\Users\abhis\Harika\K8s\Class2\LearningK8s\sessions\01-core-k8s\subsessions\04-flask-pod> cd ..
PS C:\Users\abhis\Harika\K8s\Class2\LearningK8s\sessions\01-core-k8s\subsessions> cd .\05-flask-deployment\
PS C:\Users\abhis\Harika\K8s\Class2\LearningK8s\sessions\01-core-k8s\subsessions\05-flask-deployment> ls


    Directory: C:\Users\abhis\Harika\K8s\Class2\LearningK8s\sessions\01-core-k8s\subsessions\05-flask-deployment


Mode                 LastWriteTime         Length Name                                                                                                                                                  
----                 -------------         ------ ----                                                                                                                                                  
-a----        13-06-2026     11:47           2082 01-flask-deployment.yml                                                                                                                               
-a----        13-06-2026     11:47           1592 README.md                                                                                                                                             


PS C:\Users\abhis\Harika\K8s\Class2\LearningK8s\sessions\01-core-k8s\subsessions\05-flask-deployment> kubectl apply -f .\01-flask-deployment.yml
deployment.apps/flask-web created
PS C:\Users\abhis\Harika\K8s\Class2\LearningK8s\sessions\01-core-k8s\subsessions\05-flask-deployment> cd ..
PS C:\Users\abhis\Harika\K8s\Class2\LearningK8s\sessions\01-core-k8s\subsessions> cd .\06-flask-services\
PS C:\Users\abhis\Harika\K8s\Class2\LearningK8s\sessions\01-core-k8s\subsessions\06-flask-services> ls                                               


    Directory: C:\Users\abhis\Harika\K8s\Class2\LearningK8s\sessions\01-core-k8s\subsessions\06-flask-services


Mode                 LastWriteTime         Length Name                                                                                                                                                  
----                 -------------         ------ ----                                                                                                                                                  
-a----        13-06-2026     11:47            267 01-flask-service-clusterip.yml                                                                                                                        
-a----        13-06-2026     11:47            288 02-flask-service-nodeport.yml                                                                                                                         
-a----        13-06-2026     11:47            273 03-flask-service-loadbalancer.yml                                                                                                                     
-a----        13-06-2026     11:47           2036 README.md                                                                                                                                             


PS C:\Users\abhis\Harika\K8s\Class2\LearningK8s\sessions\01-core-k8s\subsessions\06-flask-services> kubectl apply -f .\01-flask-service-clusterip.yml
service/flask-web-clusterip created
PS C:\Users\abhis\Harika\K8s\Class2\LearningK8s\sessions\01-core-k8s\subsessions\06-flask-services> kubectl apply -f .\02-flask-service-nodeport.yml
service/flask-web-nodeport created
PS C:\Users\abhis\Harika\K8s\Class2\LearningK8s\sessions\01-core-k8s\subsessions\06-flask-services> kubectl apply -f .\03-flask-service-loadbalancer.yml
service/flask-web-loadbalancer created
PS C:\Users\abhis\Harika\K8s\Class2\LearningK8s\sessions\01-core-k8s\subsessions\06-flask-services> 
```
> <img width="1575" height="646" alt="image" src="https://github.com/user-attachments/assets/f9a2815d-8602-4e1a-941a-eceb1dd60aca" />


<img width="1683" height="890" alt="image" src="https://github.com/user-attachments/assets/967112c4-1529-4ae0-aabb-c517b96ae87c" />


<img width="1642" height="343" alt="image" src="https://github.com/user-attachments/assets/9413d612-5156-44f4-b704-21e5ac1265e7" />

```
PS C:\Users\abhis\Harika\K8s\Class2\LearningK8s> kubectl get svc
NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.100.0.1   <none>        443/TCP   110m
PS C:\Users\abhis\Harika\K8s\Class2\LearningK8s> kubectl get services
NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.100.0.1   <none>        443/TCP   113m
PS C:\Users\abhis\Harika\K8s\Class2\LearningK8s> kubectl config get-context            
error: unknown command "get-context"
See 'kubectl config -h' for help and examples
PS C:\Users\abhis\Harika\K8s\Class2\LearningK8s> kubectl get svc -n app-code
No resources found in app-code namespace.
PS C:\Users\abhis\Harika\K8s\Class2\LearningK8s> kubectl get svc -n app-core
NAME                     TYPE           CLUSTER-IP       EXTERNAL-IP                                                              PORT(S)        AGE
flask-web-clusterip      ClusterIP      10.100.170.145   <none>                                                                   80/TCP         6m54s
flask-web-loadbalancer   LoadBalancer   10.100.7.181     ac2cd384f97964fa9a811db1faf3ef76-23462951.ap-south-1.elb.amazonaws.com   80:30854/TCP   6m41s
flask-web-nodeport       NodePort       10.100.179.9     <none>                                                                   80:30080/TCP   6m47s
postgres                 ClusterIP      10.100.185.228   <none>                                                                   5432/TCP       7m55s
PS C:\Users\abhis\Harika\K8s\Class2\LearningK8s> kubectl get pods
No resources found in default namespace.
PS C:\Users\abhis\Harika\K8s\Class2\LearningK8s> kubectl get pods -n app-core
NAME                         READY   STATUS    RESTARTS   AGE
flask-pod                    1/1     Running   0          9m36s
flask-web-6b6f976596-b7tdb   1/1     Running   0          9m20s
flask-web-6b6f976596-stjdm   1/1     Running   0          9m20s
postgres-5cf755b76f-zzfn6    1/1     Running   0          9m55s
PS C:\Users\abhis\Harika\K8s\Class2\LearningK8s> kubectl config get-context  
error: unknown command "get-context"
See 'kubectl config -h' for help and examples
PS C:\Users\abhis\Harika\K8s\Class2\LearningK8s> kubectl config get-contexts
CURRENT   NAME                                                              CLUSTER                                                           AUTHINFO                                                          NAMESPACE
          admincliprac@sample-cluster-cls2.ap-south-1.eksctl.io             sample-cluster-cls2.ap-south-1.eksctl.io                          admincliprac@sample-cluster-cls2.ap-south-1.eksctl.io             
          arn:aws:eks:ap-south-1:129373676098:cluster/sample-cluster-3      arn:aws:eks:ap-south-1:129373676098:cluster/sample-cluster-3      arn:aws:eks:ap-south-1:129373676098:cluster/sample-cluster-3      
*         arn:aws:eks:ap-south-1:129373676098:cluster/sample-cluster-cls2   arn:aws:eks:ap-south-1:129373676098:cluster/sample-cluster-cls2   arn:aws:eks:ap-south-1:129373676098:cluster/sample-cluster-cls2   
          docker-desktop                                                    docker-desktop                                                    docker-desktop                                                    
PS C:\Users\abhis\Harika\K8s\Class2\LearningK8s> kubectl get namespaces
NAME              STATUS   AGE
app-core          Active   24m
default           Active   123m
kube-node-lease   Active   123m
kube-public       Active   123m
kube-system       Active   123m
PS C:\Users\abhis\Harika\K8s\Class2\LearningK8s>
PS C:\Users\abhis\Harika\K8s\Class2\LearningK8s> kubectl get service flask-web-loadbalancer -n app-core
NAME                     TYPE           CLUSTER-IP     EXTERNAL-IP                                                              PORT(S)        AGE
flask-web-loadbalancer   LoadBalancer   10.100.7.181   ac2cd384f97964fa9a811db1faf3ef76-23462951.ap-south-1.elb.amazonaws.com   80:30854/TCP   18m      kubectl config set-context --current --namespace=app-coreC:\Users\abhis\Harika\K8s\Class2\LearningK8s> 
Context "arn:aws:eks:ap-south-1:129373676098:cluster/sample-cluster-cls2" modified.
PS C:\Users\abhis\Harika\K8s\Class2\LearningK8s> kubectl config set-context --current --namespace=app-core
Context "arn:aws:eks:ap-south-1:129373676098:cluster/sample-cluster-cls2" modified.
PS C:\Users\abhis\Harika\K8s\Class2\LearningK8s> kubectl get services
NAME                     TYPE           CLUSTER-IP       EXTERNAL-IP                                                              PORT(S)        AGE
flask-web-clusterip      ClusterIP      10.100.170.145   <none>                                                                   80/TCP         21m
flask-web-loadbalancer   LoadBalancer   10.100.7.181     ac2cd384f97964fa9a811db1faf3ef76-23462951.ap-south-1.elb.amazonaws.com   80:30854/TCP   21m
flask-web-nodeport       NodePort       10.100.179.9     <none>                                                                   80:30080/TCP   21m
postgres                 ClusterIP      10.100.185.228   <none>                                                                   5432/TCP       22m
PS C:\Users\abhis\Harika\K8s\Class2\LearningK8s> 
```

<img width="1603" height="730" alt="image" src="https://github.com/user-attachments/assets/a0f6b6e7-fa79-4048-a557-a3e95dbd2bab" />

```
PS C:\Users\abhis\Harika\K8s\Class2\LearningK8s> kubectl config set-context --current --namespace=app-core
Context "arn:aws:eks:ap-south-1:129373676098:cluster/sample-cluster-cls2" modified.
PS C:\Users\abhis\Harika\K8s\Class2\LearningK8s> kubectl get services
NAME                     TYPE           CLUSTER-IP       EXTERNAL-IP                                                              PORT(S)        AGE
flask-web-clusterip      ClusterIP      10.100.170.145   <none>                                                                   80/TCP         21m
flask-web-loadbalancer   LoadBalancer   10.100.7.181     ac2cd384f97964fa9a811db1faf3ef76-23462951.ap-south-1.elb.amazonaws.com   80:30854/TCP   21m
flask-web-nodeport       NodePort       10.100.179.9     <none>                                                                   80:30080/TCP   21m
postgres                 ClusterIP      10.100.185.228   <none>                                                                   5432/TCP       22m
PS C:\Users\abhis\Harika\K8s\Class2\LearningK8s> kubectl get all -n app-core
NAME                             READY   STATUS    RESTARTS   AGE
pod/flask-web-6b6f976596-jsf6z   1/1     Running   0          16m
pod/flask-web-6b6f976596-z4w6b   1/1     Running   0          16m
pod/postgres-5cf755b76f-xcbrc    1/1     Running   0          16m

NAME                             TYPE           CLUSTER-IP       EXTERNAL-IP                                                              PORT(S)        AGE
service/flask-web-clusterip      ClusterIP      10.100.170.145   <none>                                                                   80/TCP         25m
service/flask-web-loadbalancer   LoadBalancer   10.100.7.181     ac2cd384f97964fa9a811db1faf3ef76-23462951.ap-south-1.elb.amazonaws.com   80:30854/TCP   25m
service/flask-web-nodeport       NodePort       10.100.179.9     <none>                                                                   80:30080/TCP   25m
service/postgres                 ClusterIP      10.100.185.228   <none>                                                                   5432/TCP       26m

NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/flask-web   2/2     2            2           26m
deployment.apps/postgres    1/1     1            1           26m

NAME                                   DESIRED   CURRENT   READY   AGE
replicaset.apps/flask-web-6b6f976596   2         2         2       26m
replicaset.apps/postgres-5cf755b76f    1         1         1       26m
PS C:\Users\abhis\Harika\K8s\Class2\LearningK8s> kubectl get configmap -n app-core
NAME               DATA   AGE
app-config         2      30m
kube-root-ca.crt   1      38m
PS C:\Users\abhis\Harika\K8s\Class2\LearningK8s> kubectl get secret -n app-core   
NAME          TYPE     DATA   AGE
app-secrets   Opaque   4      30m
PS C:\Users\abhis\Harika\K8s\Class2\LearningK8s> kubectl describe configmap app-config -n app-core
Name:         app-config
Namespace:    app-core
Labels:       <none>
Annotations:  <none>

Data
====
DB_HOST:
----
postgres

DB_PORT:
----
5432


BinaryData
====

Events:  <none>
PS C:\Users\abhis\Harika\K8s\Class2\LearningK8s> kubectl describe secret app-secrets -n app-core
Name:         app-secrets
Namespace:    app-core
Labels:       <none>
Annotations:  <none>

Type:  Opaque

Data
====
FLASK_SECRET_KEY:   21 bytes
POSTGRES_DB:        5 bytes
POSTGRES_PASSWORD:  11 bytes
POSTGRES_USER:      7 bytes
PS C:\Users\abhis\Harika\K8s\Class2\LearningK8s> kubectl get deployment -n app-core
NAME        READY   UP-TO-DATE   AVAILABLE   AGE
flask-web   2/2     2            2           31m
postgres    1/1     1            1           32m
PS C:\Users\abhis\Harika\K8s\Class2\LearningK8s> kubectl get pods -n app-core -l app=postgres
NAME                        READY   STATUS    RESTARTS   AGE
postgres-5cf755b76f-xcbrc   1/1     Running   0          21m
PS C:\Users\abhis\Harika\K8s\Class2\LearningK8s> kubectl get service postgres -n app-core
NAME       TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)    AGE
postgres   ClusterIP   10.100.185.228   <none>        5432/TCP   32m
PS C:\Users\abhis\Harika\K8s\Class2\LearningK8s> kubectl get endpoints postgres -n app-core
Warning: v1 Endpoints is deprecated in v1.33+; use discovery.k8s.io/v1 EndpointSlice
NAME       ENDPOINTS             AGE
postgres   192.168.76.244:5432   32m
PS C:\Users\abhis\Harika\K8s\Class2\LearningK8s> kubectl get pods
NAME                         READY   STATUS    RESTARTS   AGE
flask-web-6b6f976596-jsf6z   1/1     Running   0          22m
flask-web-6b6f976596-z4w6b   1/1     Running   0          22m
postgres-5cf755b76f-xcbrc    1/1     Running   0          22m
PS C:\Users\abhis\Harika\K8s\Class2\LearningK8s> kubectl delete pod postgres-5cf755b76f-xcbrc 
pod "postgres-5cf755b76f-xcbrc" deleted from app-core namespace
PS C:\Users\abhis\Harika\K8s\Class2\LearningK8s> kubectl get pods                            
NAME                         READY   STATUS    RESTARTS   AGE
flask-web-6b6f976596-jsf6z   1/1     Running   0          24m
flask-web-6b6f976596-z4w6b   1/1     Running   0          24m
postgres-5cf755b76f-h95hx    1/1     Running   0          48s
PS C:\Users\abhis\Harika\K8s\Class2\LearningK8s>
```

<img width="1633" height="778" alt="image" src="https://github.com/user-attachments/assets/eb8b6f0d-86cc-41d7-9d42-da6d40f58e85" />

<img width="1579" height="762" alt="image" src="https://github.com/user-attachments/assets/5d4d8b97-c51b-42d1-9c6d-483fa754dccb" />


# Mandatory to run in the end
eksctl delete cluster --name sample-cluster-cls2 --region ap-south-1
