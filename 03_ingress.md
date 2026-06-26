
# Ingress

```
eksctl create cluster --name prac-cluster-ingress --region ap-south-1 --nodegroup-name standard-workers --node-type t3.medium --nodes 2 --nodes-min 2 --nodes-max 3

aws eks --region ap-south-1 update-kubeconfig --name prac-cluster-ingress

kubectl config get-contexts

eksctl delete cluster --name prac-cluster-ingress --region ap-south-1
```

## pre-requisites

> <img width="768" height="592" alt="image" src="https://github.com/user-attachments/assets/c746619b-d743-4c1f-ac46-0e8528c4ed06" />


## 6 Types of additional Ingress Providers

> <img width="767" height="919" alt="image" src="https://github.com/user-attachments/assets/cd3b05a7-8885-4da7-b406-575e1edaa953" />

## 01 - shared config

```
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\03-ingress> kubectl apply -f subsessions/01-shared-config/
namespace/app-ingress created
configmap/app-config created
secret/app-secrets created
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\03-ingress> kubectl get namespace app-ingress
NAME          STATUS   AGE
app-ingress   Active   7s
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\03-ingress> kubectl get configmap -n app-ingress
NAME               DATA   AGE
app-config         2      12s
kube-root-ca.crt   1      12s
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\03-ingress> kubectl get secret -n app-ingress
NAME          TYPE     DATA   AGE
app-secrets   Opaque   4      19s
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\03-ingress> kubectl get all -n app-ingress
No resources found in app-ingress namespace.
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\03-ingress>
```

## 02 Storage Class
```
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\03-ingress> kubectl apply -f subsessions/02-storageclass/
storageclass.storage.k8s.io/ebs-gp3 created
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\03-ingress> kubectl get storageclass ebs-gp3
NAME      PROVISIONER       RECLAIMPOLICY   VOLUMEBINDINGMODE      ALLOWVOLUMEEXPANSION   AGE
ebs-gp3   ebs.csi.aws.com   Delete          WaitForFirstConsumer   true                   11s
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\03-ingress> kubectl descrive storageclass       
error: unknown command "descrive" for "kubectl"

Did you mean this?
        describe
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\03-ingress> kubectl describe storageclass
Name:            ebs-gp3
IsDefaultClass:  No
Annotations:     kubectl.kubernetes.io/last-applied-configuration={"allowVolumeExpansion":true,"apiVersion":"storage.k8s.io/v1","kind":"StorageClass","metadata":{"annotations":{},"name":"ebs-gp3"},"parameters":{"fsType":"ext4","type":"gp3"},"provisioner":"ebs.csi.aws.com","reclaimPolicy":"Delete","volumeBindingMode":"WaitForFirstConsumer"}

Provisioner:           ebs.csi.aws.com
Parameters:            fsType=ext4,type=gp3
AllowVolumeExpansion:  True
MountOptions:          <none>
ReclaimPolicy:         Delete
VolumeBindingMode:     WaitForFirstConsumer
Events:                <none>


Name:            gp2
IsDefaultClass:  No
Annotations:     kubectl.kubernetes.io/last-applied-configuration={"apiVersion":"storage.k8s.io/v1","kind":"StorageClass","metadata":{"annotations":{},"name":"gp2"},"parameters":{"fsType":"ext4","type":"gp2"},"provisioner":"kubernetes.io/aws-ebs","volumeBindingMode":"WaitForFirstConsumer"}

Provisioner:           kubernetes.io/aws-ebs
Parameters:            fsType=ext4,type=gp2
AllowVolumeExpansion:  <unset>
MountOptions:          <none>
ReclaimPolicy:         Delete
VolumeBindingMode:     WaitForFirstConsumer
Events:                <none>
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\03-ingress>
```
> <img width="1529" height="815" alt="image" src="https://github.com/user-attachments/assets/0398e04e-3075-4503-bad7-289ec09fe0f2" />


