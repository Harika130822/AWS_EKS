
 # Pre-requisite to create and EKS cluster
 ```
 eksctl create cluster --name sample-cluster-cls4 --region ap-south-1 --nodegroup-name standard-workers --node-type t3.medium --nodes 2 --nodes-min 2 --nodes-max 3

 eksctl delete cluster --name sample-cluster-cls4 --region ap-south-1
```


```


abhis@Bharathgift MINGW64 ~/Harika/K8s/Class4
$ kubectl apply -n argocd --server-side --force-conflicts -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
customresourcedefinition.apiextensions.k8s.io/applications.argoproj.io serverside-applied
customresourcedefinition.apiextensions.k8s.io/applicationsets.argoproj.io serverside-applied
customresourcedefinition.apiextensions.k8s.io/appprojects.argoproj.io serverside-applied
serviceaccount/argocd-application-controller serverside-applied
serviceaccount/argocd-applicationset-controller serverside-applied
serviceaccount/argocd-dex-server serverside-applied
serviceaccount/argocd-notifications-controller serverside-applied
serviceaccount/argocd-redis serverside-applied
serviceaccount/argocd-repo-server serverside-applied
serviceaccount/argocd-server serverside-applied
role.rbac.authorization.k8s.io/argocd-application-controller serverside-applied
role.rbac.authorization.k8s.io/argocd-applicationset-controller serverside-applied
role.rbac.authorization.k8s.io/argocd-dex-server serverside-applied
role.rbac.authorization.k8s.io/argocd-notifications-controller serverside-applied
role.rbac.authorization.k8s.io/argocd-redis serverside-applied
role.rbac.authorization.k8s.io/argocd-server serverside-applied
clusterrole.rbac.authorization.k8s.io/argocd-application-controller serverside-applied
clusterrole.rbac.authorization.k8s.io/argocd-applicationset-controller serverside-applied
clusterrole.rbac.authorization.k8s.io/argocd-server serverside-applied
rolebinding.rbac.authorization.k8s.io/argocd-application-controller serverside-applied
rolebinding.rbac.authorization.k8s.io/argocd-applicationset-controller serverside-applied
rolebinding.rbac.authorization.k8s.io/argocd-dex-server serverside-applied
rolebinding.rbac.authorization.k8s.io/argocd-notifications-controller serverside-applied
rolebinding.rbac.authorization.k8s.io/argocd-redis serverside-applied
rolebinding.rbac.authorization.k8s.io/argocd-server serverside-applied
clusterrolebinding.rbac.authorization.k8s.io/argocd-application-controller serverside-applied
clusterrolebinding.rbac.authorization.k8s.io/argocd-applicationset-controller serverside-applied
clusterrolebinding.rbac.authorization.k8s.io/argocd-server serverside-applied
configmap/argocd-cm serverside-applied
configmap/argocd-cmd-params-cm serverside-applied
configmap/argocd-gpg-keys-cm serverside-applied
configmap/argocd-notifications-cm serverside-applied
configmap/argocd-rbac-cm serverside-applied
configmap/argocd-ssh-known-hosts-cm serverside-applied
configmap/argocd-tls-certs-cm serverside-applied
secret/argocd-notifications-secret serverside-applied
secret/argocd-secret serverside-applied
service/argocd-applicationset-controller serverside-applied
service/argocd-dex-server serverside-applied
service/argocd-metrics serverside-applied
service/argocd-notifications-controller-metrics serverside-applied
service/argocd-redis serverside-applied
service/argocd-repo-server serverside-applied
service/argocd-server serverside-applied
service/argocd-server-metrics serverside-applied
deployment.apps/argocd-applicationset-controller serverside-applied
deployment.apps/argocd-dex-server serverside-applied
deployment.apps/argocd-notifications-controller serverside-applied
deployment.apps/argocd-redis serverside-applied
deployment.apps/argocd-repo-server serverside-applied
deployment.apps/argocd-server serverside-applied
statefulset.apps/argocd-application-controller serverside-applied
networkpolicy.networking.k8s.io/argocd-application-controller-network-policy serverside-applied
networkpolicy.networking.k8s.io/argocd-applicationset-controller-network-policy serverside-applied
networkpolicy.networking.k8s.io/argocd-dex-server-network-policy serverside-applied
networkpolicy.networking.k8s.io/argocd-notifications-controller-network-policy serverside-applied
networkpolicy.networking.k8s.io/argocd-redis-network-policy serverside-applied
networkpolicy.networking.k8s.io/argocd-repo-server-network-policy serverside-applied
networkpolicy.networking.k8s.io/argocd-server-network-policy serverside-applied

abhis@Bharathgift MINGW64 ~/Harika/K8s/Class4
$ kubectl get pods -n argocd
NAME                                                READY   STATUS            RESTARTS   AGE
argocd-application-controller-0                     0/1     Running           0          19s
argocd-applicationset-controller-78d66cfd5b-5gw2p   1/1     Running           0          19s
argocd-dex-server-795b9b7569-d44s6                  0/1     PodInitializing   0          19s
argocd-notifications-controller-85db9b54dd-sgm9k    1/1     Running           0          19s
argocd-redis-5c7789bdf-zvp7h                        0/1     PodInitializing   0          19s
argocd-repo-server-575f949f6-m8kj8                  0/1     Running           0          19s
argocd-server-78bf4dfbcb-ff4z8                      0/1     Running           0          19s

abhis@Bharathgift MINGW64 ~/Harika/K8s/Class4
$ kubectl get services -n argocd
NAME                                      TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)                      AGE
argocd-applicationset-controller          ClusterIP   10.100.94.178    <none>        7000/TCP,8080/TCP            39s
argocd-dex-server                         ClusterIP   10.100.12.47     <none>        5556/TCP,5557/TCP,5558/TCP   39s
argocd-metrics                            ClusterIP   10.100.47.77     <none>        8082/TCP                     39s
argocd-notifications-controller-metrics   ClusterIP   10.100.150.134   <none>        9001/TCP                     39s
argocd-redis                              ClusterIP   10.100.144.64    <none>        6379/TCP                     39s
argocd-repo-server                        ClusterIP   10.100.23.218    <none>        8081/TCP,8084/TCP            38s
argocd-server                             ClusterIP   10.100.16.239    <none>        80/TCP,443/TCP               38s
argocd-server-metrics                     ClusterIP   10.100.74.90     <none>        8083/TCP                     38s

abhis@Bharathgift MINGW64 ~/Harika/K8s/Class4
$ kubectl get crd | grep argoproj.io
applications.argoproj.io                        2026-06-21T05:14:54Z
applicationsets.argoproj.io                     2026-06-21T05:14:55Z
appprojects.argoproj.io                         2026-06-21T05:14:55Z

abhis@Bharathgift MINGW64 ~/Harika/K8s/Class4
$ kubectl get pods -n argocd
NAME                                                READY   STATUS    RESTARTS   AGE
argocd-application-controller-0                     1/1     Running   0          6m50s
argocd-applicationset-controller-78d66cfd5b-5gw2p   1/1     Running   0          6m50s
argocd-dex-server-795b9b7569-d44s6                  1/1     Running   0          6m50s
argocd-notifications-controller-85db9b54dd-sgm9k    1/1     Running   0          6m50s
argocd-redis-5c7789bdf-zvp7h                        1/1     Running   0          6m50s
argocd-repo-server-575f949f6-m8kj8                  1/1     Running   0          6m50s
argocd-server-78bf4dfbcb-ff4z8                      1/1     Running   0          6m50s

abhis@Bharathgift MINGW64 ~/Harika/K8s/Class4
$ kubectl get services -n argocd
NAME                                      TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)                      AGE
argocd-applicationset-controller          ClusterIP   10.100.94.178    <none>        7000/TCP,8080/TCP            7m15s
argocd-dex-server                         ClusterIP   10.100.12.47     <none>        5556/TCP,5557/TCP,5558/TCP   7m15s
argocd-metrics                            ClusterIP   10.100.47.77     <none>        8082/TCP                     7m15s
argocd-notifications-controller-metrics   ClusterIP   10.100.150.134   <none>        9001/TCP                     7m15s
argocd-redis                              ClusterIP   10.100.144.64    <none>        6379/TCP                     7m15s
argocd-repo-server                        ClusterIP   10.100.23.218    <none>        8081/TCP,8084/TCP            7m14s
argocd-server                             ClusterIP   10.100.16.239    <none>        80/TCP,443/TCP               7m14s
argocd-server-metrics                     ClusterIP   10.100.74.90     <none>        8083/TCP                     7m14s

abhis@Bharathgift MINGW64 ~/Harika/K8s/Class4
$ kubectl get crd | grep argoproj.io
applications.argoproj.io                        2026-06-21T05:14:54Z
applicationsets.argoproj.io                     2026-06-21T05:14:55Z
appprojects.argoproj.io                         2026-06-21T05:14:55Z

abhis@Bharathgift MINGW64 ~/Harika/K8s/Class4
$ kubectl wait --for=condition=Available deployment/argocd-server -n argocd --timeout=180s
deployment.apps/argocd-server condition met

abhis@Bharathgift MINGW64 ~/Harika/K8s/Class4
$ kubectl port-forward svc/argocd-server -n argocd 8080:443
Forwarding from 127.0.0.1:8080 -> 8080
Forwarding from [::1]:8080 -> 8080
Handling connection for 8080
Handling connection for 8080
Handling connection for 8080
Handling connection for 8080
Handling connection for 8080
```

