```
eksctl create cluster --name prac-cluster-rbac --region ap-south-1 --nodegroup-name standard-workers --node-type t3.medium --nodes 2 --nodes-min 2 --nodes-max 3

aws eks --region ap-south-1 update-kubeconfig --name prac-cluster-rbac

kubectl config get-contexts

eksctl delete cluster --name prac-cluster-rbac --region ap-south-1
```

## created few pods in namespace app-core
```
PS C:\Users\abhis\Harika\K8s\Class4\LearningK8s\sessions\01-core-k8s> kubectl apply -f subsessions/01-namespace/
namespace/app-core created
PS C:\Users\abhis\Harika\K8s\Class4\LearningK8s\sessions\01-core-k8s> kubectl get namespaces
NAME              STATUS   AGE
app-core          Active   11s
default           Active   52m
kube-node-lease   Active   52m
kube-public       Active   52m
kube-system       Active   52m
PS C:\Users\abhis\Harika\K8s\Class4\LearningK8s\sessions\01-core-k8s> kubectl get all -n app-core
No resources found in app-core namespace.
PS C:\Users\abhis\Harika\K8s\Class4\LearningK8s\sessions\01-core-k8s> kubectl apply -f subsessions/02-configmap-secret/
configmap/app-config created
secret/app-secrets created
PS C:\Users\abhis\Harika\K8s\Class4\LearningK8s\sessions\01-core-k8s> kubectl get configmap -n app-core
NAME               DATA   AGE
app-config         2      17s
kube-root-ca.crt   1      63s
PS C:\Users\abhis\Harika\K8s\Class4\LearningK8s\sessions\01-core-k8s> kubectl apply -f subsessions/03-postgres-deployment-service/
deployment.apps/postgres created
service/postgres created
PS C:\Users\abhis\Harika\K8s\Class4\LearningK8s\sessions\01-core-k8s> kubectl apply -f subsessions/04-flask-pod/
pod/flask-pod created
PS C:\Users\abhis\Harika\K8s\Class4\LearningK8s\sessions\01-core-k8s>
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s> kubectl get namespaces
NAME              STATUS   AGE
app-core          Active   4m35s
default           Active   56m
kube-node-lease   Active   56m
kube-public       Active   56m
kube-system       Active   56m
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s> kubectl get pods -n app-core
NAME                        READY   STATUS    RESTARTS   AGE
flask-pod                   1/1     Running   0          2m44s
postgres-5cf755b76f-lh5q4   1/1     Running   0          3m17s
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s> cd .\sessions\14-rbac-and-identity\
```

## 01- prerequisites and namespaces
```
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\14-rbac-and-identity> kubectl version
Client Version: v1.34.1
Kustomize Version: v5.7.1
Server Version: v1.34.8-eks-a3a0722
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\14-rbac-and-identity> kubectl get nodes
NAME                                            STATUS   ROLES    AGE   VERSION
ip-192-168-19-52.ap-south-1.compute.internal    Ready    <none>   56m   v1.34.9-eks-93b80c6
ip-192-168-89-165.ap-south-1.compute.internal   Ready    <none>   56m   v1.34.9-eks-93b80c6
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\14-rbac-and-identity> kubectl auth can-i create namespaces
Warning: resource 'namespaces' is not namespace scoped

yes
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\14-rbac-and-identity> kubectl apply -f subsessions/01-prerequisites-and-namespace/
namespace/rbac-lab created
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\14-rbac-and-identity> kubectl get namespace rbac-lab
NAME       STATUS   AGE
rbac-lab   Active   12s
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\14-rbac-and-identity> kubectl get all -n rbac-lab
No resources found in rbac-lab namespace.
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\14-rbac-and-identity> 
```

## 02 service-accounts-and-deny-by-default
```
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\14-rbac-and-identity> kubectl apply -f subsessions/02-service-accounts-and-deny-by-default/
serviceaccount/dev-reader created
serviceaccount/app-auditor created
serviceaccount/platform-viewer created
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\14-rbac-and-identity> kubectl get serviceaccounts -n rbac-lab
NAME              SECRETS   AGE
app-auditor       0         9s
default           0         3m6s
dev-reader        0         9s
platform-viewer   0         9s
```
**verify**
```
abhis@Bharathgift MINGW64 ~/Harika/K8s/Practice/LearningK8s (main)
$ DEV_READER=system:serviceaccount:rbac-lab:dev-reader
APP_AUDITOR=system:serviceaccount:rbac-lab:app-auditor
PLATFORM_VIEWER=system:serviceaccount:rbac-lab:platform-viewer

abhis@Bharathgift MINGW64 ~/Harika/K8s/Practice/LearningK8s (main)
$ kubectl auth can-i list pods -n rbac-lab --as="$DEV_READER"
kubectl auth can-i get secrets -n rbac-lab --as="$DEV_READER"
kubectl auth can-i list services -n rbac-lab --as="$APP_AUDITOR"
kubectl auth can-i list nodes --as="$PLATFORM_VIEWER"
no
no
no
Warning: resource 'nodes' is not namespace scoped

no
```

## 03 Role and Role binding
```
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\14-rbac-and-identity> kubectl apply -f subsessions/03-namespace-role-and-rolebinding/
role.rbac.authorization.k8s.io/pod-reader created
rolebinding.rbac.authorization.k8s.io/dev-reader-pod-reader created
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\14-rbac-and-identity> kubectl get all -n rbac-lab
No resources found in rbac-lab namespace.
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\14-rbac-and-identity> kubectl get serviceaccount -n rbac-lab
NAME              SECRETS   AGE
app-auditor       0         7m58s
default           0         10m
dev-reader        0         7m58s
platform-viewer   0         7m58s
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\14-rbac-and-identity> kubectl get role -n rbac-lab          
NAME         CREATED AT
pod-reader   2026-06-27T07:29:52Z
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\14-rbac-and-identity> kubectl get rolebinding -n rbac-lab
NAME                    ROLE              AGE
dev-reader-pod-reader   Role/pod-reader   58s
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\14-rbac-and-identity> kubectl describe role pod-reader -n rbac-lab
Name:         pod-reader
Labels:       training-session=rbac
Annotations:  <none>
PolicyRule:
  Resources         Non-Resource URLs  Resource Names  Verbs
  ---------         -----------------  --------------  -----
  pods/log          []                 []              [get list watch]
  pods              []                 []              [get list watch]
  deployments.apps  []                 []              [get list watch]
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\14-rbac-and-identity> kubectl describe rolebinding dev-reader-pod-reader -n rbac-lab
Name:         dev-reader-pod-reader
Labels:       training-session=rbac
Annotations:  <none>
Role:
  Kind:  Role
  Name:  pod-reader
Subjects:
  Kind            Name        Namespace
  ----            ----        ---------
  ServiceAccount  dev-reader  rbac-lab
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\14-rbac-and-identity>
```
**verify**
```
abhis@Bharathgift MINGW64 ~/Harika/K8s/Practice/LearningK8s (main)
$ kubectl auth can-i list pods -n rbac-lab --as="$DEV_READER"
kubectl auth can-i get pods --subresource=log -n rbac-lab --as="$DEV_READER"
kubectl auth can-i list deployments.apps -n rbac-lab --as="$DEV_READER"
yes
yes
yes
abhis@Bharathgift MINGW64 ~/Harika/K8s/Practice/LearningK8s (main)
abhis@Bharathgift MINGW64 ~/Harika/K8s/Practice/LearningK8s (main)
$ kubectl auth can-i delete pods -n rbac-lab --as="$DEV_READER"
kubectl auth can-i create deployments.apps -n rbac-lab --as="$DEV_READER"
kubectl auth can-i get secrets -n rbac-lab --as="$DEV_READER"
kubectl auth can-i list pods -n default --as="$DEV_READER"
no
no
no
no

# `roleRef` is immutable. If a RoleBinding points to the wrong Role, delete and recreate the RoleBinding instead of trying to edit `roleRef`
```

