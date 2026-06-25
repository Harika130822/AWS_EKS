# Storage PV PVC Statefulset

```
eksctl create cluster --name practice-cluster-cls1 --region ap-south-1 --nodegroup-name standard-workers --node-type t3.medium --nodes 2 --nodes-min 2 --nodes-max 3
aws eks --region ap-south-1 update-kubeconfig --name practice-cluster-cls1
eksctl delete cluster --name practice-cluster-cls1 --region ap-south-1
```

## 01 storage problem and shared config
```
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\02-storage-pv-pvc-statefulset> kubectl apply -f subsessions/01-storage-problem-and-shared-config/
namespace/app-storage created
configmap/app-config created
secret/app-secrets created
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\02-storage-pv-pvc-statefulset> kubectl get namespace app-storage
NAME          STATUS   AGE
app-storage   Active   28s
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\02-storage-pv-pvc-statefulset> kubectl get namespace            
NAME              STATUS   AGE
app-storage       Active   40s
default           Active   21m
kube-node-lease   Active   21m
kube-public       Active   21m
kube-system       Active   21m
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\02-storage-pv-pvc-statefulset> kubectl get all -n app-storage
No resources found in app-storage namespace.
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\02-storage-pv-pvc-statefulset> kubectl get configmap -n app-storage
NAME               DATA   AGE
app-config         2      2m14s
kube-root-ca.crt   1      2m14s
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\02-storage-pv-pvc-statefulset> kubectl get secret -n app-storage
NAME          TYPE     DATA   AGE
app-secrets   Opaque   4      2m22s
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\02-storage-pv-pvc-statefulset>
```
> <img width="1166" height="837" alt="image" src="https://github.com/user-attachments/assets/640ab1ec-bc27-4685-827f-8451b171b523" />

## 02 Static PV PVC -> Status -> Bound
```
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\02-storage-pv-pvc-statefulset> kubectl apply -f subsessions/02-static-pv-pvc/
persistentvolume/postgres-static-hostpath-pv created
persistentvolumeclaim/postgres-data-static created
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\02-storage-pv-pvc-statefulset> kubectl get pv
NAME                          CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                              STORAGECLASS   VOLUMEATTRIBUTESCLASS   REASON   AGE
postgres-static-hostpath-pv   1Gi        RWO            Retain           Bound    app-storage/postgres-data-static   manual         <unset>                          113s
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\02-storage-pv-pvc-statefulset> kubectl get pvc -n app-storage
NAME                   STATUS   VOLUME                        CAPACITY   ACCESS MODES   STORAGECLASS   VOLUMEATTRIBUTESCLASS   AGE
postgres-data-static   Bound    postgres-static-hostpath-pv   1Gi        RWO            manual         <unset>                 2m7s
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\02-storage-pv-pvc-statefulset> kubectl get pvc
No resources found in default namespace.
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\02-storage-pv-pvc-statefulset> kubectl describe pvc postgres-data-static -n app-storage
Name:          postgres-data-static
Namespace:     app-storage
StorageClass:  manual
Status:        Bound
Volume:        postgres-static-hostpath-pv
Labels:        app=postgres
               storage-demo=static
Annotations:   pv.kubernetes.io/bind-completed: yes
               pv.kubernetes.io/bound-by-controller: yes
Finalizers:    [kubernetes.io/pvc-protection]
Capacity:      1Gi
Access Modes:  RWO
VolumeMode:    Filesystem
Used By:       <none>
Events:        <none>
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\02-storage-pv-pvc-statefulset> kubectl describe pv postgres-static-hostpath-pv               
Name:            postgres-static-hostpath-pv
Labels:          app=postgres
                 storage-demo=static
Annotations:     pv.kubernetes.io/bound-by-controller: yes
Finalizers:      [kubernetes.io/pv-protection]
StorageClass:    manual
Status:          Bound
Claim:           app-storage/postgres-data-static
Reclaim Policy:  Retain
Access Modes:    RWO
VolumeMode:      Filesystem
Capacity:        1Gi
Node Affinity:   <none>
Message:         
Source:
    Type:          HostPath (bare host directory volume)
    Path:          /tmp/k8s-three-tier-postgres
    HostPathType:  DirectoryOrCreate
Events:            <none>
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\02-storage-pv-pvc-statefulset> 
```

