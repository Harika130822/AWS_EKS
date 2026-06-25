

```
eksctl create cluster --name pract-cluster-2 --region ap-south-1 --nodegroup-name standard-workers --node-type t3.medium --nodes 2 --nodes-min 2 --nodes-max 3
aws eks --region ap-south-1 update-kubeconfig --name pract-cluster-2
eksctl delete cluster --name pract-cluster-2 --region ap-south-1
```

## HPA
```
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\04-hpa-vpa> kubectl apply -f subsessions/01-prerequisites-and-namespace/
namespace/app-autoscaling created
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\04-hpa-vpa> kubectl apply -f subsessions/02-hpa-cpu-autoscaling/
deployment.apps/cpu-demo created
service/cpu-demo created
horizontalpodautoscaler.autoscaling/cpu-demo created
deployment.apps/load-generator created
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\04-hpa-vpa> kubectl get all -n app-autoscaling
NAME                                  READY   STATUS    RESTARTS   AGE
pod/cpu-demo-5fdbb8cd4f-qkvfk         1/1     Running   0          17s
pod/load-generator-6749c875cd-swh84   1/1     Running   0          16s

NAME               TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)   AGE
service/cpu-demo   ClusterIP   10.100.192.129   <none>        80/TCP    18s

NAME                             READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/cpu-demo         1/1     1            1           18s
deployment.apps/load-generator   1/1     1            1           17s

NAME                                        DESIRED   CURRENT   READY   AGE
replicaset.apps/cpu-demo-5fdbb8cd4f         1         1         1       18s
replicaset.apps/load-generator-6749c875cd   1         1         1       17s

NAME                                           REFERENCE             TARGETS              MINPODS   MAXPODS   REPLICAS   AGE
horizontalpodautoscaler.autoscaling/cpu-demo   Deployment/cpu-demo   cpu: <unknown>/50%   1         6         1          17s
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\04-hpa-vpa> kubectl get hpa -n app-autoscaling -w
NAME       REFERENCE             TARGETS              MINPODS   MAXPODS   REPLICAS   AGE
cpu-demo   Deployment/cpu-demo   cpu: <unknown>/50%   1         6         1          43s
cpu-demo   Deployment/cpu-demo   cpu: <unknown>/50%   1         6         1          45s
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\04-hpa-vpa> kubectl get hpa -n app-autoscaling   
NAME       REFERENCE             TARGETS              MINPODS   MAXPODS   REPLICAS   AGE
cpu-demo   Deployment/cpu-demo   cpu: <unknown>/50%   1         6         1          53s
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\04-hpa-vpa> kubectl delete -f subsessions/02-hpa-cpu-autoscaling/03-load-generator.yml --ignore-not-found
deployment.apps "load-generator" deleted from app-autoscaling namespace
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\04-hpa-vpa>
```

## VPA
> <img width="795" height="153" alt="image" src="https://github.com/user-attachments/assets/657bf0d9-9059-4319-b3d0-ab5bc9218c5d" />
```
git clone https://github.com/kubernetes/autoscaler.git
cd autoscaler/vertical-pod-autoscaler
./hack/vpa-up.sh
```
> <img width="1112" height="517" alt="image" src="https://github.com/user-attachments/assets/d78f6618-fab9-4d7a-a2f7-853cba661bcc" />