```
abhis@Bharathgift MINGW64 ~/Harika/K8s/Class4
$ kubectl get secret argocd-initial-admin-secret -n argocd -o jsonpath="{.data.password}"
RS1yVmUtVFJBYll5V3BIVw==
```


Browse: https://localhost:8080

https://localhost:8080/applications/argocd/test?view=tree&resource=

Login credentials
admin
password

> <img width="1128" height="894" alt="image" src="https://github.com/user-attachments/assets/263f1cf2-38ee-465d-82d8-cb89fcb150fa" />

## creation of application
> <img width="1557" height="961" alt="image" src="https://github.com/user-attachments/assets/a0833c44-f20a-4947-8ddc-6dc827b949e5" />
> <img width="1233" height="312" alt="image" src="https://github.com/user-attachments/assets/6501e54c-2def-4574-a651-31b6849c77a6" />
> <img width="1212" height="239" alt="image" src="https://github.com/user-attachments/assets/cf1c55f1-c26b-484f-af69-c9e5cd85fabd" />
> <img width="1210" height="916" alt="image" src="https://github.com/user-attachments/assets/35669690-1d8c-47a2-b5d5-3015d176f6b1" />
> <img width="1345" height="398" alt="image" src="https://github.com/user-attachments/assets/fe39dadc-4565-4336-a099-f194615d02f7" />
> <img width="1514" height="694" alt="image" src="https://github.com/user-attachments/assets/1cdf416e-0fe1-4a85-84f2-93e55085f799" />
> <img width="1222" height="430" alt="image" src="https://github.com/user-attachments/assets/dbd0505e-b7a5-4c90-bd4d-3671ead2c199" />
> <img width="1305" height="689" alt="image" src="https://github.com/user-attachments/assets/0d604918-9955-46a3-a473-39b0991c4ec4" />
> <img width="1026" height="970" alt="image" src="https://github.com/user-attachments/assets/fb1a726e-406e-4411-9533-3c46e9d5b5bb" />
## Do manual sync and auto-sync - check the history
> <img width="1481" height="1002" alt="image" src="https://github.com/user-attachments/assets/95b05d99-4e2c-4cd4-9325-a29130421a2c" />
## commit the deployement.yml changes
> <img width="815" height="839" alt="image" src="https://github.com/user-attachments/assets/84ad4280-a8f1-42b1-9fc4-74ed758ea8ee" />
> <img width="1422" height="526" alt="image" src="https://github.com/user-attachments/assets/356624e3-e494-42c1-80f3-6ec2f28528ba" />