## 03 - postgres with static PV - path - /var/lib/postgresql/data
> <img width="1049" height="796" alt="image" src="https://github.com/user-attachments/assets/43d94d8c-2675-4e1f-bced-2258c4cc98b6" />

```
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\02-storage-pv-pvc-statefulset> kubectl apply -f subsessions/03-postgres-with-static-pvc/
deployment.apps/postgres created
service/postgres created
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\02-storage-pv-pvc-statefulset> kubectl get deployment postgres -n app-storage
NAME       READY   UP-TO-DATE   AVAILABLE   AGE
postgres   0/1     1            0           9s
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\02-storage-pv-pvc-statefulset> kubectl get pods -n app-storage -l app=postgres,storage-demo=static
NAME                        READY   STATUS    RESTARTS   AGE
postgres-59bdc5cf88-jg5bv   0/1     Running   0          16s
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\02-storage-pv-pvc-statefulset> kubectl get service postgres -n app-storage
NAME       TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)    AGE
postgres   ClusterIP   10.100.39.0   <none>        5432/TCP   22s
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\02-storage-pv-pvc-statefulset> kubectl get pvc -n app-storage
NAME                   STATUS   VOLUME                        CAPACITY   ACCESS MODES   STORAGECLASS   VOLUMEATTRIBUTESCLASS   AGE
postgres-data-static   Bound    postgres-static-hostpath-pv   1Gi        RWO            manual         <unset>                 13m
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\02-storage-pv-pvc-statefulset> kubectl get all -n app-storage
NAME                            READY   STATUS    RESTARTS   AGE
pod/postgres-59bdc5cf88-jg5bv   1/1     Running   0          39s

NAME               TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)    AGE
service/postgres   ClusterIP   10.100.39.0   <none>        5432/TCP   39s

NAME                       READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/postgres   1/1     1            1           39s

NAME                                  DESIRED   CURRENT   READY   AGE
replicaset.apps/postgres-59bdc5cf88   1         1         1       39s
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\02-storage-pv-pvc-statefulset> 
```

## 04 - Flask with persistent DB - Loadbalancer service
```
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\02-storage-pv-pvc-statefulset> kubectl apply -f subsessions/04-flask-with-persistent-db/
deployment.apps/flask-web created
service/flask-web created
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\02-storage-pv-pvc-statefulset> kubectl get deployment flask-web -n app-storage
NAME        READY   UP-TO-DATE   AVAILABLE   AGE
flask-web   0/2     2            0           9s
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\02-storage-pv-pvc-statefulset> kubectl get pods -n app-storage -l app=flask-web
NAME                         READY   STATUS    RESTARTS   AGE
flask-web-6b6f976596-2jrfb   1/1     Running   0          26s
flask-web-6b6f976596-mqsf5   1/1     Running   0          26s
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\02-storage-pv-pvc-statefulset> kubectl get service flask-web -n app-storage
NAME        TYPE           CLUSTER-IP       EXTERNAL-IP                                                                PORT(S)        AGE
flask-web   LoadBalancer   10.100.222.245   a990db4628d8e46cc929b99adb4324ea-1051815790.ap-south-1.elb.amazonaws.com   80:31163/TCP   33s
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\02-storage-pv-pvc-statefulset> kubectl get deployment flask-web -n app-storage 
NAME        READY   UP-TO-DATE   AVAILABLE   AGE
flask-web   2/2     2            2           42s
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\02-storage-pv-pvc-statefulset> kubectl port-forward -n app-storage service/flask-web 8080:80
Forwarding from 127.0.0.1:8080 -> 5000
Forwarding from [::1]:8080 -> 5000
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\02-storage-pv-pvc-statefulset> kubectl get service flask-web -n app-storage
NAME        TYPE           CLUSTER-IP       EXTERNAL-IP                                                                PORT(S)        AGE
flask-web   LoadBalancer   10.100.222.245   a990db4628d8e46cc929b99adb4324ea-1051815790.ap-south-1.elb.amazonaws.com   80:31163/TCP   78s
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\02-storage-pv-pvc-statefulset> kubectl port-forward -n app-storage service/flask-web 8080:80
Forwarding from 127.0.0.1:8080 -> 5000
Forwarding from [::1]:8080 -> 5000
Handling connection for 8080
Handling connection for 8080
Handling connection for 8080
Handling connection for 8080
Handling connection for 8080
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\02-storage-pv-pvc-statefulset> kubectl get all -n app-storage                               
NAME                             READY   STATUS    RESTARTS   AGE
pod/flask-web-6b6f976596-2jrfb   1/1     Running   0          3m15s
pod/flask-web-6b6f976596-mqsf5   1/1     Running   0          3m15s
pod/postgres-59bdc5cf88-jg5bv    1/1     Running   0          16m

NAME                TYPE           CLUSTER-IP       EXTERNAL-IP                                                                PORT(S)        AGE
service/flask-web   LoadBalancer   10.100.222.245   a990db4628d8e46cc929b99adb4324ea-1051815790.ap-south-1.elb.amazonaws.com   80:31163/TCP   3m16s
service/postgres    ClusterIP      10.100.39.0      <none>                                                                     5432/TCP       16m

NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/flask-web   2/2     2            2           3m16s
deployment.apps/postgres    1/1     1            1           16m

NAME                                   DESIRED   CURRENT   READY   AGE
replicaset.apps/flask-web-6b6f976596   2         2         2       3m16s
replicaset.apps/postgres-59bdc5cf88    1         1         1       16m
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\02-storage-pv-pvc-statefulset> 
```
> <img width="1311" height="739" alt="image" src="https://github.com/user-attachments/assets/311a21ae-cfb1-4c01-b659-3dc5a2a1efe3" />