```
abhis@Bharathgift MINGW64 ~/Harika/K8s/Practice/LearningK8s/sessions/04-hpa-vpa/subsessions (main)
$ cd autoscaler/vertical-pod-autoscaler

abhis@Bharathgift MINGW64 ~/Harika/K8s/Practice/LearningK8s/sessions/04-hpa-vpa/subsessions/autoscaler/vertical-pod-autoscaler (master)
$ ./hack/vpa-up.sh
HEAD is now at 6a616ea0c Update VPA defaults to 1.7.0
customresourcedefinition.apiextensions.k8s.io/verticalpodautoscalercheckpoints.autoscaling.k8s.io created
customresourcedefinition.apiextensions.k8s.io/verticalpodautoscalers.autoscaling.k8s.io created
clusterrole.rbac.authorization.k8s.io/system:metrics-reader created
clusterrole.rbac.authorization.k8s.io/system:vpa-actor created
clusterrole.rbac.authorization.k8s.io/system:vpa-status-actor created
clusterrole.rbac.authorization.k8s.io/system:vpa-checkpoint-actor created
clusterrole.rbac.authorization.k8s.io/system:evictioner created
clusterrole.rbac.authorization.k8s.io/system:vpa-updater-in-place created
clusterrolebinding.rbac.authorization.k8s.io/system:vpa-updater-in-place-binding created
clusterrolebinding.rbac.authorization.k8s.io/system:metrics-reader created
clusterrolebinding.rbac.authorization.k8s.io/system:vpa-actor created
clusterrolebinding.rbac.authorization.k8s.io/system:vpa-status-actor created
clusterrolebinding.rbac.authorization.k8s.io/system:vpa-checkpoint-actor created
clusterrole.rbac.authorization.k8s.io/system:vpa-target-reader created
clusterrolebinding.rbac.authorization.k8s.io/system:vpa-target-reader-binding created
clusterrolebinding.rbac.authorization.k8s.io/system:vpa-evictioner-binding created
serviceaccount/vpa-admission-controller created
serviceaccount/vpa-recommender created
serviceaccount/vpa-updater created
clusterrole.rbac.authorization.k8s.io/system:vpa-admission-controller created
clusterrolebinding.rbac.authorization.k8s.io/system:vpa-admission-controller created
clusterrole.rbac.authorization.k8s.io/system:vpa-status-reader created
clusterrolebinding.rbac.authorization.k8s.io/system:vpa-status-reader-binding created
role.rbac.authorization.k8s.io/system:leader-locking-vpa-updater created
rolebinding.rbac.authorization.k8s.io/system:leader-locking-vpa-updater created
role.rbac.authorization.k8s.io/system:leader-locking-vpa-recommender created
rolebinding.rbac.authorization.k8s.io/system:leader-locking-vpa-recommender created
deployment.apps/vpa-updater created
deployment.apps/vpa-recommender created
Generating certs for the VPA Admission Controller in /tmp/vpa-certs.
req: subject name is expected to be in the format /type0=value0/type1=value1/type2=... where characters may be escaped by \. This name is not in that format: 'C:/Program Files/Git/CN=vpa_webhook_ca'
ERROR: Failed to create CA certificate for self-signing. If the error is "unknown option -addext", update your openssl version or deploy VPA from the vpa-release-0.8 branch.
service/vpa-webhook created
deployment.apps/vpa-admission-controller created
service/vpa-webhook unchanged

abhis@Bharathgift MINGW64 ~/Harika/K8s/Practice/LearningK8s/sessions/04-hpa-vpa/subsessions/autoscaler/vertical-pod-autoscaler ((vertical-pod-autoscaler-1.7.0))
$ kubectl get crd | grep verticalpodautoscalers
verticalpodautoscalers.autoscaling.k8s.io             2026-06-25T13:55:40Z

abhis@Bharathgift MINGW64 ~/Harika/K8s/Practice/LearningK8s/sessions/04-hpa-vpa/subsessions/autoscaler/vertical-pod-autoscaler ((vertical-pod-autoscaler-1.7.0))
$ kubectl get pods -n kube-system | grep vpa
vpa-admission-controller-75db774f55-mwqgc   0/1     ContainerCreating   0          58s
vpa-recommender-75db9848b6-k7sph            1/1     Running             0          62s
vpa-updater-65cc7bcb9c-wkphk                1/1     Running             0          64s

abhis@Bharathgift MINGW64 ~/Harika/K8s/Practice/LearningK8s/sessions/04-hpa-vpa/subsessions/autoscaler/vertical-pod-autoscaler ((vertical-pod-autoscaler-1.7.0))
$ kubectl api-resources | grep -i vertical
verticalpodautoscalercheckpoints    vpacheckpoint   autoscaling.k8s.io/v1             true         VerticalPodAutoscalerCheckpoint
verticalpodautoscalers              vpa             autoscaling.k8s.io/v1             true         VerticalPodAutoscaler

abhis@Bharathgift MINGW64 ~/Harika/K8s/Practice/LearningK8s/sessions/04-hpa-vpa/subsessions/autoscaler/vertical-pod-autoscaler ((vertical-pod-autoscaler-1.7.0))
$ kubectl top pods --all-namespaces
NAMESPACE         NAME                               CPU(cores)   MEMORY(bytes)   
app-autoscaling   cpu-demo-5fdbb8cd4f-qkvfk          1m           9Mi             
kube-system       aws-node-2zc8c                     4m           54Mi            
kube-system       aws-node-4krnv                     5m           54Mi            
kube-system       coredns-8477c579f6-6tnxj           2m           11Mi            
kube-system       coredns-8477c579f6-p2c9b           2m           12Mi            
kube-system       kube-proxy-8mjqw                   1m           11Mi            
kube-system       kube-proxy-b4ks2                   1m           11Mi            
kube-system       metrics-server-847bb9c5fd-9cqrz    3m           17Mi            
kube-system       metrics-server-847bb9c5fd-s6n6h    3m           17Mi            
kube-system       vpa-recommender-75db9848b6-k7sph   1m           8Mi             
kube-system       vpa-updater-65cc7bcb9c-wkphk       1m           8Mi             

abhis@Bharathgift MINGW64 ~/Harika/K8s/Practice/LearningK8s/sessions/04-hpa-vpa/subsessions/autoscaler/vertical-pod-autoscaler ((vertical-pod-autoscaler-1.7.0))
$ kubectl get apiservice v1beta1.metrics.k8s.io
NAME                     SERVICE                      AVAILABLE   AGE
v1beta1.metrics.k8s.io   kube-system/metrics-server   True        15m

abhis@Bharathgift MINGW64 ~/Harika/K8s/Practice/LearningK8s/sessions/04-hpa-vpa/subsessions/autoscaler/vertical-pod-autoscaler ((vertical-pod-autoscaler-1.7.0))
$ 
```


