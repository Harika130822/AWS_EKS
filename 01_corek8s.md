# CoreK8s

Must do at the end

```
eksctl delete cluster --name prac-cluster --region ap-south-1
eksctl create cluster --name prac-cluster --region ap-south-1 --nodegroup-name standard-workers --node-type t3.medium --nodes 2 --nodes-min 2 --nodes-max 3
aws eks --region ap-south-1 update-kubeconfig --name prac-cluster
```

## 01-namespace
```
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\01-core-k8s\subsessions> kubectl apply -f .\01-namespace\01-namespace.yml                 
namespace/app-core created
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\01-core-k8s\subsessions> kubectl get namespaces
NAME              STATUS   AGE
app-core          Active   53s
default           Active   8m50s
kube-node-lease   Active   8m50s
kube-public       Active   8m50s
kube-system       Active   8m50s
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\01-core-k8s\subsessions> kubectl get all -n app-core
No resources found in app-core namespace.
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\01-core-k8s\subsessions>
```

## 02 - configmap and secrets
```
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\01-core-k8s\subsessions> kubectl apply -f .\02-configmap-secret\
configmap/app-config created
secret/app-secrets created
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\01-core-k8s\subsessions> kubectl get configmap -n app-core
NAME               DATA   AGE
app-config         2      33s
kube-root-ca.crt   1      3m23s
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\01-core-k8s\subsessions> kubectl describe configmap app-config -n app-core
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
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\01-core-k8s\subsessions> kubectl get secret -n app-core
NAME          TYPE     DATA   AGE
app-secrets   Opaque   4      54s
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\01-core-k8s\subsessions> kubectl describe secret app-secrets -n app-core
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
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\01-core-k8s\subsessions> kubectl get namespaces                           
NAME              STATUS   AGE
app-core          Active   3m59s
default           Active   11m
kube-node-lease   Active   11m
kube-public       Active   11m
kube-system       Active   11m
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\01-core-k8s\subsessions> kubectl get all -n app-core                                       
No resources found in app-core namespace.
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\01-core-k8s\subsessions> 
```