## 03 postgresql statefulset
```
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\03-ingress> kubectl get all -n app-ingress
No resources found in app-ingress namespace.
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\03-ingress> kubectl apply -f subsessions/03-postgres-statefulset/
service/postgres-headless created
service/postgres created
statefulset.apps/postgres-stateful created
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\03-ingress> kubectl get statefulset -n app-ingress
NAME                READY   AGE
postgres-stateful   0/1     15s
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\03-ingress> kubectl describe statefulset -n app-ingress
Name:               postgres-stateful
Namespace:          app-ingress
CreationTimestamp:  Fri, 26 Jun 2026 16:09:55 +0530
Selector:           app=postgres,session=ingress,tier=data
Labels:             app=postgres
                    session=ingress
                    tier=data
Annotations:        <none>
Replicas:           1 desired | 1 total
Update Strategy:    RollingUpdate
  Partition:        0
Pods Status:        0 Running / 1 Waiting / 0 Succeeded / 0 Failed
Pod Template:
  Labels:  app=postgres
           session=ingress
           tier=data
  Containers:
   postgres:
    Image:      postgres:16-alpine
    Port:       5432/TCP (postgres)
    Host Port:  0/TCP (postgres)
    Limits:
      cpu:     500m
      memory:  512Mi
    Requests:
      cpu:      100m
      memory:   256Mi
    Liveness:   exec [sh -c pg_isready -U "$POSTGRES_USER" -d "$POSTGRES_DB"] delay=20s timeout=1s period=10s #success=1 #failure=3
    Readiness:  exec [sh -c pg_isready -U "$POSTGRES_USER" -d "$POSTGRES_DB"] delay=8s timeout=1s period=5s #success=1 #failure=3
    Environment:
      POSTGRES_DB:        <set to the key 'POSTGRES_DB' in secret 'app-secrets'>        Optional: false
      POSTGRES_USER:      <set to the key 'POSTGRES_USER' in secret 'app-secrets'>      Optional: false
      POSTGRES_PASSWORD:  <set to the key 'POSTGRES_PASSWORD' in secret 'app-secrets'>  Optional: false
      PGDATA:             /var/lib/postgresql/data/pgdata
    Mounts:
      /var/lib/postgresql/data from postgres-data (rw)
  Volumes:         <none>
  Node-Selectors:  <none>
  Tolerations:     <none>
Volume Claims:
  Name:          postgres-data
  StorageClass:  ebs-gp3
  Labels:        app=postgres
                 session=ingress
  Annotations:   <none>
  Capacity:      1Gi
  Access Modes:  [ReadWriteOnce]
Events:
  Type    Reason            Age   From                    Message
  ----    ------            ----  ----                    -------
  Normal  SuccessfulCreate  26s   statefulset-controller  create Claim postgres-data-postgres-stateful-0 Pod postgres-stateful-0 in StatefulSet postgres-stateful success
  Normal  SuccessfulCreate  26s   statefulset-controller  create Pod postgres-stateful-0 in StatefulSet postgres-stateful successful
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\03-ingress> kubectl get pods -n app-ingress -l app=postgres,session=ingress
NAME                  READY   STATUS    RESTARTS   AGE
postgres-stateful-0   0/1     Pending   0          38s
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\03-ingress> kubectl get all -n app-ingress
NAME                      READY   STATUS    RESTARTS   AGE
pod/postgres-stateful-0   0/1     Pending   0          48s

NAME                        TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)    AGE
service/postgres            ClusterIP   10.100.117.113   <none>        5432/TCP   48s
service/postgres-headless   ClusterIP   None             <none>        5432/TCP   49s

NAME                                 READY   AGE
statefulset.apps/postgres-stateful   0/1     48s
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\03-ingress> kubectl get pods -n app-ingress -l app=postgres,session=ingress
NAME                  READY   STATUS    RESTARTS   AGE
postgres-stateful-0   0/1     Pending   0          65s
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\03-ingress> kubectl get pvc -n app-ingress
NAME                                STATUS    VOLUME   CAPACITY   ACCESS MODES   STORAGECLASS   VOLUMEATTRIBUTESCLASS   AGE
postgres-data-postgres-stateful-0   Pending                                      ebs-gp3        <unset>                 75s
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\03-ingress> kubectl describe pvc -n app-ingress
Name:          postgres-data-postgres-stateful-0
Namespace:     app-ingress
StorageClass:  ebs-gp3
Status:        Pending
Volume:        
Labels:        app=postgres
               session=ingress
               tier=data
Annotations:   volume.beta.kubernetes.io/storage-provisioner: ebs.csi.aws.com
               volume.kubernetes.io/selected-node: ip-192-168-66-62.ap-south-1.compute.internal
               volume.kubernetes.io/storage-provisioner: ebs.csi.aws.com
Finalizers:    [kubernetes.io/pvc-protection]
Capacity:      
Access Modes:  
VolumeMode:    Filesystem
Used By:       postgres-stateful-0
Events:
  Type    Reason                Age                From                         Message
  ----    ------                ----               ----                         -------
  Normal  WaitForFirstConsumer  85s                persistentvolume-controller  waiting for first consumer to be created before binding
  Normal  ExternalProvisioning  15s (x7 over 85s)  persistentvolume-controller  Waiting for a volume to be created either by the external provisioner 'ebs.csi.aws.com' or manually by the system administrator. If volume creation is delayed, please verify that the provisioner is running and correctly registered.
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\03-ingress> kubectl describe pods -n app-ingress -l app=postgres,session=ingress
Name:             postgres-stateful-0
Namespace:        app-ingress
Priority:         0
Service Account:  default
Node:             <none>
Labels:           app=postgres
                  apps.kubernetes.io/pod-index=0
                  controller-revision-hash=postgres-stateful-75956fd76c
                  session=ingress
                  statefulset.kubernetes.io/pod-name=postgres-stateful-0
                  tier=data
Annotations:      <none>
Status:           Pending
IP:               
IPs:              <none>
Controlled By:    StatefulSet/postgres-stateful
Containers:
  postgres:
    Image:      postgres:16-alpine
    Port:       5432/TCP (postgres)
    Host Port:  0/TCP (postgres)
    Limits:
      cpu:     500m
      memory:  512Mi
    Requests:
      cpu:      100m
      memory:   256Mi
    Liveness:   exec [sh -c pg_isready -U "$POSTGRES_USER" -d "$POSTGRES_DB"] delay=20s timeout=1s period=10s #success=1 #failure=3
    Readiness:  exec [sh -c pg_isready -U "$POSTGRES_USER" -d "$POSTGRES_DB"] delay=8s timeout=1s period=5s #success=1 #failure=3
    Environment:
      POSTGRES_DB:        <set to the key 'POSTGRES_DB' in secret 'app-secrets'>        Optional: false
      POSTGRES_USER:      <set to the key 'POSTGRES_USER' in secret 'app-secrets'>      Optional: false
      POSTGRES_PASSWORD:  <set to the key 'POSTGRES_PASSWORD' in secret 'app-secrets'>  Optional: false
      PGDATA:             /var/lib/postgresql/data/pgdata
    Mounts:
      /var/lib/postgresql/data from postgres-data (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-dqhct (ro)
Volumes:
  postgres-data:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  postgres-data-postgres-stateful-0
    ReadOnly:   false
  kube-api-access-dqhct:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    Optional:                false
    DownwardAPI:             true
QoS Class:                   Burstable
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:                      <none>
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\03-ingress>
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\03-ingress> kubectl get all -n app-ingress
NAME                      READY   STATUS    RESTARTS   AGE
pod/postgres-stateful-0   0/1     Pending   0          4m58s

NAME                        TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)    AGE
service/postgres            ClusterIP   10.100.117.113   <none>        5432/TCP   4m58s
service/postgres-headless   ClusterIP   None             <none>        5432/TCP   4m59s

NAME                                 READY   AGE
statefulset.apps/postgres-stateful   0/1     4m58s
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\03-ingress> 
```