```
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s> cd .\sessions\04-hpa-vpa\
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\04-hpa-vpa> kubectl apply -f subsessions/04-vpa-recommendations/01-vpa-demo-deployment.yml
deployment.apps/vpa-demo created
service/vpa-demo created
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\04-hpa-vpa> kubectl apply -f subsessions/04-vpa-recommendations/02-vpa-recommendation-only.yml
verticalpodautoscaler.autoscaling.k8s.io/vpa-demo created
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\04-hpa-vpa> kubectl apply -f subsessions/04-vpa-recommendations/04-vpa-load-generator.yml
deployment.apps/vpa-load-generator created
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\04-hpa-vpa> kubectl describe vpa vpa-demo -n app-autoscaling
Name:         vpa-demo
Namespace:    app-autoscaling
Labels:       app=vpa-demo
              autoscaling-demo=vpa
Annotations:  <none>
API Version:  autoscaling.k8s.io/v1
Kind:         VerticalPodAutoscaler
Metadata:
  Creation Timestamp:  2026-06-25T14:00:20Z
  Generation:          1
  Resource Version:    4905
  UID:                 2335350f-ad25-4154-b3b2-8d1377e88d3b
Spec:
  Resource Policy:
    Container Policies:
      Container Name:  vpa-demo
      Controlled Resources:
        cpu
        memory
      Max Allowed:
        Cpu:     1
        Memory:  512Mi
      Min Allowed:
        Cpu:     25m
        Memory:  64Mi
  Target Ref:
    API Version:  apps/v1
    Kind:         Deployment
    Name:         vpa-demo
  Update Policy:
    Update Mode:  Off
Events:           <none>
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\04-hpa-vpa> kubectl apply -f subsessions/04-vpa-recommendations/03-vpa-auto-recreate.yml
verticalpodautoscaler.autoscaling.k8s.io/vpa-demo configured
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\04-hpa-vpa> kubectl delete pod -n app-autoscaling -l app=vpa-demo
pod "vpa-demo-554d8869f-7rr28" deleted from app-autoscaling namespace
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\04-hpa-vpa> kubectl get pods -n app-autoscaling -w
NAME                                  READY   STATUS    RESTARTS   AGE
cpu-demo-5fdbb8cd4f-qkvfk             1/1     Running   0          17m
vpa-demo-554d8869f-jtm5g              1/1     Running   0          8s
vpa-load-generator-869d44994f-vgczc   1/1     Running   0          35s
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\04-hpa-vpa> kubectl get all -n app-autoscaling
NAME                                      READY   STATUS    RESTARTS   AGE
pod/cpu-demo-5fdbb8cd4f-qkvfk             1/1     Running   0          17m
pod/vpa-demo-554d8869f-jtm5g              1/1     Running   0          31s
pod/vpa-load-generator-869d44994f-vgczc   1/1     Running   0          58s

NAME               TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)   AGE
service/cpu-demo   ClusterIP   10.100.192.129   <none>        80/TCP    17m
service/vpa-demo   ClusterIP   10.100.208.117   <none>        80/TCP    76s

NAME                                 READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/cpu-demo             1/1     1            1           17m
deployment.apps/vpa-demo             1/1     1            1           76s
deployment.apps/vpa-load-generator   1/1     1            1           59s

NAME                                            DESIRED   CURRENT   READY   AGE
replicaset.apps/cpu-demo-5fdbb8cd4f             1         1         1       17m
replicaset.apps/vpa-demo-554d8869f              1         1         1       76s
replicaset.apps/vpa-load-generator-869d44994f   1         1         1       59s

NAME                                           REFERENCE             TARGETS       MINPODS   MAXPODS   REPLICAS   AGE
horizontalpodautoscaler.autoscaling/cpu-demo   Deployment/cpu-demo   cpu: 1%/50%   1         6         1          17m
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s\sessions\04-hpa-vpa>
```