## 03 - Kind - Deployment and Services
```
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\01-core-k8s\subsessions> kubectl apply -f .\03-postgres-deployment-service\
deployment.apps/postgres created
service/postgres created

# check
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\01-core-k8s\subsessions> kubectl get deployment -n app-core
NAME       READY   UP-TO-DATE   AVAILABLE   AGE
postgres   1/1     1            1           20s
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\01-core-k8s\subsessions> kubectl get pods -n app-core -l app=postgres
NAME                        READY   STATUS    RESTARTS   AGE
postgres-5cf755b76f-hw7rc   1/1     Running   0          26s
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\01-core-k8s\subsessions> kubectl get service postgres -n app-core
NAME       TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)    AGE
postgres   ClusterIP   10.100.48.144   <none>        5432/TCP   33s
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\01-core-k8s\subsessions> kubectl get endpoints postgres -n app-core
Warning: v1 Endpoints is deprecated in v1.33+; use discovery.k8s.io/v1 EndpointSlice
NAME       ENDPOINTS             AGE
postgres   192.168.75.248:5432   40s

# trouble shooting
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\01-core-k8s\subsessions> kubectl describe pod -n app-core -l app=postgres
Name:             postgres-5cf755b76f-hw7rc
Namespace:        app-core
Priority:         0
Service Account:  default
Node:             ip-192-168-88-252.ap-south-1.compute.internal/192.168.88.252
Start Time:       Wed, 24 Jun 2026 19:22:57 +0530
Labels:           app=postgres
                  pod-template-hash=5cf755b76f
                  tier=data
Annotations:      <none>
Status:           Running
IP:               192.168.75.248
IPs:
  IP:           192.168.75.248
Controlled By:  ReplicaSet/postgres-5cf755b76f
Containers:
  postgres:
    Container ID:   containerd://a5a631113638983ae4a118e259ac8267267d7c871bf85d490c1748d856d141b5
    Image:          postgres:16-alpine
    Image ID:       docker.io/library/postgres@sha256:e013e867e712fec275706a6c51c966f0bb0c93cfa8f51000f85a15f9865a28cb
    Port:           5432/TCP (postgres)
    Host Port:      0/TCP (postgres)
    State:          Running
      Started:      Wed, 24 Jun 2026 19:23:05 +0530
    Ready:          True
    Restart Count:  0
    Limits:
      cpu:     500m
      memory:  512Mi
    Requests:
      cpu:      100m
      memory:   256Mi
    Readiness:  exec [sh -c pg_isready -U "$POSTGRES_USER" -d "$POSTGRES_DB"] delay=8s timeout=1s period=5s #success=1 #failure=3
    Environment:
      POSTGRES_DB:        <set to the key 'POSTGRES_DB' in secret 'app-secrets'>        Optional: false
      POSTGRES_USER:      <set to the key 'POSTGRES_USER' in secret 'app-secrets'>      Optional: false
      POSTGRES_PASSWORD:  <set to the key 'POSTGRES_PASSWORD' in secret 'app-secrets'>  Optional: false
      PGDATA:             /var/lib/postgresql/data/pgdata
    Mounts:
      /var/lib/postgresql/data from postgres-ephemeral (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-26ccw (ro)
Conditions:
  Type                        Status
  PodReadyToStartContainers   True 
  Initialized                 True 
  Ready                       True 
  ContainersReady             True 
  PodScheduled                True 
Volumes:
  postgres-ephemeral:
    Type:       EmptyDir (a temporary directory that shares a pod's lifetime)
    Medium:     
    SizeLimit:  <unset>
  kube-api-access-26ccw:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    Optional:                false
    DownwardAPI:             true
QoS Class:                   Burstable
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type    Reason     Age    From               Message
  ----    ------     ----   ----               -------
  Normal  Scheduled  3m25s  default-scheduler  Successfully assigned app-core/postgres-5cf755b76f-hw7rc to ip-192-168-88-252.ap-south-1.compute.internal
  Normal  Pulling    3m24s  kubelet            Pulling image "postgres:16-alpine"
  Normal  Pulled     3m17s  kubelet            Successfully pulled image "postgres:16-alpine" in 7.536s (7.536s including waiting). Image size: 116039346 bytes.
  Normal  Created    3m17s  kubelet            Created container: postgres
  Normal  Started    3m17s  kubelet            Started container postgres
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\01-core-k8s\subsessions>


# trouble shooting 2
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\01-core-k8s\subsessions> kubectl logs -n app-core deployment/postgres
The files belonging to this database system will be owned by user "postgres".
This user must also own the server process.

The database cluster will be initialized with locale "en_US.utf8".
The default database encoding has accordingly been set to "UTF8".
The default text search configuration will be set to "english".

Data page checksums are disabled.

fixing permissions on existing directory /var/lib/postgresql/data/pgdata ... ok
creating subdirectories ... ok
selecting dynamic shared memory implementation ... posix
selecting default max_connections ... 100
selecting default shared_buffers ... 128MB
selecting default time zone ... UTC
creating configuration files ... ok
running bootstrap script ... ok
sh: locale: not found
2026-06-24 13:53:06.958 UTC [36] WARNING:  no usable system locales were found
performing post-bootstrap initialization ... ok
syncing data to disk ... ok


Success. You can now start the database server using:

    pg_ctl -D /var/lib/postgresql/data/pgdata -l logfile start

initdb: warning: enabling "trust" authentication for local connections
initdb: hint: You can change this by editing pg_hba.conf or using the option -A, or --auth-local and --auth-host, the next time you run initdb.
waiting for server to start....2026-06-24 13:53:08.030 UTC [42] LOG:  starting PostgreSQL 16.14 on x86_64-pc-linux-musl, compiled by gcc (Alpine 15.2.0) 15.2.0, 64-bit
2026-06-24 13:53:08.032 UTC [42] LOG:  listening on Unix socket "/var/run/postgresql/.s.PGSQL.5432"
2026-06-24 13:53:08.036 UTC [45] LOG:  database system was shut down at 2026-06-24 13:53:07 UTC
2026-06-24 13:53:08.041 UTC [42] LOG:  database system is ready to accept connections
 done
server started
CREATE DATABASE


/usr/local/bin/docker-entrypoint.sh: ignoring /docker-entrypoint-initdb.d/*

waiting for server to shut down...2026-06-24 13:53:08.158 UTC [42] LOG:  received fast shutdown request
.2026-06-24 13:53:08.160 UTC [42] LOG:  aborting any active transactions
2026-06-24 13:53:08.166 UTC [42] LOG:  background worker "logical replication launcher" (PID 48) exited with exit code 1
2026-06-24 13:53:08.166 UTC [43] LOG:  shutting down
2026-06-24 13:53:08.167 UTC [43] LOG:  checkpoint starting: shutdown immediate
2026-06-24 13:53:08.249 UTC [43] LOG:  checkpoint complete: wrote 926 buffers (5.7%); 0 WAL file(s) added, 0 removed, 0 recycled; write=0.075 s, sync=0.004 s, total=0.083 s; sync files=301, longest=0.002 s, average=0.001 s; distance=4283 kB, estimate=4283 kB; lsn=0/1925D58, redo lsn=0/1925D58
2026-06-24 13:53:08.256 UTC [42] LOG:  database system is shut down
 done
server stopped

PostgreSQL init process complete; ready for start up.

2026-06-24 13:53:08.284 UTC [1] LOG:  starting PostgreSQL 16.14 on x86_64-pc-linux-musl, compiled by gcc (Alpine 15.2.0) 15.2.0, 64-bit
2026-06-24 13:53:08.284 UTC [1] LOG:  listening on IPv4 address "0.0.0.0", port 5432
2026-06-24 13:53:08.284 UTC [1] LOG:  listening on IPv6 address "::", port 5432
2026-06-24 13:53:08.286 UTC [1] LOG:  listening on Unix socket "/var/run/postgresql/.s.PGSQL.5432"
2026-06-24 13:53:08.290 UTC [58] LOG:  database system was shut down at 2026-06-24 13:53:08 UTC
2026-06-24 13:53:08.328 UTC [1] LOG:  database system is ready to accept connections
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\01-core-k8s\subsessions>


PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\01-core-k8s\subsessions> kubectl get all -n app-core
NAME                            READY   STATUS    RESTARTS   AGE
pod/postgres-5cf755b76f-hw7rc   1/1     Running   0          5m45s

NAME               TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)    AGE
service/postgres   ClusterIP   10.100.48.144   <none>        5432/TCP   5m45s

NAME                       READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/postgres   1/1     1            1           5m45s

NAME                                  DESIRED   CURRENT   READY   AGE
replicaset.apps/postgres-5cf755b76f   1         1         1       5m45s
```
> <img width="1513" height="869" alt="image" src="https://github.com/user-attachments/assets/db1fbd42-389d-4658-9b0c-b590b7712b5f" />