## 04 - api microservices
```
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\03-ingress> kubectl apply -f subsessions/04-api-microservices/
deployment.apps/user-service created
service/user-service created
deployment.apps/app-service created
service/app-service created
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\03-ingress> kubectl get deployment -n app-ingress
NAME           READY   UP-TO-DATE   AVAILABLE   AGE
app-service    0/2     2            0           8s
user-service   0/2     2            0           8s
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\03-ingress> kubectl get service -n app-ingress
NAME                TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)    AGE
app-service         ClusterIP   10.100.144.53    <none>        5002/TCP   11s
postgres            ClusterIP   10.100.117.113   <none>        5432/TCP   6m12s
postgres-headless   ClusterIP   None             <none>        5432/TCP   6m13s
user-service        ClusterIP   10.100.142.79    <none>        5001/TCP   12s
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\03-ingress> kubectl get endpoints -n app-ingress
Warning: v1 Endpoints is deprecated in v1.33+; use discovery.k8s.io/v1 EndpointSlice
NAME                ENDPOINTS   AGE
app-service                     18s
postgres            <none>      6m19s
postgres-headless   <none>      6m20s
user-service                    19s
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\03-ingress> kubectl port-forward -n app-ingress service/user-service 8081:5001
Forwarding from 127.0.0.1:8081 -> 5001
Forwarding from [::1]:8081 -> 5001
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\03-ingress> curl http://localhost:8081/api/users
curl : Unable to connect to the remote server
At line:1 char:1
+ curl http://localhost:8081/api/users
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : InvalidOperation: (System.Net.HttpWebRequest:HttpWebRequest) [Invoke-WebRequest], WebException
    + FullyQualifiedErrorId : WebCmdletWebResponseException,Microsoft.PowerShell.Commands.InvokeWebRequestCommand
 
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\03-ingress> kubectl port-forward -n app-ingress service/app-service 8082:5002
Forwarding from 127.0.0.1:8082 -> 5002
Forwarding from [::1]:8082 -> 5002

```