## 04 Cluster Role with Rolebinding
```
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\14-rbac-and-identity> kubectl apply -f subsessions/04-clusterrole-with-rolebinding/
clusterrole.rbac.authorization.k8s.io/workload-viewer created
rolebinding.rbac.authorization.k8s.io/app-auditor-workload-viewer created
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\14-rbac-and-identity> kubectl describe clusterrole workload-viewer
Name:         workload-viewer
Labels:       training-session=rbac
Annotations:  <none>
PolicyRule:
  Resources          Non-Resource URLs  Resource Names  Verbs
  ---------          -----------------  --------------  -----
  configmaps         []                 []              [get list watch]
  endpoints          []                 []              [get list watch]
  pods               []                 []              [get list watch]
  services           []                 []              [get list watch]
  daemonsets.apps    []                 []              [get list watch]
  deployments.apps   []                 []              [get list watch]
  replicasets.apps   []                 []              [get list watch]
  statefulsets.apps  []                 []              [get list watch]
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\14-rbac-and-identity> kubectl describe rolebinding app-auditor-workload-viewer -n rbac-lab
Name:         app-auditor-workload-viewer
Labels:       training-session=rbac
Annotations:  <none>
Role:
  Kind:  ClusterRole
  Name:  workload-viewer
Subjects:
  Kind            Name         Namespace
  ----            ----         ---------
  ServiceAccount  app-auditor  rbac-lab
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\14-rbac-and-identity>
```
**verify**
```
abhis@Bharathgift MINGW64 ~/Harika/K8s/Practice/LearningK8s (main)
$ APP_AUDITOR=system:serviceaccount:rbac-lab:app-auditor

kubectl auth can-i list services -n rbac-lab --as="$APP_AUDITOR"
kubectl auth can-i list deployments.apps -n rbac-lab --as="$APP_AUDITOR"
kubectl auth can-i list configmaps -n rbac-lab --as="$APP_AUDITOR"
yes
yes
yes

abhis@Bharathgift MINGW64 ~/Harika/K8s/Practice/LearningK8s (main)
$ kubectl auth can-i list services -n default --as="$APP_AUDITOR"
kubectl auth can-i list nodes --as="$APP_AUDITOR"
kubectl auth can-i get secrets -n rbac-lab --as="$APP_AUDITOR"
no
Warning: resource 'nodes' is not namespace scoped

no
no

```

## 05 ClusterRoleBinding for Cluster scope
```
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\14-rbac-and-identity> kubectl apply -f subsessions/05-clusterrolebinding-for-cluster-scope/
clusterrole.rbac.authorization.k8s.io/node-reader created
clusterrolebinding.rbac.authorization.k8s.io/platform-viewer-node-reader created
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\14-rbac-and-identity> kubectl describe clusterrole node-reader
Name:         node-reader
Labels:       training-session=rbac
Annotations:  <none>
PolicyRule:
  Resources  Non-Resource URLs  Resource Names  Verbs
  ---------  -----------------  --------------  -----
  nodes      []                 []              [get list watch]
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\14-rbac-and-identity> kubectl describe clusterrolebinding platform-viewer-node-reader
Name:         platform-viewer-node-reader
Labels:       training-session=rbac
Annotations:  <none>
Role:
  Kind:  ClusterRole
  Name:  node-reader
Subjects:
  Kind            Name             Namespace
  ----            ----             ---------
  ServiceAccount  platform-viewer  rbac-lab
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\14-rbac-and-identity> 
```
**verify**
```
abhis@Bharathgift MINGW64 ~/Harika/K8s/Practice/LearningK8s (main)
$ PLATFORM_VIEWER=system:serviceaccount:rbac-lab:platform-viewer

kubectl auth can-i list nodes --as="$PLATFORM_VIEWER"
kubectl auth can-i get nodes --as="$PLATFORM_VIEWER"
Warning: resource 'nodes' is not namespace scoped

yes
Warning: resource 'nodes' is not namespace scoped

yes

abhis@Bharathgift MINGW64 ~/Harika/K8s/Practice/LearningK8s (main)
$ kubectl auth can-i list pods --all-namespaces --as="$PLATFORM_VIEWER"
kubectl auth can-i list secrets --all-namespaces --as="$PLATFORM_VIEWER"
kubectl auth can-i create deployments.apps -n rbac-lab --as="$PLATFORM_VIEWER"
no
no
no
```

**Q&A**

difference is rolebinding and clusterrolebinding

rolebinding used for both Role and Clusterrole 

when rolebinding is used why do we need clusterrolebinding?