> <img width="1168" height="368" alt="image" src="https://github.com/user-attachments/assets/5c76825f-9eab-47a4-b57b-939101512335" />


## 04 - flask pod
```
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\01-core-k8s\subsessions> kubectl apply -f .\04-flask-pod\                  
pod/flask-pod created
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\01-core-k8s\subsessions> kubectl get pod flask-pod -n app-core
NAME        READY   STATUS    RESTARTS   AGE
flask-pod   1/1     Running   0          21s
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\01-core-k8s\subsessions> kubectl describe pod flask-pod -n app-core
Name:             flask-pod
Namespace:        app-core
Priority:         0
Service Account:  default
Node:             ip-192-168-88-252.ap-south-1.compute.internal/192.168.88.252
Start Time:       Wed, 24 Jun 2026 19:38:04 +0530
Labels:           app=flask-pod
                  tier=application
Annotations:      <none>
Status:           Running
IP:               192.168.87.138
IPs:
  IP:  192.168.87.138
Containers:
  flask-web:
    Container ID:   containerd://4910b82f5625f4e68287a7d1c69c0e7f2adbfc4a4897c8f0c2abb2bf1be4f9ad
    Image:          prashantdey/appk8stutorial:1.0
    Image ID:       docker.io/prashantdey/appk8stutorial@sha256:37e26231a15922cadecc14656bd996ca5f811066c52e3a640b4bddc9a47a529b
    Port:           5000/TCP (http)
    Host Port:      0/TCP (http)
    State:          Running
      Started:      Wed, 24 Jun 2026 19:38:11 +0530
    Ready:          True
    Restart Count:  0
    Limits:
      cpu:     500m
      memory:  256Mi
    Requests:
      cpu:     100m
      memory:  128Mi
    Environment:
      DB_HOST:           <set to the key 'DB_HOST' of config map 'app-config'>         Optional: false
      DB_PORT:           <set to the key 'DB_PORT' of config map 'app-config'>         Optional: false
      DB_NAME:           <set to the key 'POSTGRES_DB' in secret 'app-secrets'>        Optional: false
      DB_USER:           <set to the key 'POSTGRES_USER' in secret 'app-secrets'>      Optional: false
      DB_PASSWORD:       <set to the key 'POSTGRES_PASSWORD' in secret 'app-secrets'>  Optional: false
      FLASK_SECRET_KEY:  <set to the key 'FLASK_SECRET_KEY' in secret 'app-secrets'>   Optional: false
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-8jdj6 (ro)
Conditions:
  Type                        Status
  PodReadyToStartContainers   True 
  Initialized                 True 
  Ready                       True 
  ContainersReady             True 
  PodScheduled                True 
Volumes:
  kube-api-access-8jdj6:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    Optional:                false
    DownwardAPI:             true
QoS Class:                   Burstable
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type    Reason     Age   From               Message
  ----    ------     ----  ----               -------
  Normal  Scheduled  34s   default-scheduler  Successfully assigned app-core/flask-pod to ip-192-168-88-252.ap-south-1.compute.internal
  Normal  Pulling    33s   kubelet            Pulling image "prashantdey/appk8stutorial:1.0"
  Normal  Pulled     27s   kubelet            Successfully pulled image "prashantdey/appk8stutorial:1.0" in 5.901s (5.901s including waiting). Image size: 48431974 bytes.
  Normal  Created    27s   kubelet            Created container: flask-web
  Normal  Started    27s   kubelet            Started container flask-web
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\01-core-k8s\subsessions> kubectl logs flask-pod -n app-core
[2026-06-24 14:08:12 +0000] [1] [INFO] Starting gunicorn 22.0.0
[2026-06-24 14:08:12 +0000] [1] [INFO] Listening at: http://0.0.0.0:5000 (1)
[2026-06-24 14:08:12 +0000] [1] [INFO] Using worker: gthread
[2026-06-24 14:08:12 +0000] [7] [INFO] Booting worker with pid: 7
[2026-06-24 14:08:12 +0000] [8] [INFO] Booting worker with pid: 8
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\01-core-k8s\subsessions> kubectl get pod -n app-core
NAME                        READY   STATUS    RESTARTS   AGE
flask-pod                   1/1     Running   0          59s
postgres-5cf755b76f-hw7rc   1/1     Running   0          16m
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\01-core-k8s\subsessions> kubectl port-forward -n app-core pod/flask-pod 8080:5000
Forwarding from 127.0.0.1:8080 -> 5000
Forwarding from [::1]:8080 -> 5000
```
> <img width="1321" height="636" alt="image" src="https://github.com/user-attachments/assets/1d690204-5cc7-49e2-a138-b6e9b9885b59" />
> <img width="1447" height="695" alt="image" src="https://github.com/user-attachments/assets/bafcedb0-ae7d-4aa1-92ba-d4ad8e5ae01c" />