## 05 frontend
```
S C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\03-ingress> kubectl apply -f subsessions/05-frontend/
deployment.apps/frontend created
service/frontend created
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\03-ingress> kubectl get deployment frontend -n app-ingress
NAME       READY   UP-TO-DATE   AVAILABLE   AGE
frontend   0/2     2            0           8s
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\03-ingress> kubectl get pods -n app-ingress -l app=frontend
NAME                       READY   STATUS    RESTARTS   AGE
frontend-844488469-gfs75   0/1     Running   0          15s
frontend-844488469-vsdzw   0/1     Running   0          15s
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\03-ingress> kubectl get service frontend -n app-ingress
NAME       TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)   AGE
frontend   ClusterIP   10.100.200.191   <none>        80/TCP    23s
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\03-ingress> kubectl port-forward -n app-ingress service/frontend 8080:80
Forwarding from 127.0.0.1:8080 -> 5000
Forwarding from [::1]:8080 -> 5000


PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s> kubectl get all -n app-ingress
NAME                                READY   STATUS    RESTARTS      AGE
pod/app-service-8667945846-742vw    0/1     Running   4 (25s ago)   4m45s
pod/app-service-8667945846-v2xc7    0/1     Running   4 (26s ago)   4m44s
pod/frontend-844488469-gfs75        0/1     Running   0             48s
pod/frontend-844488469-vsdzw        0/1     Running   0             48s
pod/postgres-stateful-0             0/1     Pending   0             10m
pod/user-service-858b4897dd-7ct87   0/1     Running   4 (25s ago)   4m45s
pod/user-service-858b4897dd-hnczj   0/1     Running   4 (26s ago)   4m45s

NAME                        TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)    AGE
service/app-service         ClusterIP   10.100.144.53    <none>        5002/TCP   4m44s
service/frontend            ClusterIP   10.100.200.191   <none>        80/TCP     48s
service/postgres            ClusterIP   10.100.117.113   <none>        5432/TCP   10m
service/postgres-headless   ClusterIP   None             <none>        5432/TCP   10m
service/user-service        ClusterIP   10.100.142.79    <none>        5001/TCP   4m45s

NAME                           READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/app-service    0/2     2            0           4m45s
deployment.apps/frontend       0/2     2            0           48s
deployment.apps/user-service   0/2     2            0           4m45s

NAME                                      DESIRED   CURRENT   READY   AGE
replicaset.apps/app-service-8667945846    2         2         0       4m45s
replicaset.apps/frontend-844488469        2         2         0       48s
replicaset.apps/user-service-858b4897dd   2         2         0       4m45s

NAME                                 READY   AGE
statefulset.apps/postgres-stateful   0/1     10m
```
> <img width="1434" height="745" alt="image" src="https://github.com/user-attachments/assets/de81d876-17cd-4db2-abe4-9725be915157" />
enter some randome data
> <img width="1421" height="789" alt="image" src="https://github.com/user-attachments/assets/62e71e1c-e202-4a0b-90af-dc815ed9ac4c" />