### Delete the postgressql and check the persistency
```
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\02-storage-pv-pvc-statefulset> kubectl delete pod -n app-storage -l app=postgres,storage-demo=static
pod "postgres-59bdc5cf88-jg5bv" deleted from app-storage namespace
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\02-storage-pv-pvc-statefulset> kubectl get pods -n app-storage -w
NAME                         READY   STATUS    RESTARTS   AGE
flask-web-6b6f976596-2jrfb   1/1     Running   0          6m51s
flask-web-6b6f976596-mqsf5   1/1     Running   0          6m51s
postgres-59bdc5cf88-9hpzg    0/1     Running   0          8s
flask-web-6b6f976596-mqsf5   0/1     Running   0          6m54s
postgres-59bdc5cf88-9hpzg    1/1     Running   0          12s
flask-web-6b6f976596-mqsf5   1/1     Running   0          6m59s
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\02-storage-pv-pvc-statefulset> kubectl get pods -n app-storage   
NAME                         READY   STATUS    RESTARTS   AGE
flask-web-6b6f976596-2jrfb   1/1     Running   0          7m6s
flask-web-6b6f976596-mqsf5   1/1     Running   0          7m6s
postgres-59bdc5cf88-9hpzg    1/1     Running   0          23s
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\02-storage-pv-pvc-statefulset> kubectl wait -n app-storage --for=condition=ready pod -l app=postgres,storage-demo=static --timeout=180s
pod/postgres-59bdc5cf88-9hpzg condition met
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\02-storage-pv-pvc-statefulset> kubectl get endpoints postgres -n app-storage
Warning: v1 Endpoints is deprecated in v1.33+; use discovery.k8s.io/v1 EndpointSlice
NAME       ENDPOINTS            AGE
postgres   192.168.0.237:5432   21m
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\02-storage-pv-pvc-statefulset> 
```
> <img width="1481" height="945" alt="image" src="https://github.com/user-attachments/assets/34f6e8b5-cf2f-4165-9458-0e113b040b8b" />

> <img width="1063" height="528" alt="image" src="https://github.com/user-attachments/assets/59a5bbd1-fa69-4d86-908a-a9f7ff1fd391" />