```
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s> kubectl delete pod flask-pod -n app-core
pod "flask-pod" deleted from app-core namespace
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s> kubectl get pods -n app-core
NAME                        READY   STATUS    RESTARTS   AGE
postgres-5cf755b76f-hw7rc   1/1     Running   0          19m
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s>
# It does not come back automatically. That is why the next sub-session replaces it with a Deployment.
```
> <img width="1306" height="526" alt="image" src="https://github.com/user-attachments/assets/2971e823-d53c-40fb-b547-5c286c3601c1" />

```
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\01-core-k8s\subsessions> kubectl port-forward -n app-core pod/flask-pod 8080:5000
Forwarding from 127.0.0.1:8080 -> 5000
Forwarding from [::1]:8080 -> 5000
Handling connection for 8080
Handling connection for 8080
Handling connection for 8080
E0624 19:43:10.342716   28504 portforward.go:424] "Unhandled Error" err="an error occurred forwarding 8080 -> 5000: error forwarding port 5000 to pod c94177e2de48b9f57965519d626f38d008b3084be15fdc8a4b8fd38e83cbef68, uid : failed to find sandbox \"c94177e2de48b9f57965519d626f38d008b3084be15fdc8a4b8fd38e83cbef68\" in store: not found"
error: lost connection to pod
```