## 06 - Message board Ingress
```

abhis@Bharathgift MINGW64 ~/Harika/K8s/Practice/LearningK8s (main)
$ cd sessions/03-ingress/subsessions/

abhis@Bharathgift MINGW64 ~/Harika/K8s/Practice/LearningK8s/sessions/03-ingress/subsessions (main)
$ export CLUSTER_NAME=prac-cluster-ingress
export AWS_REGION=ap-south-1
export VPC_ID=$(aws eks describe-cluster \
  --name "$CLUSTER_NAME" \
  --region "$AWS_REGION" \
  --query "cluster.resourcesVpcConfig.vpcId" \
  --output text)

abhis@Bharathgift MINGW64 ~/Harika/K8s/Practice/LearningK8s/sessions/03-ingress/subsessions (main)
$ eksctl utils associate-iam-oidc-provider \
  --cluster "$CLUSTER_NAME" \
  --region "$AWS_REGION" \
  --approve
2026-06-26 16:32:09 [ℹ]  will create IAM Open ID Connect provider for cluster "prac-cluster-ingress" in "ap-south-1"
2026-06-26 16:32:09 [✔]  created IAM Open ID Connect provider for cluster "prac-cluster-ingress" in "ap-south-1"

abhis@Bharathgift MINGW64 ~/Harika/K8s/Practice/LearningK8s/sessions/03-ingress/subsessions (main)
$ curl -o iam_policy.json https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/main/docs/install/iam_policy.json
  % Total    % Received % Xferd  Average Speed  Time    Time    Time   Current
                                 Dload  Upload  Total   Spent   Left   Speed
100   8955 100   8955   0      0   1174      0   00:07   00:07           1666

abhis@Bharathgift MINGW64 ~/Harika/K8s/Practice/LearningK8s/sessions/03-ingress/subsessions (main)
$ aws iam create-policy \
  --policy-name AWSLoadBalancerControllerIAMPolicy \
  --policy-document file://iam_policy.json
{                                                                                                                                                                                      
    "Policy": {
        "PolicyName": "AWSLoadBalancerControllerIAMPolicy",
        "PolicyId": "ANPAR4H2FUJBNWQNODFL6",
        "Arn": "arn:aws:iam::129373676098:policy/AWSLoadBalancerControllerIAMPolicy",
        "Path": "/",
        "DefaultVersionId": "v1",
        "AttachmentCount": 0,
        "PermissionsBoundaryUsageCount": 0,
        "IsAttachable": true,
        "CreateDate": "2026-06-26T11:03:00+00:00",
        "UpdateDate": "2026-06-26T11:03:00+00:00"
    }
}

abhis@Bharathgift MINGW64 ~/Harika/K8s/Practice/LearningK8s/sessions/03-ingress/subsessions (main)
$ export AWS_ACCOUNT_ID=$(aws sts get-caller-identity --query Account --output text)

eksctl create iamserviceaccount \
  --cluster "$CLUSTER_NAME" \
  --region "$AWS_REGION" \
  --namespace kube-system \
  --name aws-load-balancer-controller \
  --role-name AmazonEKSLoadBalancerControllerRole \
  --attach-policy-arn arn:aws:iam::$AWS_ACCOUNT_ID:policy/AWSLoadBalancerControllerIAMPolicy \
  --approve
2026-06-26 16:33:57 [ℹ]  1 iamserviceaccount (kube-system/aws-load-balancer-controller) was included (based on the include/exclude rules)
2026-06-26 16:33:57 [!]  serviceaccounts that exist in Kubernetes will be excluded, use --override-existing-serviceaccounts to override
2026-06-26 16:33:57 [ℹ]  1 task: { 
    2 sequential sub-tasks: { 
        create IAM role for serviceaccount "kube-system/aws-load-balancer-controller",
        create serviceaccount "kube-system/aws-load-balancer-controller",
    } }2026-06-26 16:33:57 [ℹ]  building iamserviceaccount stack "eksctl-prac-cluster-ingress-addon-iamserviceaccount-kube-system-aws-load-balancer-controller"
2026-06-26 16:33:58 [ℹ]  deploying stack "eksctl-prac-cluster-ingress-addon-iamserviceaccount-kube-system-aws-load-balancer-controller"
2026-06-26 16:33:58 [ℹ]  waiting for CloudFormation stack "eksctl-prac-cluster-ingress-addon-iamserviceaccount-kube-system-aws-load-balancer-controller"
2026-06-26 16:34:28 [ℹ]  waiting for CloudFormation stack "eksctl-prac-cluster-ingress-addon-iamserviceaccount-kube-system-aws-load-balancer-controller"
2026-06-26 16:34:28 [ℹ]  created serviceaccount "kube-system/aws-load-balancer-controller"
```