```
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\02-storage-pv-pvc-statefulset> kubectl get pods -n app-storage
NAME                         READY   STATUS    RESTARTS   AGE
flask-web-6b6f976596-2jrfb   1/1     Running   0          17m
flask-web-6b6f976596-mqsf5   1/1     Running   0          17m
postgres-59bdc5cf88-9hpzg    1/1     Running   0          10m
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\02-storage-pv-pvc-statefulset> kubectl get pvc -n app-storage
NAME                   STATUS   VOLUME                        CAPACITY   ACCESS MODES   STORAGECLASS   VOLUMEATTRIBUTESCLASS   AGE
postgres-data-static   Bound    postgres-static-hostpath-pv   1Gi        RWO            manual         <unset>                 43m
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\02-storage-pv-pvc-statefulset> kubectl describe pvc postgres-data-static -n app-storage  
Name:          postgres-data-static
Namespace:     app-storage
StorageClass:  manual
Status:        Bound
Volume:        postgres-static-hostpath-pv
Labels:        app=postgres
               storage-demo=static
Annotations:   pv.kubernetes.io/bind-completed: yes
               pv.kubernetes.io/bound-by-controller: yes
Finalizers:    [kubernetes.io/pvc-protection]
Capacity:      1Gi
Access Modes:  RWO
VolumeMode:    Filesystem
Used By:       postgres-59bdc5cf88-9hpzg
Events:        <none>
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\02-storage-pv-pvc-statefulset> kubectl get endpoints postgres -n app-storage
Warning: v1 Endpoints is deprecated in v1.33+; use discovery.k8s.io/v1 EndpointSlice
NAME       ENDPOINTS            AGE
postgres   192.168.0.237:5432   30m
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\02-storage-pv-pvc-statefulset> kubectl logs -n app-storage deployment/postgres

PostgreSQL Database directory appears to contain a database; Skipping initialization

2026-06-25 11:56:50.741 UTC [1] LOG:  starting PostgreSQL 16.14 on x86_64-pc-linux-musl, compiled by gcc (Alpine 15.2.0) 15.2.0, 64-bit
2026-06-25 11:56:50.741 UTC [1] LOG:  listening on IPv4 address "0.0.0.0", port 5432
2026-06-25 11:56:50.741 UTC [1] LOG:  listening on IPv6 address "::", port 5432
2026-06-25 11:56:50.744 UTC [1] LOG:  listening on Unix socket "/var/run/postgresql/.s.PGSQL.5432"
2026-06-25 11:56:50.746 UTC [28] LOG:  database system was shut down at 2026-06-25 11:56:49 UTC
2026-06-25 11:56:50.750 UTC [1] LOG:  database system is ready to accept connections
2026-06-25 12:01:50.845 UTC [26] LOG:  checkpoint starting: time
2026-06-25 12:01:51.246 UTC [26] LOG:  checkpoint complete: wrote 7 buffers (0.0%); 0 WAL file(s) added, 0 removed, 0 recycled; write=0.402 s, sync=0.001 s, total=0.402 s; sync files=6, longest=0.001 s, average=0.001 s; distance=1 kB, estimate=1 kB; lsn=0/198EB88, redo lsn=0/198EB50
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\02-storage-pv-pvc-statefulset> kubectl logs -n app-storage deployment/flask-web
Found 2 pods, using pod/flask-web-6b6f976596-2jrfb
[2026-06-25 11:50:14 +0000] [1] [INFO] Starting gunicorn 22.0.0
[2026-06-25 11:50:14 +0000] [1] [INFO] Listening at: http://0.0.0.0:5000 (1)
[2026-06-25 11:50:14 +0000] [1] [INFO] Using worker: gthread
[2026-06-25 11:50:14 +0000] [8] [INFO] Booting worker with pid: 8
[2026-06-25 11:50:14 +0000] [9] [INFO] Booting worker with pid: 9
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\02-storage-pv-pvc-statefulset> 
```
> <img width="1033" height="536" alt="image" src="https://github.com/user-attachments/assets/08b57c04-2db8-4d1f-ba0d-c5d4e41d24f8" />

> <img width="1032" height="682" alt="image" src="https://github.com/user-attachments/assets/8efba8cc-8b07-4170-9e71-7135bec9a0fb" />


## 05 StorageClass -> Dynamic Database -> AWS -> EBS CSI driver attached to cluster
> <img width="1546" height="628" alt="image" src="https://github.com/user-attachments/assets/6dd73ce3-cc84-4691-aaec-c5e0d61abe43" />