## 05 - flask deployment
```
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\01-core-k8s\subsessions> kubectl apply -f .\05-flask-deployment\
deployment.apps/flask-web created
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\01-core-k8s\subsessions> kubectl get deployment flask-web -n app-core
NAME        READY   UP-TO-DATE   AVAILABLE   AGE
flask-web   1/2     2            1           18s
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\01-core-k8s\subsessions> kubectl get pods -n app-core -l app=flask-web
NAME                         READY   STATUS    RESTARTS   AGE
flask-web-6b6f976596-4hjvt   1/1     Running   0          24s
flask-web-6b6f976596-88kkv   1/1     Running   0          24s
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\01-core-k8s\subsessions> kubectl rollout status deployment/flask-web -n app-core
deployment "flask-web" successfully rolled out
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\01-core-k8s\subsessions> kubectl get deployment flask-web -n app-core           
NAME        READY   UP-TO-DATE   AVAILABLE   AGE
flask-web   2/2     2            2           60s

# SCALING
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\01-core-k8s\subsessions> kubectl scale deployment flask-web -n app-core --replicas=3
deployment.apps/flask-web scaled
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\01-core-k8s\subsessions> kubectl get pods -n app-core -l app=flask-web
NAME                         READY   STATUS    RESTARTS   AGE
flask-web-6b6f976596-4hjvt   1/1     Running   0          81s
flask-web-6b6f976596-88kkv   1/1     Running   0          81s
flask-web-6b6f976596-cxrj4   0/1     Running   0          10s
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\01-core-k8s\subsessions> kubectl get pods -n app-core -l app=flask-web
NAME                         READY   STATUS    RESTARTS   AGE
flask-web-6b6f976596-4hjvt   1/1     Running   0          89s
flask-web-6b6f976596-88kkv   1/1     Running   0          89s
flask-web-6b6f976596-cxrj4   1/1     Running   0          18s
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\01-core-k8s\subsessions> kubectl get pods -n app-core                 
NAME                         READY   STATUS    RESTARTS   AGE
flask-web-6b6f976596-4hjvt   1/1     Running   0          2m31s
flask-web-6b6f976596-88kkv   1/1     Running   0          2m31s
flask-web-6b6f976596-cxrj4   1/1     Running   0          80s
postgres-5cf755b76f-hw7rc    1/1     Running   0          26m

# SELF HEALING TEST
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\01-core-k8s\subsessions> kubectl delete pod -n app-core -l app=flask-web
pod "flask-web-6b6f976596-4hjvt" deleted from app-core namespace
pod "flask-web-6b6f976596-88kkv" deleted from app-core namespace
pod "flask-web-6b6f976596-cxrj4" deleted from app-core namespace
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\01-core-k8s\subsessions> kubectl get pods -n app-core -w
NAME                         READY   STATUS    RESTARTS   AGE
flask-web-6b6f976596-6kqr4   0/1     Running   0          11s
flask-web-6b6f976596-gqwhr   0/1     Running   0          11s
flask-web-6b6f976596-tnzjw   0/1     Running   0          11s
postgres-5cf755b76f-hw7rc    1/1     Running   0          27m
flask-web-6b6f976596-gqwhr   1/1     Running   0          12s
flask-web-6b6f976596-6kqr4   1/1     Running   0          12s
flask-web-6b6f976596-tnzjw   1/1     Running   0          13s
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\01-core-k8s\subsessions> kubectl get pods -n app-core -w
NAME                         READY   STATUS    RESTARTS   AGE
flask-web-6b6f976596-6kqr4   1/1     Running   0          33s
flask-web-6b6f976596-gqwhr   1/1     Running   0          33s
flask-web-6b6f976596-tnzjw   1/1     Running   0          33s
postgres-5cf755b76f-hw7rc    1/1     Running   0          27m
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\01-core-k8s\subsessions> kubectl get pods -n app-core   
NAME                         READY   STATUS    RESTARTS   AGE
flask-web-6b6f976596-6kqr4   1/1     Running   0          49s
flask-web-6b6f976596-gqwhr   1/1     Running   0          49s
flask-web-6b6f976596-tnzjw   1/1     Running   0          49s
postgres-5cf755b76f-hw7rc    1/1     Running   0          27m
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\01-core-k8s\subsessions> 
# The Deployment creates replacement Pods.
```