### controller installation using HELM
```
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\03-ingress> $helmDir = "$env:USERPROFILE\bin"
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\03-ingress> New-Item -ItemType Directory -Path $helmDir -Force                                                                                                                       
                                                                                                                        
                                                                                                                        
    Directory: C:\Users\abhis                                                                                           
                                                                                                                        

Mode                 LastWriteTime         Length Name                                                                 
----                 -------------         ------ ----                                                                 
d-----        26-06-2026     16:57                bin                                                                  


PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\03-ingress> $url = "https://get.helm.sh/helm-v3.14.0-windows-amd64.zip"
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\03-ingress> Invoke-WebRequest -Uri $url -OutFile "$helmDir\helm.zip"
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\03-ingress> Expand-Archive -Path "$helmDir\helm.zip" -DestinationPath $helmDir -Force
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\03-ingress> $env:PATH += ";$helmDir\windows-amd64"
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\03-ingress> [Environment]::SetEnvironmentVariable("PATH", $env:PATH, [EnvironmentVariableTarget]::User)
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\03-ingress> helm version
version.BuildInfo{Version:"v3.14.0", GitCommit:"3fc9f4b2638e76f26739cd77c7017139be81d0ea", GitTreeState:"clean", GoVersion:"go1.21.5"}
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\03-ingress> helm repo add eks https://aws.github.io/eks-charts
"eks" has been added to your repositories
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\03-ingress> helm repo update
Hang tight while we grab the latest from your chart repositories...
...Successfully got an update from the "eks" chart repository
Update Complete. ⎈Happy Helming!⎈

PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\03-ingress> helm install aws-load-balancer-controller eks/aws-load-balancer-controller --namespace kube-system --set clusterName=prac-cluster-ingress --set serviceAccount.create=false --set serviceAccount.name=aws-load-balancer-controller --set region=ap-south-1 --set vpcId=vpc-0549fa8ba84aa2201
NAME: aws-load-balancer-controller
LAST DEPLOYED: Fri Jun 26 17:20:20 2026
NAMESPACE: kube-system
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
AWS Load Balancer controller installed!
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\03-ingress> 
```

### check controller
```
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s> kubectl get deployment -n kube-system aws-load-balancer-controller
NAME                           READY   UP-TO-DATE   AVAILABLE   AGE
aws-load-balancer-controller   2/2     2            2           3m18s
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s> kubectl get pods -n kube-system -l app.kubernetes.io/name=aws-load-balancer-controller
NAME                                            READY   STATUS    RESTARTS   AGE
aws-load-balancer-controller-6dcdf59759-q6mb4   1/1     Running   0          3m29s
aws-load-balancer-controller-6dcdf59759-qv25d   1/1     Running   0          3m29s
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s>
```

