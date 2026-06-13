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




eksctl delete cluster --name sample-cluster-cls2 --region ap-south-1