## 06 flask services - clusterIP NodePort LoadBalancer
```
# cluster IP
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\01-core-k8s\subsessions> kubectl apply -f 06-flask-services/01-flask-service-clusterip.yml            
service/flask-web-clusterip created
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\01-core-k8s\subsessions> kubectl port-forward -n app-core service/flask-web-clusterip 8080:80
Forwarding from 127.0.0.1:8080 -> 5000
Forwarding from [::1]:8080 -> 5000
Handling connection for 8080


PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\01-core-k8s\subsessions> kubectl get all -n app-core
NAME                             READY   STATUS    RESTARTS   AGE
pod/flask-web-6b6f976596-6kqr4   1/1     Running   0          12m
pod/flask-web-6b6f976596-gqwhr   1/1     Running   0          12m
pod/flask-web-6b6f976596-tnzjw   1/1     Running   0          12m
pod/postgres-5cf755b76f-hw7rc    1/1     Running   0          38m

NAME                          TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)    AGE
service/flask-web-clusterip   ClusterIP   10.100.190.165   <none>        80/TCP     3m55s
service/postgres              ClusterIP   10.100.48.144    <none>        5432/TCP   39m

NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/flask-web   3/3     3            3           15m
deployment.apps/postgres    1/1     1            1           39m

NAME                                   DESIRED   CURRENT   READY   AGE
replicaset.apps/flask-web-6b6f976596   3         3         3       15m
replicaset.apps/postgres-5cf755b76f    1         1         1       39m


PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\01-core-k8s\subsessions> kubectl get endpoints -n app-core
Warning: v1 Endpoints is deprecated in v1.33+; use discovery.k8s.io/v1 EndpointSlice
NAME                  ENDPOINTS                                                     AGE
flask-web-clusterip   192.168.22.142:5000,192.168.68.191:5000,192.168.92.157:5000   7m28s
postgres              192.168.75.248:5432                                           42m
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\01-core-k8s\subsessions> 
```
> <img width="1529" height="726" alt="image" src="https://github.com/user-attachments/assets/0617c756-008b-4c98-a2dd-61abfb1a8fc3" />
> <img width="1492" height="869" alt="image" src="https://github.com/user-attachments/assets/f3f8c5e6-4640-4c01-88c2-b2e22c107923" />

```
# NodePort
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\01-core-k8s\subsessions> kubectl apply -f 06-flask-services/02-flask-service-nodeport.yml            
service/flask-web-nodeport created
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\01-core-k8s\subsessions> kubectl get service flask-web-nodeport -n app-core
NAME                 TYPE       CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
flask-web-nodeport   NodePort   10.100.160.163   <none>        80:30080/TCP   9s
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\01-core-k8s\subsessions> kubectl get endpoints -n app-core                      
Warning: v1 Endpoints is deprecated in v1.33+; use discovery.k8s.io/v1 EndpointSlice
NAME                  ENDPOINTS                                                     AGE
flask-web-clusterip   192.168.22.142:5000,192.168.68.191:5000,192.168.92.157:5000   11m
flask-web-nodeport    192.168.22.142:5000,192.168.68.191:5000,192.168.92.157:5000   19s
postgres              192.168.75.248:5432                                           46m

```

```
# LoadBalancer


PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\01-core-k8s\subsessions> kubectl apply -f 06-flask-services/03-flask-service-loadbalancer.yml            
service/flask-web-loadbalancer created
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\01-core-k8s\subsessions> kubectl get service flask-web-loadbalancer -n app-core
NAME                     TYPE           CLUSTER-IP      EXTERNAL-IP                                                               PORT(S)      AGE
flask-web-loadbalancer   LoadBalancer   10.100.18.224   a1f99585b3ffa465f979ddd2d116d702-162849287.ap-south-1.elb.amazonaws.com   80:31045/TCP   9s
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\01-core-k8s\subsessions> kubectl get endpoints -n app-core                      
Warning: v1 Endpoints is deprecated in v1.33+; use discovery.k8s.io/v1 EndpointSlice
NAME                     ENDPOINTS                                                     AGE
flask-web-clusterip      192.168.22.142:5000,192.168.68.191:5000,192.168.92.157:5000   12m
flask-web-loadbalancer   192.168.22.142:5000,192.168.68.191:5000,192.168.92.157:5000   19s
flask-web-nodeport       192.168.22.142:5000,192.168.68.191:5000,192.168.92.157:5000   57s
postgres                 192.168.75.248:5432                                           47m
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\01-core-k8s\subsessions>

#If the Flask service has no endpoints, check Deployment Pod labels and Service selectors.
```