### kube-system before and after
```
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s> kubectl get all -n kube-system
NAME                                     READY   STATUS             RESTARTS        AGE
pod/aws-node-pg2xw                       2/2     Running            0               72m
pod/aws-node-tggx6                       2/2     Running            0               72m
pod/coredns-8477c579f6-mrcbn             1/1     Running            0               75m
pod/coredns-8477c579f6-r4fsd             1/1     Running            0               75m
pod/ebs-csi-controller-cd6d6f649-4jg5t   5/6     CrashLoopBackOff   59 (4m5s ago)   37m
pod/ebs-csi-controller-cd6d6f649-rj6n4   1/6     CrashLoopBackOff   55 (4m5s ago)   37m
pod/ebs-csi-node-qphnf                   3/3     Running            0               37m
pod/ebs-csi-node-qzv5v                   3/3     Running            0               37m
pod/kube-proxy-6j5cc                     1/1     Running            0               72m
pod/kube-proxy-ngdzd                     1/1     Running            0               72m
pod/metrics-server-847bb9c5fd-qkhvx      1/1     Running            0               70m
pod/metrics-server-847bb9c5fd-zs6zx      1/1     Running            0               70m

NAME                                TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)                  AGE
service/eks-extension-metrics-api   ClusterIP   10.100.157.121   <none>        443/TCP                  78m
service/kube-dns                    ClusterIP   10.100.0.10      <none>        53/UDP,53/TCP,9153/TCP   75m
service/metrics-server              ClusterIP   10.100.171.163   <none>        443/TCP                  70m

NAME                                  DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR              AGE
daemonset.apps/aws-node               2         2         2       2            2           <none>                     75m
daemonset.apps/ebs-csi-node           2         2         2       2            2           kubernetes.io/os=linux     37m
daemonset.apps/ebs-csi-node-windows   0         0         0       0            0           kubernetes.io/os=windows   37m
daemonset.apps/kube-proxy             2         2         2       2            2           <none>                     75m

NAME                                 READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/coredns              2/2     2            2           75m
deployment.apps/ebs-csi-controller   0/2     2            0           37m
deployment.apps/metrics-server       2/2     2            2           70m

NAME                                           DESIRED   CURRENT   READY   AGE
replicaset.apps/coredns-8477c579f6             2         2         2       75m
replicaset.apps/ebs-csi-controller-cd6d6f649   2         2         0       37m
replicaset.apps/metrics-server-847bb9c5fd      2         2         2       70m


PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s> kubectl get all -n kube-system
NAME                                                READY   STATUS             RESTARTS       AGE
pod/aws-load-balancer-controller-6dcdf59759-q6mb4   1/1     Running            0              3m37s
pod/aws-load-balancer-controller-6dcdf59759-qv25d   1/1     Running            0              3m37s
pod/aws-node-pg2xw                                  2/2     Running            0              93m
pod/aws-node-tggx6                                  2/2     Running            0              93m
pod/coredns-8477c579f6-mrcbn                        1/1     Running            0              97m
pod/coredns-8477c579f6-r4fsd                        1/1     Running            0              97m
pod/ebs-csi-controller-cd6d6f649-4jg5t              1/6     CrashLoopBackOff   77 (3s ago)    58m
pod/ebs-csi-controller-cd6d6f649-rj6n4              1/6     CrashLoopBackOff   77 (13s ago)   58m
pod/ebs-csi-node-qphnf                              3/3     Running            0              58m
pod/ebs-csi-node-qzv5v                              3/3     Running            0              58m
pod/kube-proxy-6j5cc                                1/1     Running            0              93m
pod/kube-proxy-ngdzd                                1/1     Running            0              93m
pod/metrics-server-847bb9c5fd-qkhvx                 1/1     Running            0              91m
pod/metrics-server-847bb9c5fd-zs6zx                 1/1     Running            0              91m

NAME                                        TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)                  AGE
service/aws-load-balancer-webhook-service   ClusterIP   10.100.183.248   <none>        443/TCP                  3m38s
service/eks-extension-metrics-api           ClusterIP   10.100.157.121   <none>        443/TCP                  99m
service/kube-dns                            ClusterIP   10.100.0.10      <none>        53/UDP,53/TCP,9153/TCP   97m
service/metrics-server                      ClusterIP   10.100.171.163   <none>        443/TCP                  91m

NAME                                  DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR              AGE
daemonset.apps/aws-node               2         2         2       2            2           <none>                     97m
daemonset.apps/ebs-csi-node           2         2         2       2            2           kubernetes.io/os=linux     58m
daemonset.apps/ebs-csi-node-windows   0         0         0       0            0           kubernetes.io/os=windows   58m
daemonset.apps/kube-proxy             2         2         2       2            2           <none>                     97m

NAME                                           READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/aws-load-balancer-controller   2/2     2            2           3m38s
deployment.apps/coredns                        2/2     2            2           97m
deployment.apps/ebs-csi-controller             0/2     2            0           58m
deployment.apps/metrics-server                 2/2     2            2           91m

NAME                                                      DESIRED   CURRENT   READY   AGE
replicaset.apps/aws-load-balancer-controller-6dcdf59759   2         2         2       3m38s
replicaset.apps/coredns-8477c579f6                        2         2         2       97m
replicaset.apps/ebs-csi-controller-cd6d6f649              2         2         0       58m
replicaset.apps/metrics-server-847bb9c5fd                 2         2         2       91m
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s> 
```