### verification
```
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s> kubectl top pods -n app-autoscaling
NAME                                  CPU(cores)   MEMORY(bytes)   
cpu-demo-5fdbb8cd4f-qkvfk             1m           9Mi             
vpa-demo-554d8869f-jtm5g              500m         11Mi            
vpa-load-generator-869d44994f-vgczc   14m          0Mi             
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s> kubectl describe hpa cpu-demo -n app-autoscaling
Name:                                                  cpu-demo
Namespace:                                             app-autoscaling
Labels:                                                app=cpu-demo
                                                       autoscaling-demo=hpa
Annotations:                                           <none>
CreationTimestamp:                                     Thu, 25 Jun 2026 19:13:30 +0530
Reference:                                             Deployment/cpu-demo
Metrics:                                               ( current / target )
  resource cpu on pods  (as a percentage of request):  1% (1m) / 50%
Min replicas:                                          1
Max replicas:                                          6
Behavior:
  Scale Up:
    Stabilization Window: 0 seconds
    Select Policy: Max
    Policies:
      - Type: Percent  Value: 100  Period: 30 seconds
      - Type: Pods     Value: 2    Period: 30 seconds
  Scale Down:
    Stabilization Window: 180 seconds
    Select Policy: Max
    Policies:
      - Type: Percent  Value: 50  Period: 60 seconds
Deployment pods:       1 current / 1 desired
Conditions:
  Type            Status  Reason              Message
  ----            ------  ------              -------
  AbleToScale     True    ReadyForNewScale    recommended size matches current size
  ScalingActive   True    ValidMetricFound    the HPA was able to successfully calculate a replica count from cpu resource utilization (percentage of request)
  ScalingLimited  False   DesiredWithinRange  the desired count is within the acceptable range
Events:
  Type     Reason                        Age                From                       Message
  ----     ------                        ----               ----                       -------
  Warning  FailedGetResourceMetric       19m (x2 over 20m)  horizontal-pod-autoscaler  failed to get cpu utilization: unable to get metrics for resource cpu: no metrics returned from resource metrics API
  Warning  FailedComputeMetricsReplicas  19m (x2 over 20m)  horizontal-pod-autoscaler  invalid metrics (1 invalid out of 1), first error is: failed to get cpu resource metric value: failed to get cpu utilization: unable to get metrics for resource cpu: no metrics returned from resource metrics API
  Warning  FailedGetResourceMetric       19m                horizontal-pod-autoscaler  failed to get cpu utilization: did not receive metrics for targeted pods (pods might be unready)
  Warning  FailedComputeMetricsReplicas  19m                horizontal-pod-autoscaler  invalid metrics (1 invalid out of 1), first error is: failed to get cpu resource metric value: failed to get cpu utilization: did not receive metrics for targeted pods (pods might be unready)
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s> kubectl describe pod -n app-autoscaling -l app=cpu-demo
Name:             cpu-demo-5fdbb8cd4f-qkvfk
Namespace:        app-autoscaling
Priority:         0
Service Account:  default
Node:             ip-192-168-61-246.ap-south-1.compute.internal/192.168.61.246
Start Time:       Thu, 25 Jun 2026 19:13:29 +0530
Labels:           app=cpu-demo
                  autoscaling-demo=hpa
                  pod-template-hash=5fdbb8cd4f
Annotations:      <none>
Status:           Running
IP:               192.168.34.159
IPs:
  IP:           192.168.34.159
Controlled By:  ReplicaSet/cpu-demo-5fdbb8cd4f
Containers:
  cpu-demo:
    Container ID:   containerd://cc0111143e437fea8d4ca53add2fd4e88913e405c3ddd0fad89a3e7ebff0aecd
    Image:          registry.k8s.io/hpa-example
    Image ID:       registry.k8s.io/hpa-example@sha256:581697a37f0e136db86d6b30392f0db40ce99c8248a7044c770012f4e8491544
    Port:           80/TCP (http)
    Host Port:      0/TCP (http)
    State:          Running
      Started:      Thu, 25 Jun 2026 19:13:39 +0530
    Ready:          True
    Restart Count:  0
    Limits:
      cpu:     500m
      memory:  128Mi
    Requests:
      cpu:        100m
      memory:     64Mi
    Environment:  <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-rfkqx (ro)
Conditions:
  Type                        Status
  PodReadyToStartContainers   True 
  Initialized                 True 
  Ready                       True 
  ContainersReady             True 
  PodScheduled                True 
Volumes:
  kube-api-access-rfkqx:
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
  Normal  Scheduled  20m   default-scheduler  Successfully assigned app-autoscaling/cpu-demo-5fdbb8cd4f-qkvfk to ip-192-168-61-246.ap-south-1.compute.internal
  Normal  Pulling    20m   kubelet            Pulling image "registry.k8s.io/hpa-example"
  Normal  Pulled     20m   kubelet            Successfully pulled image "registry.k8s.io/hpa-example" in 9.595s (9.595s including waiting). Image size: 164030864 bytes.
  Normal  Created    20m   kubelet            Created container: cpu-demo
  Normal  Started    20m   kubelet            Started container cpu-demo
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s> kubectl get vpa -n app-autoscaling
NAME       MODE       CPU    MEM     PROVIDED   AGE
vpa-demo   Recreate   587m   250Mi   True       3m55s
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s> kubectl describe vpa vpa-demo -n app-autoscaling
Name:         vpa-demo
Namespace:    app-autoscaling
Labels:       app=vpa-demo
              autoscaling-demo=vpa
Annotations:  <none>
API Version:  autoscaling.k8s.io/v1
Kind:         VerticalPodAutoscaler
Metadata:
  Creation Timestamp:  2026-06-25T14:00:20Z
  Generation:          2
  Resource Version:    5543
  UID:                 2335350f-ad25-4154-b3b2-8d1377e88d3b
Spec:
  Resource Policy:
    Container Policies:
      Container Name:  vpa-demo
      Controlled Resources:
        cpu
        memory
      Max Allowed:
        Cpu:     1
        Memory:  512Mi
      Min Allowed:
        Cpu:     25m
        Memory:  64Mi
  Target Ref:
    API Version:  apps/v1
    Kind:         Deployment
    Name:         vpa-demo
  Update Policy:
    Update Mode:  Recreate
Status:
  Conditions:
    Last Transition Time:  2026-06-25T14:00:52Z
    Status:                True
    Type:                  RecommendationProvided
  Observed Generation:     2
  Recommendation:
    Container Recommendations:
      Container Name:  vpa-demo
      Lower Bound:
        Cpu:     269m
        Memory:  250Mi
      Target:
        Cpu:     587m
        Memory:  250Mi
      Uncapped Target:
        Cpu:     587m
        Memory:  250Mi
      Upper Bound:
        Cpu:     1
        Memory:  512Mi
Events:          <none>
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s> kubectl get pods -n kube-system | grep vpa
grep : The term 'grep' is not recognized as the name of a cmdlet, function, script file, or operable program. Check the spelling of the name, or if a path was included, verify that 
the path is correct and try again.
At line:1 char:35
+ kubectl get pods -n kube-system | grep vpa
+                                   ~~~~
    + CategoryInfo          : ObjectNotFound: (grep:String) [], CommandNotFoundException
    + FullyQualifiedErrorId : CommandNotFoundException
 
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s> kubectl top pods -n app-autoscaling
NAME                                  CPU(cores)   MEMORY(bytes)   
cpu-demo-5fdbb8cd4f-qkvfk             1m           9Mi             
vpa-demo-554d8869f-jtm5g              501m         11Mi            
vpa-load-generator-869d44994f-vgczc   14m          0Mi             
PS C:\Users\abhis\Harika\K8s\Practice\LearningK8s>
```


