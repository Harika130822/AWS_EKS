
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