### app-ingress before and after
```
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s> kubectl get all -n app-ingress
NAME                                READY   STATUS    RESTARTS      AGE
pod/app-service-8667945846-742vw    0/1     Running   4 (25s ago)   4m45s
pod/app-service-8667945846-v2xc7    0/1     Running   4 (26s ago)   4m44s
pod/frontend-844488469-gfs75        0/1     Running   0             48s
pod/frontend-844488469-vsdzw        0/1     Running   0             48s
pod/postgres-stateful-0             0/1     Pending   0             10m
pod/user-service-858b4897dd-7ct87   0/1     Running   4 (25s ago)   4m45s
pod/user-service-858b4897dd-hnczj   0/1     Running   4 (26s ago)   4m45s

NAME                        TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)    AGE
service/app-service         ClusterIP   10.100.144.53    <none>        5002/TCP   4m44s
service/frontend            ClusterIP   10.100.200.191   <none>        80/TCP     48s
service/postgres            ClusterIP   10.100.117.113   <none>        5432/TCP   10m
service/postgres-headless   ClusterIP   None             <none>        5432/TCP   10m
service/user-service        ClusterIP   10.100.142.79    <none>        5001/TCP   4m45s

NAME                           READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/app-service    0/2     2            0           4m45s
deployment.apps/frontend       0/2     2            0           48s
deployment.apps/user-service   0/2     2            0           4m45s

NAME                                      DESIRED   CURRENT   READY   AGE
replicaset.apps/app-service-8667945846    2         2         0       4m45s
replicaset.apps/frontend-844488469        2         2         0       48s
replicaset.apps/user-service-858b4897dd   2         2         0       4m45s

NAME                                 READY   AGE
statefulset.apps/postgres-stateful   0/1     10m


PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s> kubectl get all -n app-ingress
NAME                                READY   STATUS             RESTARTS         AGE
pod/app-service-8667945846-742vw    0/1     Running            22 (5m15s ago)   68m
pod/app-service-8667945846-v2xc7    0/1     Running            22 (5m15s ago)   68m
pod/frontend-844488469-gfs75        0/1     Running            0                64m
pod/frontend-844488469-vsdzw        0/1     Running            0                64m
pod/postgres-stateful-0             0/1     Pending            0                74m
pod/user-service-858b4897dd-7ct87   0/1     Running            22 (5m21s ago)   68m
pod/user-service-858b4897dd-hnczj   0/1     CrashLoopBackOff   21 (4m58s ago)   68m

NAME                        TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)    AGE
service/app-service         ClusterIP   10.100.144.53    <none>        5002/TCP   68m
service/frontend            ClusterIP   10.100.200.191   <none>        80/TCP     64m
service/postgres            ClusterIP   10.100.117.113   <none>        5432/TCP   74m
service/postgres-headless   ClusterIP   None             <none>        5432/TCP   74m
service/user-service        ClusterIP   10.100.142.79    <none>        5001/TCP   68m

NAME                           READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/app-service    0/2     2            0           68m
deployment.apps/frontend       0/2     2            0           64m
deployment.apps/user-service   0/2     2            0           68m

NAME                                      DESIRED   CURRENT   READY   AGE
replicaset.apps/app-service-8667945846    2         2         0       68m
replicaset.apps/frontend-844488469        2         2         0       64m
replicaset.apps/user-service-858b4897dd   2         2         0       68m

NAME                                 READY   AGE
statefulset.apps/postgres-stateful   0/1     74m
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s> 
```

## 06 INGRESS 
```
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s> cd .\sessions\03-ingress\               
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\03-ingress> kubectl apply -f subsessions/06-ingress/
ingress.networking.k8s.io/message-board-ingress created
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\03-ingress> kubectl get ingress -n app-ingress
NAME                    CLASS   HOSTS   ADDRESS                                                                    PORTS   AGE
message-board-ingress   alb     *       k8s-appingre-messageb-475db74cc5-1887033768.ap-south-1.elb.amazonaws.com   80      8s
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\03-ingress> kubectl describe ingress message-board-ingress -n app-ingress
Name:             message-board-ingress
Labels:           <none>
Namespace:        app-ingress
Address:          k8s-appingre-messageb-475db74cc5-1887033768.ap-south-1.elb.amazonaws.com
Ingress Class:    alb
Default backend:  <default>
Rules:
  Host        Path  Backends
  ----        ----  --------
  *           
              /api/users   user-service:5001 ()
              /api/apps    app-service:5002 ()
              /            frontend:80 ()
Annotations:  alb.ingress.kubernetes.io/healthcheck-path: /healthz
              alb.ingress.kubernetes.io/listen-ports: [{"HTTP": 80}]
              alb.ingress.kubernetes.io/scheme: internet-facing
              alb.ingress.kubernetes.io/target-type: ip
Events:
  Type    Reason                  Age   From     Message
  ----    ------                  ----  ----     -------
  Normal  SuccessfullyReconciled  11s   ingress  Successfully reconciled
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\03-ingress> kubectl get ingress message-board-ingress -n app-ingress
NAME                    CLASS   HOSTS   ADDRESS                                                                    PORTS   AGE
message-board-ingress   alb     *       k8s-appingre-messageb-475db74cc5-1887033768.ap-south-1.elb.amazonaws.com   80      44s
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\03-ingress>
```
> <img width="1466" height="777" alt="image" src="https://github.com/user-attachments/assets/d7f7d72f-84d9-46d7-9315-418218b8f971" />

> <img width="1286" height="768" alt="image" src="https://github.com/user-attachments/assets/ab002418-66d6-4e90-b1d8-21fd6cbd85b8" />

> <img width="1104" height="178" alt="image" src="https://github.com/user-attachments/assets/fd3a5cc2-6ee2-49dd-8d57-2d76f3cd67fa" />

> <img width="1103" height="277" alt="image" src="https://github.com/user-attachments/assets/64f8c265-053e-4ab5-bf08-315462a41d70" />