```
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\02-storage-pv-pvc-statefulset> kubectl get csidriver
NAME              ATTACHREQUIRED   PODINFOONMOUNT   STORAGECAPACITY   TOKENREQUESTS   REQUIRESREPUBLISH   MODES        AGE
ebs.csi.aws.com   true             false            false             <unset>         false               Persistent   4m51s
efs.csi.aws.com   false            false            false             <unset>         false               Persistent   84m
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\02-storage-pv-pvc-statefulset> kubectl get storageclass
NAME   PROVISIONER             RECLAIMPOLICY   VOLUMEBINDINGMODE      ALLOWVOLUMEEXPANSION   AGE
gp2    kubernetes.io/aws-ebs   Delete          WaitForFirstConsumer   false                  85m
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\02-storage-pv-pvc-statefulset> kubectl apply -f subsessions/05-storageclass/
storageclass.storage.k8s.io/ebs-gp3 created
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\02-storage-pv-pvc-statefulset> kubectl get storageclass
NAME      PROVISIONER             RECLAIMPOLICY   VOLUMEBINDINGMODE      ALLOWVOLUMEEXPANSION   AGE
ebs-gp3   ebs.csi.aws.com         Delete          WaitForFirstConsumer   true                   8s
gp2       kubernetes.io/aws-ebs   Delete          WaitForFirstConsumer   false                  85m
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\02-storage-pv-pvc-statefulset> 
```
> <img width="949" height="448" alt="image" src="https://github.com/user-attachments/assets/054db26c-256e-4487-80e4-e2efdc51d127" />

> <img width="838" height="853" alt="image" src="https://github.com/user-attachments/assets/9148c249-1c02-485c-85ec-7fc46eb13728" />

> <img width="830" height="283" alt="image" src="https://github.com/user-attachments/assets/d88a5579-2902-4ec2-af13-9cae736832ab" />