```
abhis@Bharathgift MINGW64 ~/Harika/K8s/Practice/LearningK8s (main)
$ kubectl get nodes
NAME                                            STATUS   ROLES    AGE   VERSION
ip-192-168-19-52.ap-south-1.compute.internal    Ready    <none>   94m   v1.34.9-eks-93b80c6
ip-192-168-89-165.ap-south-1.compute.internal   Ready    <none>   94m   v1.34.9-eks-93b80c6

abhis@Bharathgift MINGW64 ~/Harika/K8s/Practice/LearningK8s (main)
$ kubectl get nodes -n app-core
NAME                                            STATUS   ROLES    AGE   VERSION
ip-192-168-19-52.ap-south-1.compute.internal    Ready    <none>   94m   v1.34.9-eks-93b80c6
ip-192-168-89-165.ap-south-1.compute.internal   Ready    <none>   94m   v1.34.9-eks-93b80c6

abhis@Bharathgift MINGW64 ~/Harika/K8s/Practice/LearningK8s (main)
$ kubectl get nodes -n rbac-lab
NAME                                            STATUS   ROLES    AGE   VERSION
ip-192-168-19-52.ap-south-1.compute.internal    Ready    <none>   94m   v1.34.9-eks-93b80c6
ip-192-168-89-165.ap-south-1.compute.internal   Ready    <none>   94m   v1.34.9-eks-93b80c6

abhis@Bharathgift MINGW64 ~/Harika/K8s/Practice/LearningK8s (main)
$ kubectl get pods
No resources found in default namespace.

abhis@Bharathgift MINGW64 ~/Harika/K8s/Practice/LearningK8s (main)
$ kubectl get pods -n app-core
NAME                        READY   STATUS    RESTARTS   AGE
flask-pod                   1/1     Running   0          47m
postgres-5cf755b76f-lh5q4   1/1     Running   0          48m

abhis@Bharathgift MINGW64 ~/Harika/K8s/Practice/LearningK8s (main)
$ kubectl get pods -n rbac-lab
No resources found in rbac-lab namespace.
```