# CleanUp

> <img width="1276" height="691" alt="image" src="https://github.com/user-attachments/assets/86e96f2f-a331-4dec-ac23-ef32e298bb13" />
> <img width="1195" height="499" alt="image" src="https://github.com/user-attachments/assets/37ee21a3-c227-4ff7-9f3c-4a553652a247" />




```

abhis@Bharathgift MINGW64 ~/Harika/K8s/Class4
$ kubectl get all -n argocd-demo
No resources found in argocd-demo namespace.

abhis@Bharathgift MINGW64 ~/Harika/K8s/Class4
$ kubectl delete namespace argocd-demo --ignore-not-found

abhis@Bharathgift MINGW64 ~/Harika/K8s/Class4
$ kubectl delete -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml --ignore-not-found
Warning: deleting cluster-scoped resources, not scoped to the provided namespace
customresourcedefinition.apiextensions.k8s.io "applications.argoproj.io" deleted
customresourcedefinition.apiextensions.k8s.io "applicationsets.argoproj.io" deleted
customresourcedefinition.apiextensions.k8s.io "appprojects.argoproj.io" deleted
serviceaccount "argocd-application-controller" deleted from argocd namespace
serviceaccount "argocd-applicationset-controller" deleted from argocd namespace
serviceaccount "argocd-dex-server" deleted from argocd namespace
serviceaccount "argocd-notifications-controller" deleted from argocd namespace
serviceaccount "argocd-redis" deleted from argocd namespace
serviceaccount "argocd-repo-server" deleted from argocd namespace
serviceaccount "argocd-server" deleted from argocd namespace
role.rbac.authorization.k8s.io "argocd-application-controller" deleted from argocd namespace
role.rbac.authorization.k8s.io "argocd-applicationset-controller" deleted from argocd namespace
role.rbac.authorization.k8s.io "argocd-dex-server" deleted from argocd namespace
role.rbac.authorization.k8s.io "argocd-notifications-controller" deleted from argocd namespace
role.rbac.authorization.k8s.io "argocd-redis" deleted from argocd namespace
role.rbac.authorization.k8s.io "argocd-server" deleted from argocd namespace
clusterrole.rbac.authorization.k8s.io "argocd-application-controller" deleted
clusterrole.rbac.authorization.k8s.io "argocd-applicationset-controller" deleted
clusterrole.rbac.authorization.k8s.io "argocd-server" deleted
rolebinding.rbac.authorization.k8s.io "argocd-application-controller" deleted from argocd namespace
rolebinding.rbac.authorization.k8s.io "argocd-applicationset-controller" deleted from argocd namespace
rolebinding.rbac.authorization.k8s.io "argocd-dex-server" deleted from argocd namespace
rolebinding.rbac.authorization.k8s.io "argocd-notifications-controller" deleted from argocd namespace
rolebinding.rbac.authorization.k8s.io "argocd-redis" deleted from argocd namespace
rolebinding.rbac.authorization.k8s.io "argocd-server" deleted from argocd namespace
clusterrolebinding.rbac.authorization.k8s.io "argocd-application-controller" deleted
clusterrolebinding.rbac.authorization.k8s.io "argocd-applicationset-controller" deleted
clusterrolebinding.rbac.authorization.k8s.io "argocd-server" deleted
configmap "argocd-cm" deleted from argocd namespace
configmap "argocd-cmd-params-cm" deleted from argocd namespace
configmap "argocd-gpg-keys-cm" deleted from argocd namespace
configmap "argocd-notifications-cm" deleted from argocd namespace
configmap "argocd-rbac-cm" deleted from argocd namespace
configmap "argocd-ssh-known-hosts-cm" deleted from argocd namespace
configmap "argocd-tls-certs-cm" deleted from argocd namespace
secret "argocd-notifications-secret" deleted from argocd namespace
secret "argocd-secret" deleted from argocd namespace
service "argocd-applicationset-controller" deleted from argocd namespace
service "argocd-dex-server" deleted from argocd namespace
service "argocd-metrics" deleted from argocd namespace
service "argocd-notifications-controller-metrics" deleted from argocd namespace
service "argocd-redis" deleted from argocd namespace
service "argocd-repo-server" deleted from argocd namespace
service "argocd-server" deleted from argocd namespace
service "argocd-server-metrics" deleted from argocd namespace
deployment.apps "argocd-applicationset-controller" deleted from argocd namespace
deployment.apps "argocd-dex-server" deleted from argocd namespace
deployment.apps "argocd-notifications-controller" deleted from argocd namespace
deployment.apps "argocd-redis" deleted from argocd namespace
deployment.apps "argocd-repo-server" deleted from argocd namespace
deployment.apps "argocd-server" deleted from argocd namespace
statefulset.apps "argocd-application-controller" deleted from argocd namespace
networkpolicy.networking.k8s.io "argocd-application-controller-network-policy" deleted from argocd namespace
networkpolicy.networking.k8s.io "argocd-applicationset-controller-network-policy" deleted from argocd namespace
networkpolicy.networking.k8s.io "argocd-dex-server-network-policy" deleted from argocd namespace
networkpolicy.networking.k8s.io "argocd-notifications-controller-network-policy" deleted from argocd namespace
networkpolicy.networking.k8s.io "argocd-redis-network-policy" deleted from argocd namespace
networkpolicy.networking.k8s.io "argocd-repo-server-network-policy" deleted from argocd namespace
networkpolicy.networking.k8s.io "argocd-server-network-policy" deleted from argocd namespace

abhis@Bharathgift MINGW64 ~/Harika/K8s/Class4
$ kubectl delete -f subsessions/01-prerequisites-and-install/01-argocd-namespace.yml --ignore-not-found
error: the path "subsessions/01-prerequisites-and-install/01-argocd-namespace.yml" does not exist

abhis@Bharathgift MINGW64 ~/Harika/K8s/Class4
$ kubectl get namespace argocd argocd-demo
NAME     STATUS   AGE
argocd   Active   60m
Error from server (NotFound): namespaces "argocd-demo" not found

abhis@Bharathgift MINGW64 ~/Harika/K8s/Class4
$ kubectl get namespace argocd argocd-demo
Error from server (NotFound): namespaces "argocd" not found
Error from server (NotFound): namespaces "argocd-demo" not found

abhis@Bharathgift MINGW64 ~/Harika/K8s/Class4

```