### cleanup VPA
```
abhis@Bharathgift MINGW64 ~/Harika/K8s/Practice/LearningK8s/sessions/04-hpa-vpa/subsessions/autoscaler/vertical-pod-autoscaler ((vertical-pod-autoscaler-1.7.0))
$ kubectl describe hpa cpu-demo -n app-autoscaling
Name:                                                  cpu-demo
Namespace:                                             app-autoscaling
Labels:                                                app=cpu-demo
                                                       autoscaling-demo=hpa
Annotations:                                           <none>
CreationTimestamp:                                     Thu, 25 Jun 2026 19:13:30 +0530
Reference:                                             Deployment/cpu-demo
Metrics:                                               ( current / target )
  resource cpu on pods  (as a percentage of request):  1% (1m) / 50%
Min replicas:                                          1
Max replicas:                                          6
Behavior:
  Scale Up:
    Stabilization Window: 0 seconds
    Select Policy: Max
    Policies:
      - Type: Percent  Value: 100  Period: 30 seconds
      - Type: Pods     Value: 2    Period: 30 seconds
  Scale Down:
    Stabilization Window: 180 seconds
    Select Policy: Max
    Policies:
      - Type: Percent  Value: 50  Period: 60 seconds
Deployment pods:       1 current / 1 desired
Conditions:
  Type            Status  Reason              Message
  ----            ------  ------              -------
  AbleToScale     True    ReadyForNewScale    recommended size matches current size
  ScalingActive   True    ValidMetricFound    the HPA was able to successfully calculate a replica count from cpu resource utilization (percentage of request)
  ScalingLimited  False   DesiredWithinRange  the desired count is within the acceptable range
Events:
  Type     Reason                        Age                From                       Message
  ----     ------                        ----               ----                       -------
  Warning  FailedGetResourceMetric       23m (x2 over 24m)  horizontal-pod-autoscaler  failed to get cpu utilization: unable to get metrics for resource cpu: no metrics returned from resource metrics API
  Warning  FailedComputeMetricsReplicas  23m (x2 over 24m)  horizontal-pod-autoscaler  invalid metrics (1 invalid out of 1), first error is: failed to get cpu resource metric value: failed to get cpu utilization: unable to get metrics for resource cpu: no metrics returned from resource metrics API
  Warning  FailedGetResourceMetric       23m                horizontal-pod-autoscaler  failed to get cpu utilization: did not receive metrics for targeted pods (pods might be unready)
  Warning  FailedComputeMetricsReplicas  23m                horizontal-pod-autoscaler  invalid metrics (1 invalid out of 1), first error is: failed to get cpu resource metric value: failed to get cpu utilization: did not receive metrics for targeted pods (pods might be unready)

abhis@Bharathgift MINGW64 ~/Harika/K8s/Practice/LearningK8s/sessions/04-hpa-vpa/subsessions/autoscaler/vertical-pod-autoscaler ((vertical-pod-autoscaler-1.7.0))
$ kubectl describe pod -n app-autoscaling -l app=cpu-demo
Name:             cpu-demo-5fdbb8cd4f-qkvfk
Namespace:        app-autoscaling
Priority:         0
Service Account:  default
Node:             ip-192-168-61-246.ap-south-1.compute.internal/192.168.61.246
Start Time:       Thu, 25 Jun 2026 19:13:29 +0530
Labels:           app=cpu-demo
                  autoscaling-demo=hpa
                  pod-template-hash=5fdbb8cd4f
Annotations:      <none>
Status:           Running
IP:               192.168.34.159
IPs:
  IP:           192.168.34.159
Controlled By:  ReplicaSet/cpu-demo-5fdbb8cd4f
Containers:
  cpu-demo:
    Container ID:   containerd://cc0111143e437fea8d4ca53add2fd4e88913e405c3ddd0fad89a3e7ebff0aecd
    Image:          registry.k8s.io/hpa-example
    Image ID:       registry.k8s.io/hpa-example@sha256:581697a37f0e136db86d6b30392f0db40ce99c8248a7044c770012f4e8491544
    Port:           80/TCP (http)
    Host Port:      0/TCP (http)
    State:          Running
      Started:      Thu, 25 Jun 2026 19:13:39 +0530
    Ready:          True
    Restart Count:  0
    Limits:
      cpu:     500m
      memory:  128Mi
    Requests:
      cpu:        100m
      memory:     64Mi
    Environment:  <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-rfkqx (ro)
Conditions:
  Type                        Status
  PodReadyToStartContainers   True 
  Initialized                 True 
  Ready                       True 
  ContainersReady             True 
  PodScheduled                True 
Volumes:
  kube-api-access-rfkqx:
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
  Normal  Scheduled  24m   default-scheduler  Successfully assigned app-autoscaling/cpu-demo-5fdbb8cd4f-qkvfk to ip-192-168-61-246.ap-south-1.compute.internal
  Normal  Pulling    24m   kubelet            Pulling image "registry.k8s.io/hpa-example"
  Normal  Pulled     24m   kubelet            Successfully pulled image "registry.k8s.io/hpa-example" in 9.595s (9.595s including waiting). Image size: 164030864 bytes.
  Normal  Created    24m   kubelet            Created container: cpu-demo
  Normal  Started    24m   kubelet            Started container cpu-demo

abhis@Bharathgift MINGW64 ~/Harika/K8s/Practice/LearningK8s/sessions/04-hpa-vpa/subsessions/autoscaler/vertical-pod-autoscaler ((vertical-pod-autoscaler-1.7.0))
$ AA
```