## Config View
```
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\14-rbac-and-identity> kubectl config view
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: DATA+OMITTED
    server: https://754314F72785E71EAD5B8839352F152F.gr7.ap-south-1.eks.amazonaws.com
  name: arn:aws:eks:ap-south-1:129373676098:cluster/prac-cluster
- cluster:
    certificate-authority-data: DATA+OMITTED
    server: https://05B7262EC7B190DCF98BC9FB925145F8.gr7.ap-south-1.eks.amazonaws.com
  name: arn:aws:eks:ap-south-1:129373676098:cluster/prac-cluster-ingress
- cluster:
    certificate-authority-data: DATA+OMITTED
    server: https://F03ECD7199A68C778EE3BF215C01A55A.gr7.ap-south-1.eks.amazonaws.com
  name: arn:aws:eks:ap-south-1:129373676098:cluster/prac-cluster-rbac
- cluster:
    certificate-authority-data: DATA+OMITTED
    server: https://72EB0C1FDFE46A23F7E6DB4D2C830C17.sk1.ap-south-1.eks.amazonaws.com
  name: arn:aws:eks:ap-south-1:129373676098:cluster/pract-cluster-2
- cluster:
    certificate-authority-data: DATA+OMITTED
    server: https://6D9541318A3B3BB7E4E6A37FB6DF1E0B.gr7.ap-south-1.eks.amazonaws.com
  name: arn:aws:eks:ap-south-1:129373676098:cluster/practice-cluster-cls1
- cluster:
    certificate-authority-data: DATA+OMITTED
    server: https://B74A91831DF5D546F1CBB20A91D7D04C.gr7.ap-south-1.eks.amazonaws.com
  name: arn:aws:eks:ap-south-1:129373676098:cluster/sample-cluster-3
- cluster:
    certificate-authority-data: DATA+OMITTED
    server: https://2C7F212B4078FBF392C556628D94D07C.yl4.ap-south-1.eks.amazonaws.com
  name: arn:aws:eks:ap-south-1:129373676098:cluster/sample-cluster-cls2
- cluster:
    certificate-authority-data: DATA+OMITTED
    server: https://B1EACB6E0982215850D448AE75C8876C.gr7.ap-south-1.eks.amazonaws.com
  name: arn:aws:eks:ap-south-1:129373676098:cluster/sample-cluster-cls3
- cluster:
    certificate-authority-data: DATA+OMITTED
    server: https://24D0DDC5B2D697DAC25C3640D3A50D60.gr7.ap-south-1.eks.amazonaws.com
  name: arn:aws:eks:ap-south-1:129373676098:cluster/sample-cluster-cls4
- cluster:
    certificate-authority-data: DATA+OMITTED
    server: https://127.0.0.1:49670
  name: docker-desktop
- cluster:
    certificate-authority-data: DATA+OMITTED
    server: https://F03ECD7199A68C778EE3BF215C01A55A.gr7.ap-south-1.eks.amazonaws.com
  name: prac-cluster-rbac.ap-south-1.eksctl.io
contexts:
- context:
    cluster: prac-cluster-rbac.ap-south-1.eksctl.io
    user: admincliprac@prac-cluster-rbac.ap-south-1.eksctl.io
  name: admincliprac@prac-cluster-rbac.ap-south-1.eksctl.io
- context:
    cluster: arn:aws:eks:ap-south-1:129373676098:cluster/prac-cluster
    user: arn:aws:eks:ap-south-1:129373676098:cluster/prac-cluster
  name: arn:aws:eks:ap-south-1:129373676098:cluster/prac-cluster
- context:
    cluster: arn:aws:eks:ap-south-1:129373676098:cluster/prac-cluster-ingress
    user: arn:aws:eks:ap-south-1:129373676098:cluster/prac-cluster-ingress
  name: arn:aws:eks:ap-south-1:129373676098:cluster/prac-cluster-ingress
- context:
    cluster: arn:aws:eks:ap-south-1:129373676098:cluster/prac-cluster-rbac
    user: arn:aws:eks:ap-south-1:129373676098:cluster/prac-cluster-rbac
  name: arn:aws:eks:ap-south-1:129373676098:cluster/prac-cluster-rbac
- context:
    cluster: arn:aws:eks:ap-south-1:129373676098:cluster/pract-cluster-2
    user: arn:aws:eks:ap-south-1:129373676098:cluster/pract-cluster-2
  name: arn:aws:eks:ap-south-1:129373676098:cluster/pract-cluster-2
- context:
    cluster: arn:aws:eks:ap-south-1:129373676098:cluster/practice-cluster-cls1
    user: arn:aws:eks:ap-south-1:129373676098:cluster/practice-cluster-cls1
  name: arn:aws:eks:ap-south-1:129373676098:cluster/practice-cluster-cls1
- context:
    cluster: arn:aws:eks:ap-south-1:129373676098:cluster/sample-cluster-3
    user: arn:aws:eks:ap-south-1:129373676098:cluster/sample-cluster-3
  name: arn:aws:eks:ap-south-1:129373676098:cluster/sample-cluster-3
- context:
    cluster: arn:aws:eks:ap-south-1:129373676098:cluster/sample-cluster-cls2
    namespace: app-core
    user: arn:aws:eks:ap-south-1:129373676098:cluster/sample-cluster-cls2
  name: arn:aws:eks:ap-south-1:129373676098:cluster/sample-cluster-cls2
- context:
    cluster: arn:aws:eks:ap-south-1:129373676098:cluster/sample-cluster-cls3
    user: arn:aws:eks:ap-south-1:129373676098:cluster/sample-cluster-cls3
  name: arn:aws:eks:ap-south-1:129373676098:cluster/sample-cluster-cls3
- context:
    cluster: arn:aws:eks:ap-south-1:129373676098:cluster/sample-cluster-cls4
    user: arn:aws:eks:ap-south-1:129373676098:cluster/sample-cluster-cls4
  name: arn:aws:eks:ap-south-1:129373676098:cluster/sample-cluster-cls4
- context:
    cluster: docker-desktop
    user: docker-desktop
  name: docker-desktop
current-context: arn:aws:eks:ap-south-1:129373676098:cluster/prac-cluster-rbac
kind: Config
users:
- name: admincliprac@prac-cluster-rbac.ap-south-1.eksctl.io
  user:
    exec:
      apiVersion: client.authentication.k8s.io/v1beta1
      args:
      - eks
      - get-token
      - --output
      - json
      - --cluster-name
      - prac-cluster-rbac
      - --region
      - ap-south-1
      command: aws
      env:
      - name: AWS_STS_REGIONAL_ENDPOINTS
        value: regional
      interactiveMode: IfAvailable
      provideClusterInfo: false
- name: arn:aws:eks:ap-south-1:129373676098:cluster/prac-cluster
  user:
    exec:
      apiVersion: client.authentication.k8s.io/v1beta1
      args:
      - --region
      - ap-south-1
      - eks
      - get-token
      - --cluster-name
      - prac-cluster
      - --output
      - json
      command: aws
      env: null
      interactiveMode: IfAvailable
      provideClusterInfo: false
- name: arn:aws:eks:ap-south-1:129373676098:cluster/prac-cluster-ingress
  user:
    exec:
      apiVersion: client.authentication.k8s.io/v1beta1
      args:
      - --region
      - ap-south-1
      - eks
      - get-token
      - --cluster-name
      - prac-cluster-ingress
      - --output
      - json
      command: aws
      env: null
      interactiveMode: IfAvailable
      provideClusterInfo: false
- name: arn:aws:eks:ap-south-1:129373676098:cluster/prac-cluster-rbac
  user:
    exec:
      apiVersion: client.authentication.k8s.io/v1beta1
      args:
      - --region
      - ap-south-1
      - eks
      - get-token
      - --cluster-name
      - prac-cluster-rbac
      - --output
      - json
      command: aws
      env: null
      interactiveMode: IfAvailable
      provideClusterInfo: false
- name: arn:aws:eks:ap-south-1:129373676098:cluster/pract-cluster-2
  user:
    exec:
      apiVersion: client.authentication.k8s.io/v1beta1
      args:
      - --region
      - ap-south-1
      - eks
      - get-token
      - --cluster-name
      - pract-cluster-2
      - --output
      - json
      command: aws
      env: null
      interactiveMode: IfAvailable
      provideClusterInfo: false
- name: arn:aws:eks:ap-south-1:129373676098:cluster/practice-cluster-cls1
  user:
    exec:
      apiVersion: client.authentication.k8s.io/v1beta1
      args:
      - --region
      - ap-south-1
      - eks
      - get-token
      - --cluster-name
      - practice-cluster-cls1
      - --output
      - json
      command: aws
      env: null
      interactiveMode: IfAvailable
      provideClusterInfo: false
- name: arn:aws:eks:ap-south-1:129373676098:cluster/sample-cluster-3
  user:
    exec:
      apiVersion: client.authentication.k8s.io/v1beta1
      args:
      - --region
      - ap-south-1
      - eks
      - get-token
      - --cluster-name
      - sample-cluster-3
      - --output
      - json
      command: aws
      env: null
      interactiveMode: IfAvailable
      provideClusterInfo: false
- name: arn:aws:eks:ap-south-1:129373676098:cluster/sample-cluster-cls2
  user:
    exec:
      apiVersion: client.authentication.k8s.io/v1beta1
      args:
      - --region
      - ap-south-1
      - eks
      - get-token
      - --cluster-name
      - sample-cluster-cls2
      - --output
      - json
      command: aws
      env: null
      interactiveMode: IfAvailable
      provideClusterInfo: false
- name: arn:aws:eks:ap-south-1:129373676098:cluster/sample-cluster-cls3
  user:
    exec:
      apiVersion: client.authentication.k8s.io/v1beta1
      args:
      - --region
      - ap-south-1
      - eks
      - get-token
      - --cluster-name
      - sample-cluster-cls3
      - --output
      - json
      command: aws
      env: null
      interactiveMode: IfAvailable
      provideClusterInfo: false
- name: arn:aws:eks:ap-south-1:129373676098:cluster/sample-cluster-cls4
  user:
    exec:
      apiVersion: client.authentication.k8s.io/v1beta1
      args:
      - --region
      - ap-south-1
      - eks
      - get-token
      - --cluster-name
      - sample-cluster-cls4
      - --output
      - json
      command: aws
      env: null
      interactiveMode: IfAvailable
      provideClusterInfo: false
- name: docker-desktop
  user:
    client-certificate-data: DATA+OMITTED
    client-key-data: DATA+OMITTED
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\14-rbac-and-identity>

```

