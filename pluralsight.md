
## Task 1:

Count the number of Nodes that are ready to run normal workloads

Determine how many nodes in the cluster are ready to run normal workloads (i.e., workloads that do not have any special tolerations).

Output this number to the file /k8s/0001/count.txt.


```
kubectl get nodes
kubectl describe nodes | grep Taints
kubectl get nodes -o custom-columns=NAME:.metadata.name,TAINTS:.spec.taints
kubectl get nodes -o json | jq '[.items[] | select(.status.conditions[] | select(.type=="Ready" and .status=="True")) | select((.spec.taints // []) | length == 0)] | length' > /k8s/0001/count.txt
kubectl get nodes -o json | jq '[.items[]
| select(any(.status.conditions[]; .type=="Ready" and .status=="True"))
| select((.spec.taints // []) | length == 0)
] | length' > /k8s/0001/count.txt
```


## Task 2:

Retrieve Error Messages from a container Log

In the backend namesapace, check the log for the proc container in the data-handler Pod

Save the lines which contain the text ERROR to the file /k8s/0002/errors.txt

```
kubectl logs -n backend data-handler -c proc | grep ERROR > /k8s/0002/errors.txt

cat /k8s/0002/errors.txt
```

# Task 3:

Find the Pod with the a Label in app=auth in the web Namespace that is Utilizing the Most CPU

Before doing this step,please wait a minute or two to give our backend script time to generate CPU load 

Determine which Pod in the web namespace with the label app=auth is using the most CPU. Save the name of this Pod to the file /k8s/0003/cpu-pod.txt

```
kubectl top pod -n web -l app=auth --no-headers | sort -k2 -h | tail -1 | awk '{print $1}' > /k8s/0003/cpu-pod.txt
```