> <img width="1159" height="609" alt="image" src="https://github.com/user-attachments/assets/7a0d8b97-2c75-4ae5-be81-77490d617183" />


## Q&A 

### 1. Why do we create a Namespace first?
A Namespace is a logical partition within a Kubernetes cluster that provides isolation and organization. Creating a Namespace first establishes a dedicated environment where you can deploy your application resources without interfering with other applications or users. This is particularly useful in multi-tenant environments or when managing multiple projects. Namespaces allow you to apply resource quotas, access controls, and policies specific to your application, making it easier to manage resource allocation and maintain security boundaries. By establishing a Namespace upfront, you ensure a clean, organized deployment environment from the start.

### 2. Why does Flask need a ConfigMap and Secret?
Flask applications require external configuration and sensitive credentials that should be managed separately from the application code. A ConfigMap stores non-sensitive configuration data (like Flask environment variables, feature flags, or application settings) as key-value pairs that Flask can read at runtime. A Secret, on the other hand, stores sensitive information such as database passwords, API keys, or authentication tokens in an encrypted or base64-encoded format. By decoupling configuration from the application image, you can change settings without rebuilding the container, and by using Secrets, you prevent sensitive data from being hardcoded in your source code or accidentally exposed in logs.

### 3. Why does PostgreSQL need a Service even though users do not access it directly?
PostgreSQL needs a Service to provide a stable, DNS-resolvable hostname and load-balanced access point for other Pods within the cluster. Even though external users don't access PostgreSQL directly, other Pods in your cluster (like your Flask application) need a reliable way to communicate with it. A Service abstracts the underlying Pods and provides a consistent network interface. If a PostgreSQL Pod fails and is replaced, the Service's DNS name remains the same, so dependent applications don't need to know about Pod IP changes. This internal Service acts as a gateway, ensuring stable communication between microservices within the cluster.

### 4. What is the limitation of a standalone Pod?
A standalone Pod is ephemeral and lacks self-healing capabilities. If a Pod crashes or is deleted, there's no mechanism to automatically recreate it—your application simply goes down. Additionally, a standalone Pod doesn't provide scalability; you cannot easily manage multiple replicas for load distribution. There's also no built-in rolling update or versioning strategy, making it difficult to deploy new versions of your application without downtime. Standalone Pods are useful for one-time tasks or debugging, but for production applications, they don't provide the reliability, resilience, or operational features needed in a distributed system.

### 5. What does a Deployment add on top of Pods?
A Deployment is a higher-level abstraction that manages Pods through a ReplicaSet. It adds several critical features: self-healing (automatically restarts failed Pods), replication (maintains a desired number of running instances for load distribution), rolling updates (deploys new versions gradually without downtime), and rollback (reverts to previous versions if issues occur). A Deployment also manages the lifecycle of Pods declaratively—you specify the desired state, and Kubernetes continuously works to maintain that state. This makes Deployments the production-ready choice for running stateless applications with high availability and zero-downtime deployments.

### 6. How does a Service know which Pods should receive traffic?
A Service uses labels and selectors to identify which Pods should receive traffic. When you create a Service, you specify a selector—a set of key-value label pairs. Kubernetes automatically discovers all Pods that match those labels and includes them as endpoints for the Service. When traffic arrives at the Service, it's load-balanced across these matching Pods. If a Pod is created with matching labels, it's automatically added to the Service's endpoint pool. Conversely, if a Pod is terminated or its labels change, it's removed from the pool. This dynamic, label-based mechanism decouples Services from Pods, allowing flexible and scalable network routing without hard-coded Pod references.