## login as tha user 
```
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\14-rbac-and-identity> kubectl create token dev-reader -n rbac-lab
eyJhbGciOiJSUzI1NiIsImtpZCI6ImUxNWQ3ZGJkZDk1MzhiYzJjNWI4ZDU5ZmNhN2EzZDRkYjY1Y2ZkNTciLCJ0eXAiOiJKV1QifQ.eyJhdWQiOlsiaHR0cHM6Ly9rdWJlcm5ldGVzLmRlZmF1bHQuc3ZjIl0sImV4cCI6MTc4MjU1MDk3MywiaWF0IjoxNzgyNTQ3MzczLCJpc3MiOiJodHRwczovL29pZGMuZWtzLmFwLXNvdXRoLTEuYW1hem9uYXdzLmNvbS9pZC9GMDNFQ0Q3MTk5QTY4Qzc3OEVFM0JGMjE1QzAxQTU1QSIsImp0aSI6IjZjNTgyOGIyLWY4NDctNDVhMC1iOGIyLWU1ZmIyZmFmZDRjZCIsImt1YmVybmV0ZXMuaW8iOnsibmFtZXNwYWNlIjoicmJhYy1sYWIiLCJzZXJ2aWNlYWNjb3VudCI6eyJuYW1lIjoiZGV2LXJlYWRlciIsInVpZCI6ImYzY2NiOGE0LTExN2YtNDFiNS1iY2E0LTg1MmU4NTEyMjQ4NiJ9fSwibmJmIjoxNzgyNTQ3MzczLCJzdWIiOiJzeXN0ZW06c2VydmljZWFjY291bnQ6cmJhYy1sYWI6ZGV2LXJlYWRlciJ9.oYhtYfjkL8iRyMn4yBxeB8okZu7wuplW_czzNRRejcc61SJ76yZJpFEZoiznPrBhxxTy7jQiwXMDktjs69DgEQHn-5k9jGdSk3p6o_pUkQeCHYYVjTL-bir8XZTxNKAGEul3drZc_cv0VNeCG-TmDdusNzm1Cgta4Pj_J5wlE1-RGmfBrtgwM5I9xTHhFsBC_iU9bS6qQeTE4shc3hyGibF5zGQyMcPAVVExi9Dct38A6Pnt_7qxaBKK0jbNCUqy9DN98P4WQDrUoJ0Tsw8Qrdgt2f_Mk-dLpsAxAo2vURYTJjjAaClC0qBKbicq8yPKKP7AGLT09DHplCemE8yD2A
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\14-rbac-and-identity> kubectl config view                        
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: DATA+OMITTED
    server: https://754314F72785E71EAD5B8839352F152F.gr7.ap-south-1.eks.amazonaws.com
  name: arn:aws:eks:ap-south-1:129373676098:cluster/prac-cluster
- cluster:
    certificate-authority-data: DATA+OMITTED
    server: https://05B7262EC7B190DCF98BC9FB925145F8.gr7.ap-south-1.eks.amazonaws.com
  name: arn:aws:eks:ap-south-1:129373676098:cluster/prac-cluster-ingress
- cluster:
    certificate-authority-data: DATA+OMITTED
    server: https://F03ECD7199A68C778EE3BF215C01A55A.gr7.ap-south-1.eks.amazonaws.com
  name: arn:aws:eks:ap-south-1:129373676098:cluster/prac-cluster-rbac
- cluster:
    certificate-authority-data: DATA+OMITTED
    server: https://72EB0C1FDFE46A23F7E6DB4D2C830C17.sk1.ap-south-1.eks.amazonaws.com
  name: arn:aws:eks:ap-south-1:129373676098:cluster/pract-cluster-2
- cluster:
    certificate-authority-data: DATA+OMITTED
    server: https://6D9541318A3B3BB7E4E6A37FB6DF1E0B.gr7.ap-south-1.eks.amazonaws.com
  name: arn:aws:eks:ap-south-1:129373676098:cluster/practice-cluster-cls1
- cluster:
    certificate-authority-data: DATA+OMITTED
    server: https://B74A91831DF5D546F1CBB20A91D7D04C.gr7.ap-south-1.eks.amazonaws.com
  name: arn:aws:eks:ap-south-1:129373676098:cluster/sample-cluster-3
- cluster:
    certificate-authority-data: DATA+OMITTED
    server: https://2C7F212B4078FBF392C556628D94D07C.yl4.ap-south-1.eks.amazonaws.com
  name: arn:aws:eks:ap-south-1:129373676098:cluster/sample-cluster-cls2
- cluster:
    certificate-authority-data: DATA+OMITTED
    server: https://B1EACB6E0982215850D448AE75C8876C.gr7.ap-south-1.eks.amazonaws.com
  name: arn:aws:eks:ap-south-1:129373676098:cluster/sample-cluster-cls3
- cluster:
    certificate-authority-data: DATA+OMITTED
    server: https://24D0DDC5B2D697DAC25C3640D3A50D60.gr7.ap-south-1.eks.amazonaws.com
  name: arn:aws:eks:ap-south-1:129373676098:cluster/sample-cluster-cls4
- cluster:
    certificate-authority-data: DATA+OMITTED
    server: https://127.0.0.1:49670
  name: docker-desktop
- cluster:
    certificate-authority-data: DATA+OMITTED
    server: https://F03ECD7199A68C778EE3BF215C01A55A.gr7.ap-south-1.eks.amazonaws.com
  name: prac-cluster-rbac.ap-south-1.eksctl.io
contexts:
- context:
    cluster: prac-cluster-rbac.ap-south-1.eksctl.io
    user: admincliprac@prac-cluster-rbac.ap-south-1.eksctl.io
  name: admincliprac@prac-cluster-rbac.ap-south-1.eksctl.io
- context:
    cluster: arn:aws:eks:ap-south-1:129373676098:cluster/prac-cluster
    user: arn:aws:eks:ap-south-1:129373676098:cluster/prac-cluster
  name: arn:aws:eks:ap-south-1:129373676098:cluster/prac-cluster
- context:
    cluster: arn:aws:eks:ap-south-1:129373676098:cluster/prac-cluster-ingress
    user: arn:aws:eks:ap-south-1:129373676098:cluster/prac-cluster-ingress
  name: arn:aws:eks:ap-south-1:129373676098:cluster/prac-cluster-ingress
- context:
    cluster: arn:aws:eks:ap-south-1:129373676098:cluster/prac-cluster-rbac
    user: arn:aws:eks:ap-south-1:129373676098:cluster/prac-cluster-rbac
  name: arn:aws:eks:ap-south-1:129373676098:cluster/prac-cluster-rbac
- context:
    cluster: arn:aws:eks:ap-south-1:129373676098:cluster/pract-cluster-2
    user: arn:aws:eks:ap-south-1:129373676098:cluster/pract-cluster-2
  name: arn:aws:eks:ap-south-1:129373676098:cluster/pract-cluster-2
- context:
    cluster: arn:aws:eks:ap-south-1:129373676098:cluster/practice-cluster-cls1
    user: arn:aws:eks:ap-south-1:129373676098:cluster/practice-cluster-cls1
  name: arn:aws:eks:ap-south-1:129373676098:cluster/practice-cluster-cls1
- context:
    cluster: arn:aws:eks:ap-south-1:129373676098:cluster/sample-cluster-3
    user: arn:aws:eks:ap-south-1:129373676098:cluster/sample-cluster-3
  name: arn:aws:eks:ap-south-1:129373676098:cluster/sample-cluster-3
- context:
    cluster: arn:aws:eks:ap-south-1:129373676098:cluster/sample-cluster-cls2
    namespace: app-core
    user: arn:aws:eks:ap-south-1:129373676098:cluster/sample-cluster-cls2
  name: arn:aws:eks:ap-south-1:129373676098:cluster/sample-cluster-cls2
- context:
    cluster: arn:aws:eks:ap-south-1:129373676098:cluster/sample-cluster-cls3
    user: arn:aws:eks:ap-south-1:129373676098:cluster/sample-cluster-cls3
  name: arn:aws:eks:ap-south-1:129373676098:cluster/sample-cluster-cls3
- context:
    cluster: arn:aws:eks:ap-south-1:129373676098:cluster/sample-cluster-cls4
    user: arn:aws:eks:ap-south-1:129373676098:cluster/sample-cluster-cls4
  name: arn:aws:eks:ap-south-1:129373676098:cluster/sample-cluster-cls4
- context:
    cluster: docker-desktop
    user: docker-desktop
  name: docker-desktop
current-context: arn:aws:eks:ap-south-1:129373676098:cluster/prac-cluster-rbac
kind: Config
users:
- name: admincliprac@prac-cluster-rbac.ap-south-1.eksctl.io
  user:
    exec:
      apiVersion: client.authentication.k8s.io/v1beta1
      args:
      - eks
      - get-token
      - --output
      - json
      - --cluster-name
      - prac-cluster-rbac
      - --region
      - ap-south-1
      command: aws
      env:
      - name: AWS_STS_REGIONAL_ENDPOINTS
        value: regional
      interactiveMode: IfAvailable
      provideClusterInfo: false
- name: arn:aws:eks:ap-south-1:129373676098:cluster/prac-cluster
  user:
    exec:
      apiVersion: client.authentication.k8s.io/v1beta1
      args:
      - --region
      - ap-south-1
      - eks
      - get-token
      - --cluster-name
      - prac-cluster
      - --output
      - json
      command: aws
      env: null
      interactiveMode: IfAvailable
      provideClusterInfo: false
- name: arn:aws:eks:ap-south-1:129373676098:cluster/prac-cluster-ingress
  user:
    exec:
      apiVersion: client.authentication.k8s.io/v1beta1
      args:
      - --region
      - ap-south-1
      - eks
      - get-token
      - --cluster-name
      - prac-cluster-ingress
      - --output
      - json
      command: aws
      env: null
      interactiveMode: IfAvailable
      provideClusterInfo: false
- name: arn:aws:eks:ap-south-1:129373676098:cluster/prac-cluster-rbac
  user:
    exec:
      apiVersion: client.authentication.k8s.io/v1beta1
      args:
      - --region
      - ap-south-1
      - eks
      - get-token
      - --cluster-name
      - prac-cluster-rbac
      - --output
      - json
      command: aws
      env: null
      interactiveMode: IfAvailable
      provideClusterInfo: false
- name: arn:aws:eks:ap-south-1:129373676098:cluster/pract-cluster-2
  user:
    exec:
      apiVersion: client.authentication.k8s.io/v1beta1
      args:
      - --region
      - ap-south-1
      - eks
      - get-token
      - --cluster-name
      - pract-cluster-2
      - --output
      - json
      command: aws
      env: null
      interactiveMode: IfAvailable
      provideClusterInfo: false
- name: arn:aws:eks:ap-south-1:129373676098:cluster/practice-cluster-cls1
  user:
    exec:
      apiVersion: client.authentication.k8s.io/v1beta1
      args:
      - --region
      - ap-south-1
      - eks
      - get-token
      - --cluster-name
      - practice-cluster-cls1
      - --output
      - json
      command: aws
      env: null
      interactiveMode: IfAvailable
      provideClusterInfo: false
- name: arn:aws:eks:ap-south-1:129373676098:cluster/sample-cluster-3
  user:
    exec:
      apiVersion: client.authentication.k8s.io/v1beta1
      args:
      - --region
      - ap-south-1
      - eks
      - get-token
      - --cluster-name
      - sample-cluster-3
      - --output
      - json
      command: aws
      env: null
      interactiveMode: IfAvailable
      provideClusterInfo: false
- name: arn:aws:eks:ap-south-1:129373676098:cluster/sample-cluster-cls2
  user:
    exec:
      apiVersion: client.authentication.k8s.io/v1beta1
      args:
      - --region
      - ap-south-1
      - eks
      - get-token
      - --cluster-name
      - sample-cluster-cls2
      - --output
      - json
      command: aws
      env: null
      interactiveMode: IfAvailable
      provideClusterInfo: false
- name: arn:aws:eks:ap-south-1:129373676098:cluster/sample-cluster-cls3
  user:
    exec:
      apiVersion: client.authentication.k8s.io/v1beta1
      args:
      - --region
      - ap-south-1
      - eks
      - get-token
      - --cluster-name
      - sample-cluster-cls3
      - --output
      - json
      command: aws
      env: null
      interactiveMode: IfAvailable
      provideClusterInfo: false
- name: arn:aws:eks:ap-south-1:129373676098:cluster/sample-cluster-cls4
  user:
    exec:
      apiVersion: client.authentication.k8s.io/v1beta1
      args:
      - --region
      - ap-south-1
      - eks
      - get-token
      - --cluster-name
      - sample-cluster-cls4
      - --output
      - json
      command: aws
      env: null
      interactiveMode: IfAvailable
      provideClusterInfo: false
- name: docker-desktop
  user:
    client-certificate-data: DATA+OMITTED
    client-key-data: DATA+OMITTED
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\14-rbac-and-identity> kubectl config view --minify -o jsonpath= '{.clusters[0].cluster.server}'
error: unexpected arguments: [{.clusters[0].cluster.server}]
See 'kubectl config view -h' for help and examples
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\14-rbac-and-identity> kubectl config view --minify -o jsonpath='{.clusters[0].cluster.server}' 
https://F03ECD7199A68C778EE3BF215C01A55A.gr7.ap-south-1.eks.amazonaws.com
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\14-rbac-and-identity>
```
## authenticate as another user
```
kubectl create token dev-reader -n rbac-lab
kubectl config view --minify -o jsonpath='{.clusters[0].cluster.server}'
kubectl --server=https://F03ECD7199A68C778EE3BF215C01A55A.gr7.ap-south-1.eks.amazonaws.com --token=eyJhbGciOiJSUzI1NiIsImtpZCI6ImUxNWQ3ZGJkZDk1MzhiYzJjNWI4ZDU5ZmNhN2EzZDRkYjY1Y2ZkNTciLCJ0eXAiOiJKV1QifQ.eyJhdWQiOlsiaHR0cHM6Ly9rdWJlcm5ldGVzLmRlZmF1bHQuc3ZjIl0sImV4cCI6MTc4MjU1MDk3MywiaWF0IjoxNzgyNTQ3MzczLCJpc3MiOiJodHRwczovL29pZGMuZWtzLmFwLXNvdXRoLTEuYW1hem9uYXdzLmNvbS9pZC9GMDNFQ0Q3MTk5QTY4Qzc3OEVFM0JGMjE1QzAxQTU1QSIsImp0aSI6IjZjNTgyOGIyLWY4NDctNDVhMC1iOGIyLWU1ZmIyZmFmZDRjZCIsImt1YmVybmV0ZXMuaW8iOnsibmFtZXNwYWNlIjoicmJhYy1sYWIiLCJzZXJ2aWNlYWNjb3VudCI6eyJuYW1lIjoiZGV2LXJlYWRlciIsInVpZCI6ImYzY2NiOGE0LTExN2YtNDFiNS1iY2E0LTg1MmU4NTEyMjQ4NiJ9fSwibmJmIjoxNzgyNTQ3MzczLCJzdWIiOiJzeXN0ZW06c2VydmljZWFjY291bnQ6cmJhYy1sYWI6ZGV2LXJlYWRlciJ9.oYhtYfjkL8iRyMn4yBxeB8okZu7wuplW_czzNRRejcc61SJ76yZJpFEZoiznPrBhxxTy7jQiwXMDktjs69DgEQHn-5k9jGdSk3p6o_pUkQeCHYYVjTL-bir8XZTxNKAGEul3drZc_cv0VNeCG-TmDdusNzm1Cgta4Pj_J5wlE1-RGmfBrtgwM5I9xTHhFsBC_iU9bS6qQeTE4shc3hyGibF5zGQyMcPAVVExi9Dct38A6Pnt_7qxaBKK0jbNCUqy9DN98P4WQDrUoJ0Tsw8Qrdgt2f_Mk-dLpsAxAo2vURYTJjjAaClC0qBKbicq8yPKKP7AGLT09DHplCemE8yD2A --insecure-skip-tls-verify=true get svc -n rbac-lab 
```
**verify**
```
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\14-rbac-and-identity> kubectl config view --minify -o jsonpath='{.clusters[0].cluster.server}' 
https://F03ECD7199A68C778EE3BF215C01A55A.gr7.ap-south-1.eks.amazonaws.com
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\14-rbac-and-identity> kubectl --server=https://F03ECD7199A68C778EE3BF215C01A55A.gr7.ap-south-1.eks.amazonaws.com --token=eyJhbGciOiJSUzI1NiIsImtpZCI6ImUxNWQ3ZGJkZDk1MzhiYzJjNWI4ZDU5ZmNhN2EzZDRkYjY1Y2ZkNTciLCJ0eXAiOiJKV1QifQ.eyJhdWQiOlsiaHR0cHM6Ly9rdWJlcm5ldGVzLmRlZmF1bHQuc3ZjIl0sImV4cCI6MTc4MjU1MDk3MywiaWF0IjoxNzgyNTQ3MzczLCJpc3MiOiJodHRwczovL29pZGMuZWtzLmFwLXNvdXRoLTEuYW1hem9uYXdzLmNvbS9pZC9GMDNFQ0Q3MTk5QTY4Qzc3OEVFM0JGMjE1QzAxQTU1QSIsImp0aSI6IjZjNTgyOGIyLWY4NDctNDVhMC1iOGIyLWU1ZmIyZmFmZDRjZCIsImt1YmVybmV0ZXMuaW8iOnsibmFtZXNwYWNlIjoicmJhYy1sYWIiLCJzZXJ2aWNlYWNjb3VudCI6eyJuYW1lIjoiZGV2LXJlYWRlciIsInVpZCI6ImYzY2NiOGE0LTExN2YtNDFiNS1iY2E0LTg1MmU4NTEyMjQ4NiJ9fSwibmJmIjoxNzgyNTQ3MzczLCJzdWIiOiJzeXN0ZW06c2VydmljZWFjY291bnQ6cmJhYy1sYWI6ZGV2LXJlYWRlciJ9.oYhtYfjkL8iRyMn4yBxeB8okZu7wuplW_czzNRRejcc61SJ76yZJpFEZoiznPrBhxxTy7jQiwXMDktjs69DgEQHn-5k9jGdSk3p6o_pUkQeCHYYVjTL-bir8XZTxNKAGEul3drZc_cv0VNeCG-TmDdusNzm1Cgta4Pj_J5wlE1-RGmfBrtgwM5I9xTHhFsBC_iU9bS6qQeTE4shc3hyGibF5zGQyMcPAVVExi9Dct38A6Pnt_7qxaBKK0jbNCUqy9DN98P4WQDrUoJ0Tsw8Qrdgt2f_Mk-dLpsAxAo2vURYTJjjAaClC0qBKbicq8yPKKP7AGLT09DHplCemE8yD2A --insecure-skip-tls-verify=true get svc -n rbac-lab 
>> 
Error from server (Forbidden): services is forbidden: User "system:serviceaccount:rbac-lab:dev-reader" cannot list resource "services" in API group "" in the namespace "rbac-lab"
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\14-rbac-and-identity> kubectl --server=https://F03ECD7199A68C778EE3BF215C01A55A.gr7.ap-south-1.eks.amazonaws.com --token=eyJhbGciOiJSUzI1NiIsImtpZCI6ImUxNWQ3ZGJkZDk1MzhiYzJjNWI4ZDU5ZmNhN2EzZDRkYjY1Y2ZkNTciLCJ0eXAiOiJKV1QifQ.eyJhdWQiOlsiaHR0cHM6Ly9rdWJlcm5ldGVzLmRlZmF1bHQuc3ZjIl0sImV4cCI6MTc4MjU1MDk3MywiaWF0IjoxNzgyNTQ3MzczLCJpc3MiOiJodHRwczovL29pZGMuZWtzLmFwLXNvdXRoLTEuYW1hem9uYXdzLmNvbS9pZC9GMDNFQ0Q3MTk5QTY4Qzc3OEVFM0JGMjE1QzAxQTU1QSIsImp0aSI6IjZjNTgyOGIyLWY4NDctNDVhMC1iOGIyLWU1ZmIyZmFmZDRjZCIsImt1YmVybmV0ZXMuaW8iOnsibmFtZXNwYWNlIjoicmJhYy1sYWIiLCJzZXJ2aWNlYWNjb3VudCI6eyJuYW1lIjoiZGV2LXJlYWRlciIsInVpZCI6ImYzY2NiOGE0LTExN2YtNDFiNS1iY2E0LTg1MmU4NTEyMjQ4NiJ9fSwibmJmIjoxNzgyNTQ3MzczLCJzdWIiOiJzeXN0ZW06c2VydmljZWFjY291bnQ6cmJhYy1sYWI6ZGV2LXJlYWRlciJ9.oYhtYfjkL8iRyMn4yBxeB8okZu7wuplW_czzNRRejcc61SJ76yZJpFEZoiznPrBhxxTy7jQiwXMDktjs69DgEQHn-5k9jGdSk3p6o_pUkQeCHYYVjTL-bir8XZTxNKAGEul3drZc_cv0VNeCG-TmDdusNzm1Cgta4Pj_J5wlE1-RGmfBrtgwM5I9xTHhFsBC_iU9bS6qQeTE4shc3hyGibF5zGQyMcPAVVExi9Dct38A6Pnt_7qxaBKK0jbNCUqy9DN98P4WQDrUoJ0Tsw8Qrdgt2f_Mk-dLpsAxAo2vURYTJjjAaClC0qBKbicq8yPKKP7AGLT09DHplCemE8yD2A --insecure-skip-tls-verify=true get pod -n rbac-lab 
>> 
No resources found in rbac-lab namespace.
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\14-rbac-and-identity> kubectl --server=https://F03ECD7199A68C778EE3BF215C01A55A.gr7.ap-south-1.eks.amazonaws.com --token=eyJhbGciOiJSUzI1NiIsImtpZCI6ImUxNWQ3ZGJkZDk1MzhiYzJjNWI4ZDU5ZmNhN2EzZDRkYjY1Y2ZkNTciLCJ0eXAiOiJKV1QifQ.eyJhdWQiOlsiaHR0cHM6Ly9rdWJlcm5ldGVzLmRlZmF1bHQuc3ZjIl0sImV4cCI6MTc4MjU1MDk3MywiaWF0IjoxNzgyNTQ3MzczLCJpc3MiOiJodHRwczovL29pZGMuZWtzLmFwLXNvdXRoLTEuYW1hem9uYXdzLmNvbS9pZC9GMDNFQ0Q3MTk5QTY4Qzc3OEVFM0JGMjE1QzAxQTU1QSIsImp0aSI6IjZjNTgyOGIyLWY4NDctNDVhMC1iOGIyLWU1ZmIyZmFmZDRjZCIsImt1YmVybmV0ZXMuaW8iOnsibmFtZXNwYWNlIjoicmJhYy1sYWIiLCJzZXJ2aWNlYWNjb3VudCI6eyJuYW1lIjoiZGV2LXJlYWRlciIsInVpZCI6ImYzY2NiOGE0LTExN2YtNDFiNS1iY2E0LTg1MmU4NTEyMjQ4NiJ9fSwibmJmIjoxNzgyNTQ3MzczLCJzdWIiOiJzeXN0ZW06c2VydmljZWFjY291bnQ6cmJhYy1sYWI6ZGV2LXJlYWRlciJ9.oYhtYfjkL8iRyMn4yBxeB8okZu7wuplW_czzNRRejcc61SJ76yZJpFEZoiznPrBhxxTy7jQiwXMDktjs69DgEQHn-5k9jGdSk3p6o_pUkQeCHYYVjTL-bir8XZTxNKAGEul3drZc_cv0VNeCG-TmDdusNzm1Cgta4Pj_J5wlE1-RGmfBrtgwM5I9xTHhFsBC_iU9bS6qQeTE4shc3hyGibF5zGQyMcPAVVExi9Dct38A6Pnt_7qxaBKK0jbNCUqy9DN98P4WQDrUoJ0Tsw8Qrdgt2f_Mk-dLpsAxAo2vURYTJjjAaClC0qBKbicq8yPKKP7AGLT09DHplCemE8yD2A --insecure-skip-tls-verify=true get pod -n app-core 
>> 
Error from server (Forbidden): pods is forbidden: User "system:serviceaccount:rbac-lab:dev-reader" cannot list resource "pods" in API group "" in the namespace "app-core"
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\14-rbac-and-identity> kubectl --server=https://F03ECD7199A68C778EE3BF215C01A55A.gr7.ap-south-1.eks.amazonaws.com --token=eyJhbGciOiJSUzI1NiIsImtpZCI6ImUxNWQ3ZGJkZDk1MzhiYzJjNWI4ZDU5ZmNhN2EzZDRkYjY1Y2ZkNTciLCJ0eXAiOiJKV1QifQ.eyJhdWQiOlsiaHR0cHM6Ly9rdWJlcm5ldGVzLmRlZmF1bHQuc3ZjIl0sImV4cCI6MTc4MjU1MDk3MywiaWF0IjoxNzgyNTQ3MzczLCJpc3MiOiJodHRwczovL29pZGMuZWtzLmFwLXNvdXRoLTEuYW1hem9uYXdzLmNvbS9pZC9GMDNFQ0Q3MTk5QTY4Qzc3OEVFM0JGMjE1QzAxQTU1QSIsImp0aSI6IjZjNTgyOGIyLWY4NDctNDVhMC1iOGIyLWU1ZmIyZmFmZDRjZCIsImt1YmVybmV0ZXMuaW8iOnsibmFtZXNwYWNlIjoicmJhYy1sYWIiLCJzZXJ2aWNlYWNjb3VudCI6eyJuYW1lIjoiZGV2LXJlYWRlciIsInVpZCI6ImYzY2NiOGE0LTExN2YtNDFiNS1iY2E0LTg1MmU4NTEyMjQ4NiJ9fSwibmJmIjoxNzgyNTQ3MzczLCJzdWIiOiJzeXN0ZW06c2VydmljZWFjY291bnQ6cmJhYy1sYWI6ZGV2LXJlYWRlciJ9.oYhtYfjkL8iRyMn4yBxeB8okZu7wuplW_czzNRRejcc61SJ76yZJpFEZoiznPrBhxxTy7jQiwXMDktjs69DgEQHn-5k9jGdSk3p6o_pUkQeCHYYVjTL-bir8XZTxNKAGEul3drZc_cv0VNeCG-TmDdusNzm1Cgta4Pj_J5wlE1-RGmfBrtgwM5I9xTHhFsBC_iU9bS6qQeTE4shc3hyGibF5zGQyMcPAVVExi9Dct38A6Pnt_7qxaBKK0jbNCUqy9DN98P4WQDrUoJ0Tsw8Qrdgt2f_Mk-dLpsAxAo2vURYTJjjAaClC0qBKbicq8yPKKP7AGLT09DHplCemE8yD2A --insecure-skip-tls-verify=true get list -n rbac-lab 
>> 
Error from server (Forbidden): lists is forbidden: User "system:serviceaccount:rbac-lab:dev-reader" cannot list resource "lists" in API group "" at the cluster scope
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\14-rbac-and-identity>
```