## 06 PostgresSQL
```
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\02-storage-pv-pvc-statefulset> kubectl delete -f subsessions/03-postgres-with-static-pvc/ --ignore-not-found
deployment.apps "postgres" deleted from app-storage namespace
service "postgres" deleted from app-storage namespace
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\02-storage-pv-pvc-statefulset> kubectl apply -f subsessions/05-storageclass/
storageclass.storage.k8s.io/ebs-gp3 unchanged
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\02-storage-pv-pvc-statefulset> kubectl apply -f subsessions/06-postgres-statefulset/
service/postgres-headless unchanged
service/postgres created
statefulset.apps/postgres-stateful configured
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\02-storage-pv-pvc-statefulset> kubectl get statefulset -n app-storag
No resources found in app-storag namespace.
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\02-storage-pv-pvc-statefulset> kubectl get statefulset -n app-storage
NAME                READY   AGE
postgres-stateful   0/1     5m8s
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\02-storage-pv-pvc-statefulset> kubectl get pods -n app-storage -l app=postgres,storage-demo=stateful
NAME                  READY   STATUS    RESTARTS   AGE
postgres-stateful-0   0/1     Pending   0          5m15s
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\02-storage-pv-pvc-statefulset> kubectl get service -n app-storage
NAME                TYPE           CLUSTER-IP       EXTERNAL-IP                                                                PORT(S)        AGE
flask-web           LoadBalancer   10.100.222.245   a990db4628d8e46cc929b99adb4324ea-1051815790.ap-south-1.elb.amazonaws.com   80:31163/TCP   51m
postgres            ClusterIP      10.100.192.68    <none>                                                                     5432/TCP       44s
postgres-headless   ClusterIP      None             <none>                                                                     5432/TCP       5m23s
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\02-storage-pv-pvc-statefulset> kubectl get pvc -n app-storage
NAME                                STATUS    VOLUME                        CAPACITY   ACCESS MODES   STORAGECLASS   VOLUMEATTRIBUTESCLASS   AGE
postgres-data-postgres-stateful-0   Pending                                                           ebs-gp3        <unset>                 5m29s
postgres-data-static                Bound     postgres-static-hostpath-pv   1Gi        RWO            manual         <unset>                 78m
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\02-storage-pv-pvc-statefulset> kubectl get pv
NAME                          CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                              STORAGECLASS   VOLUMEATTRIBUTESCLASS   REASON   AGE
postgres-static-hostpath-pv   1Gi        RWO            Retain           Bound    app-storage/postgres-data-static   manual         <unset>                          78m
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\02-storage-pv-pvc-statefulset> kubectl describe statefulset postgres-stateful -n app-storage
Name:               postgres-stateful
Namespace:          app-storage
CreationTimestamp:  Thu, 25 Jun 2026 18:06:35 +0530
Selector:           app=postgres,storage-demo=stateful,tier=data
Labels:             app=postgres
                    storage-demo=stateful
                    tier=data
Annotations:        <none>
Replicas:           1 desired | 1 total
Update Strategy:    RollingUpdate
  Partition:        0
Pods Status:        0 Running / 1 Waiting / 0 Succeeded / 0 Failed
Pod Template:
  Labels:  app=postgres
           storage-demo=stateful
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
                 storage-demo=stateful
  Annotations:   <none>
  Capacity:      1Gi
  Access Modes:  [ReadWriteOnce]
Events:
  Type    Reason            Age    From                    Message
  ----    ------            ----   ----                    -------
  Normal  SuccessfulCreate  5m40s  statefulset-controller  create Claim postgres-data-postgres-stateful-0 Pod postgres-stateful-0 in StatefulSet postgres-stateful success
  Normal  SuccessfulCreate  5m40s  statefulset-controller  create Pod postgres-stateful-0 in StatefulSet postgres-stateful successful
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\02-storage-pv-pvc-statefulset> kubectl describe pvc postgres-data-postgres-stateful-0 -n app-storage
Name:          postgres-data-postgres-stateful-0
Namespace:     app-storage
StorageClass:  ebs-gp3
Status:        Pending
Volume:        
Labels:        app=postgres
               storage-demo=stateful
               tier=data
Annotations:   volume.beta.kubernetes.io/storage-provisioner: ebs.csi.aws.com
               volume.kubernetes.io/selected-node: ip-192-168-15-21.ap-south-1.compute.internal
               volume.kubernetes.io/storage-provisioner: ebs.csi.aws.com
Finalizers:    [kubernetes.io/pvc-protection]
Capacity:      
Access Modes:  
VolumeMode:    Filesystem
Used By:       postgres-stateful-0
Events:
  Type    Reason                Age                   From                         Message
  ----    ------                ----                  ----                         -------
  Normal  WaitForFirstConsumer  5m46s                 persistentvolume-controller  waiting for first consumer to be created before binding
  Normal  ExternalProvisioning  14s (x24 over 5m46s)  persistentvolume-controller  Waiting for a volume to be created either by the external provisioner 'ebs.csi.aws.com' or manually by the system administrator. If volume creation is delayed, please verify that the provisioner is running and correctly registered.
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\02-storage-pv-pvc-statefulset> kubectl apply -f subsessions/04-flask-with-persistent-db/
deployment.apps/flask-web unchanged
service/flask-web unchanged
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\02-storage-pv-pvc-statefulset> kubectl delete -f subsessions/04-flask-with-persistent-db/
deployment.apps "flask-web" deleted from app-storage namespace
service "flask-web" deleted from app-storage namespace
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\02-storage-pv-pvc-statefulset> 
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\02-storage-pv-pvc-statefulset> kubectl delete -f subsessions/04-flask-with-persistent-db/
Error from server (NotFound): error when deleting "subsessions\\04-flask-with-persistent-db\\01-flask-deployment.yml": deployments.apps "flask-web" not found
Error from server (NotFound): error when deleting "subsessions\\04-flask-with-persistent-db\\02-flask-service.yml": services "flask-web" not found
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\02-storage-pv-pvc-statefulset> kubectl apply -f subsessions/04-flask-with-persistent-db/ 
deployment.apps/flask-web created
service/flask-web created
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\02-storage-pv-pvc-statefulset> 
```
> <img width="781" height="648" alt="image" src="https://github.com/user-attachments/assets/d47b1a23-1062-4b58-90a1-ee8cada509ed" />

> <img width="802" height="498" alt="image" src="https://github.com/user-attachments/assets/2a8efc85-931d-4de8-9696-902e125d44f4" />


