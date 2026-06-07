# Kubernetes 1&2 
Learning Guide and Hands-on Tasks 
This guide introduces the fundamentals of Kubernetes and provides 5 practical tasks to help 
beginners gain hands-on experience with container orchestration. 

## Part 1: Understanding Kubernetes Fundamentals 
What is Kubernetes? 
Kubernetes (K8s) is an open-source container orchestration platform that automates the 
deployment, scaling, and management of containerized applications. It was originally developed 
by Google and is now maintained by the Cloud Native Computing Foundation (CNCF). 
Why Use Kubernetes? 
● High Availability: Ensures your applications are always running 
● Scalability: Easily scale applications up or down based on demand 
● Disaster Recovery: Quickly recover from failures with self-healing capabilities 
● Load Balancing: Distribute network traffic to maintain stability 
Core Kubernetes Architecture 
● Automated Rollouts/Rollbacks: Deploy and update applications reliably 
Kubernetes follows a master-worker architecture: 
1. Control Plane (Master) - The brain of the cluster that manages: 
○ API Server: Communication hub for all cluster components 
○ Scheduler: Assigns pods to nodes 
○ Controller Manager: Oversees controllers that regulate the cluster state 
○ etcd: Distributed key-value store that maintains cluster configuration 
2. Nodes (Workers) - Machines that run your applications: 
○ Kubelet: Agent that ensures containers are running in a pod 
○ Container Runtime: Software responsible for running containers (e.g., Docker) 
○ Kube-proxy: Manages network rules and connection forwarding 

Core Kubernetes Components 
Pods 
● Smallest deployable unit in Kubernetes 
● Contains one or more containers that share storage and network resources 
● Has a unique IP address within the cluster 
● Containers in a pod are always scheduled on the same node 
```
# Example of a simple Pod YAML 
apiVersion: v1 
kind: Pod 
metadata: 
  name: nginx-pod 
spec: 
  containers: 
  - name: nginx 
    image: nginx:latest 
    ports: 
    - containerPort: 80 
```
Deployments 
● Manages ReplicaSets and provides declarative updates to applications 
● Ensures a specified number of pod replicas are running 
● Supports rolling updates and rollbacks 
```
# Example of a Deployment YAML 
apiVersion: apps/v1 
kind: Deployment 
metadata: 
  name: nginx-deployment 
spec: 
  replicas: 3 
  selector: 
    matchLabels: 
      app: nginx 
  template: 
    metadata: 
      labels: 
        app: nginx 
    spec: 
      containers: 
      - name: nginx 
        image: nginx:latest 
        ports: 
        - containerPort: 80 
``` 
Services 
● Provides networking and IP address to access pods 
● Enables communication between different parts of an application 
● Types of services: 
○ ClusterIP: Default, exposes service on an internal IP within the cluster 
○ NodePort: Exposes service on each node's IP at a static port 
○ LoadBalancer: Exposes service externally using a cloud provider's load 
balancer 
○ ExternalName: Maps service to DNS name 
```
# Example of a Service YAML 
 
apiVersion: v1 
kind: Service 
metadata: 
  name: nginx-service 
spec: 
  type: ClusterIP 
  selector: 
    app: nginx 
  ports: 
  - port: 80 
    targetPort: 80 
``` 
Persistent Volumes (PV) & Persistent Volume Claims (PVC) 
● PV: A piece of storage provisioned by an administrator 
● PVC: A request for storage by a user that can be fulfilled by a PV 
```
# Example of a PV and PVC 
 
apiVersion: v1 
kind: PersistentVolume 
metadata: 
3 | MK 
 
  name: example-pv 
spec: 
  capacity: 
    storage: 1Gi 
  accessModes: 
    - ReadWriteOnce 
  hostPath: 
    path: "/mnt/data" --- 
apiVersion: v1 
kind: PersistentVolumeClaim 
metadata: 
  name: example-pvc 
spec: 
  accessModes: 
    - ReadWriteOnce 
  resources: 
    requests: 
      storage: 500Mi 
``` 
ConfigMaps and Secrets 
● ConfigMaps: Store non-confidential configuration data 
● Secrets: Store sensitive information like passwords and tokens 
```
# Example of a ConfigMap 
apiVersion: v1 
kind: ConfigMap 
metadata: 
  name: app-config 
data: 
  database_url: "mysql:3306" 
  app_mode: "development"

--- 

# Example of a Secret 
apiVersion: v1 
kind: Secret 
metadata: 
  name: app-secrets 
type: Opaque 
data: 
  username: YWRtaW4=  # base64 encoded 'admin' 
  password: cGFzc3dvcmQxMjM=  # base64 encoded 'password123' 
 
�
�
 Part 2: Environment Setup 
Before starting the hands-on tasks, you'll need to set up a Kubernetes environment. Here are 
some options: 
Option 1: Minikube (Local Development) 
Minikube creates a single-node Kubernetes cluster on your local machine. 
Installation: 
# For Linux 
curl -LO 
https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd6
4 
sudo install minikube-linux-amd64 /usr/local/bin/minikube 
 
# For Windows (run in PowerShell as Admin) 
New-Item -Path 'c:\' -Name 'minikube' -ItemType Directory -Force 
Invoke-WebRequest -OutFile 'c:\minikube\minikube.exe' -Uri 
'https://github.com/kubernetes/minikube/releases/latest/download/minikube-w
indows-amd64.exe' 
$env:Path += ';c:\minikube' 
 
# For macOS 
brew install minikube 
 
Start Minikube: 
minikube start 
 
Option 2: Kind (Kubernetes in Docker) 
Kind runs Kubernetes clusters using Docker containers as nodes. 
Installation: 
5 | MK 
 
# For Linux 
curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.17.0/kind-linux-amd64 
chmod +x ./kind 
sudo mv ./kind /usr/local/bin/kind 
 
# For Windows (run in PowerShell) 
curl.exe -Lo kind-windows-amd64.exe 
https://kind.sigs.k8s.io/dl/v0.17.0/kind-windows-amd64 
Move-Item .\kind-windows-amd64.exe c:\minikube\kind.exe 
 
# For macOS 
brew install kind 
 
Create a cluster: 
kind create cluster --name beginner-cluster 
 
Option 3: Docker Desktop 
Docker Desktop comes with Kubernetes support built-in. 
Enable Kubernetes: 
1. Open Docker Desktop 
2. Go to Settings/Preferences 
3. Select Kubernetes 
4. Check "Enable Kubernetes" 
5. Click Apply & Restart 
Required Tools 
In addition to a Kubernetes cluster, you'll need: 
kubectl: Command-line tool for interacting with Kubernetes clusters 
# For Linux 
curl -LO "https://dl.k8s.io/release/$(curl -L -s 
https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl" 
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl 
 
# For Windows (run in PowerShell as Admin) 
curl.exe -LO 
6 | MK 
 
"https://dl.k8s.io/release/v1.26.0/bin/windows/amd64/kubectl.exe" 
mkdir 'C:\Program Files\kubectl' 
Move-Item .\kubectl.exe 'C:\Program Files\kubectl' 
$env:Path += ';C:\Program Files\kubectl' 
 
# For macOS 
brew install kubectl 
● Docker: Container runtime for building and running containers 
 
Verify installation: 
kubectl version --client 
 
�
�
 Part 3: Hands-on Tasks 
Task 1: Deploy Your First Pod and Explore Basic Commands 
Objective: Create a simple nginx pod and learn basic kubectl commands. 
Steps: 
1. Create a file named first-pod.yaml: 
apiVersion: v1 
kind: Pod 
metadata: 
  name: first-nginx-pod 
  labels: 
    app: nginx 
spec: 
  containers: 
  - name: nginx 
    image: nginx:latest 
    ports: 
    - containerPort: 80 
 
 
7 | MK 
2. Apply the configuration: 
kubectl apply -f first-pod.yaml 
3. Verify the pod is running: 
kubectl get pods 
4. Get detailed information about the pod: 
kubectl describe pod first-nginx-pod 
5. Access the nginx web server: 
# This will forward local port 8080 to port 80 of the pod 
kubectl port-forward first-nginx-pod 8080:80 
6. Open a web browser and navigate to http://localhost:8080 to see the nginx 
welcome page 
7. View the logs of the pod: 
kubectl logs first-nginx-pod 
8. Execute a command inside the pod: 
9. Delete the pod: 
kubectl exec -it first-nginx-pod -- /bin/bash  
# Then run some commands inside the container 
ls -la 
exit 
kubectl delete pod first-nginx-pod 
Expected Outcome: You'll learn how to create, inspect, interact with, and delete a pod, which is 
the basic building block of Kubernetes. 
Task 2: Create a Deployment and Scale It 
Objective: Deploy an application using Deployments and learn how to scale it. 
Steps: 
1. Create a file named sample-deployment.yaml: 
8 | MK 
 
apiVersion: apps/v1 
kind: Deployment 
metadata: 
  name: sample-app 
  labels: 
    app: sample-app 
spec: 
  replicas: 2 
  selector: 
    matchLabels: 
      app: sample-app 
  template: 
    metadata: 
      labels: 
        app: sample-app 
    spec: 
      containers: 
      - name: sample-app 
        image: httpd:latest 
        ports: 
        - containerPort: 80 
 
2. Apply the deployment: 
kubectl apply -f sample-deployment.yaml 
3. Check the deployment status: 
kubectl get deployments 
4. View the pods created by the deployment: 
kubectl get pods -l app=sample-app 
5. Scale the deployment to run 5 replicas: 
kubectl scale deployment sample-app --replicas=5 
6. Verify the new pods are created: 
kubectl get pods -l app=sample-app 
7. Update the image used by the deployment: 
9 | MK 
 
kubectl set image deployment/sample-app sample-app=httpd:2.4-alpine 
8. Check the rollout status: 
kubectl rollout status deployment/sample-app 
9. View the rollout history: 
kubectl rollout history deployment/sample-app 
 
Expected Outcome: You'll understand how Deployments manage pods, provide scaling 
capabilities, and support rolling updates. 
Task 3: Expose Your Application Using Different Service Types 
Objective: Learn how to create different types of Services to expose your application. 
Steps: 
1. Ensure your sample-app deployment from Task 2 is running 
2. Create a ClusterIP service (only accessible within the cluster): 
Create a file named clusterip-service.yaml: 
apiVersion: v1 
kind: Service 
metadata: 
  name: sample-clusterip 
spec: 
  type: ClusterIP 
  selector: 
    app: sample-app 
  ports: 
  - port: 80 
    targetPort: 80 
 
3. Apply the ClusterIP service: 
kubectl apply -f clusterip-service.yaml 
4. Get the service details: 
kubectl get service sample-clusterip 
10 | MK 
 
5. Test the ClusterIP service from within the cluster: 
# Create a temporary pod to test the service 
kubectl run temp-pod --image=busybox -it --rm -- wget -qO- sample-clusterip 
6. Create a NodePort service (accessible on each node's IP): 
Create a file named nodeport-service.yaml: 
apiVersion: v1 
kind: Service 
metadata: 
  name: sample-nodeport 
spec: 
  type: NodePort 
  selector: 
    app: sample-app 
  ports: 
  - port: 80 
    targetPort: 80 
    nodePort: 30080  # Optional, if not specified, a random port between 
30000-32767 will be assigned 
 
7. Apply the NodePort service: 
kubectl apply -f nodeport-service.yaml 
8. Get the service details: 
kubectl get service sample-nodeport 
9. Access the NodePort service: 
○ For Minikube: minikube service sample-nodeport --url 
○ For other setups: Access http://<node-ip>:30080 
10. If you have a cloud environment, create a LoadBalancer service: 
Create a file named loadbalancer-service.yaml: 
 
apiVersion: v1 
kind: Service 
metadata: 
  name: sample-lb 
11 | MK 
spec: 
type: LoadBalancer 
selector: 
app: sample-app 
ports: - port: 80 
targetPort: 80 
11. Apply the LoadBalancer service (Note: This will create an actual load balancer in cloud 
environments): 
kubectl apply -f loadbalancer-service.yaml 
12. Get the LoadBalancer details: 
kubectl get service sample-lb 
Expected Outcome: You'll understand the different service types in Kubernetes and how they 
enable access to your applications. 
Task 4: Work with ConfigMaps and Secrets 
Steps: 
Objective: Learn how to use ConfigMaps and Secrets to manage configuration and sensitive 
data. 
1. Create a ConfigMap: 
Create a file named app-config.yaml: 
apiVersion: v1 
kind: ConfigMap 
metadata: 
name: app-config 
data: 
app.properties: | 
environment=development 
log_level=INFO 
database_url=mysql:3306/dev 
feature_flags: | 
enable_new_dashboard=true 
12 | MK 
 
    enable_user_profiles=false 
 
2. Apply the ConfigMap: 
kubectl apply -f app-config.yaml 
 
3. Create a Secret: 
Create a file named app-secret.yaml: 
apiVersion: v1 
kind: Secret 
metadata: 
  name: app-secret 
type: Opaque 
stringData:  # Using stringData allows us to use plain text (it will be 
encoded for us) 
  db_user: admin 
  db_password: supersecret 
 
4. Apply the Secret: 
kubectl apply -f app-secret.yaml 
 
5. Create a pod that uses both ConfigMap and Secret: 
Create a file named configmap-secret-pod.yaml: 
apiVersion: v1 
kind: Pod 
metadata: 
  name: config-secret-pod 
spec: 
  containers: 
  - name: app 
    image: busybox 
    command: ["/bin/sh", "-c", "while true; do echo 'Running...'; sleep 
3600; done"] 
    volumeMounts: 
    - name: config-volume 
      mountPath: /config 
13 | MK 
 
    - name: secret-volume 
      mountPath: /secrets 
    env: 
    - name: ENVIRONMENT 
      valueFrom: 
        configMapKeyRef: 
          name: app-config 
          key: app.properties 
    - name: DB_USER 
      valueFrom: 
        secretKeyRef: 
          name: app-secret 
          key: db_user 
    - name: DB_PASSWORD 
      valueFrom: 
        secretKeyRef: 
          name: app-secret 
          key: db_password 
  volumes: 
  - name: config-volume 
    configMap: 
      name: app-config 
  - name: secret-volume 
    secret: 
      secretName: app-secret 
 
6. Apply the pod configuration: 
kubectl apply -f configmap-secret-pod.yaml 
 
7. Verify the pod is using the ConfigMap and Secret: 
# Check environment variables 
kubectl exec config-secret-pod -- env | grep -E 'ENVIRONMENT|DB_' 
 
# Check mounted volumes 
kubectl exec config-secret-pod -- ls -la /config 
kubectl exec config-secret-pod -- cat /config/app.properties 
kubectl exec config-secret-pod -- ls -la /secrets 
kubectl exec config-secret-pod -- cat /secrets/db_user 
 
14 | MK 
 
Expected Outcome: You'll understand how to use ConfigMaps for configuration data and 
Secrets for sensitive information, and how to inject them into pods. 
Task 5: Set Up Persistent Storage with PV and PVC 
Objective: Learn how to create and use Persistent Volumes and Persistent Volume Claims. 
Steps: 
1. Create a Persistent Volume (PV): 
Create a file named sample-pv.yaml: 
apiVersion: v1 
kind: PersistentVolume 
metadata: 
  name: sample-pv 
spec: 
  capacity: 
    storage: 1Gi 
  accessModes: 
    - ReadWriteOnce 
  persistentVolumeReclaimPolicy: Retain 
  hostPath:  # For production, you'd use a proper storage solution 
    path: "/mnt/data" 
 
2. Apply the PV: 
kubectl apply -f sample-pv.yaml 
3. Check the PV status: 
kubectl get pv 
4. Create a Persistent Volume Claim (PVC): 
Create a file named sample-pvc.yaml: 
apiVersion: v1 
kind: PersistentVolumeClaim 
metadata: 
  name: sample-pvc 
spec: 
  accessModes: 
    - ReadWriteOnce 
15 | MK 
 
  resources: 
    requests: 
      storage: 500Mi 
 
5. Apply the PVC: 
kubectl apply -f sample-pvc.yaml 
6. Check the PVC status: 
kubectl get pvc 
7. Create a Pod that uses the PVC: 
Create a file named pv-pod.yaml: 
apiVersion: v1 
kind: Pod 
metadata: 
  name: pv-pod 
spec: 
  containers: 
  - name: task-pv-container 
    image: nginx 
    ports: 
    - containerPort: 80 
    volumeMounts: 
    - mountPath: "/usr/share/nginx/html" 
      name: task-pv-storage 
  volumes: 
  - name: task-pv-storage 
    persistentVolumeClaim: 
      claimName: sample-pvc 
 
8. Apply the pod configuration: 
kubectl apply -f pv-pod.yaml 
9. Verify the pod is running: 
kubectl get pod pv-pod 
10. Create some data in the persistent volume: 
16 | MK 
kubectl exec pv-pod -- sh -c "echo 'Hello from Kubernetes storage!' > 
/usr/share/nginx/html/index.html" 
11. Verify the data was saved: 
kubectl exec pv-pod -- cat /usr/share/nginx/html/index.html 
12. Delete the pod: 
kubectl delete pod pv-pod 
13. Create a new pod with the same PVC to verify data persistence: 
Create a file named pv-pod2.yaml: 
apiVersion: v1 
kind: Pod 
metadata: 
name: pv-pod2 
spec: 
containers: - name: task-pv-container 
image: nginx 
ports: - containerPort: 80 
volumeMounts: - mountPath: "/usr/share/nginx/html" 
name: task-pv-storage 
volumes: - name: task-pv-storage 
persistentVolumeClaim: 
claimName: sample-pvc 
14. Apply the second pod: 
kubectl apply -f pv-pod2.yaml 
15. Verify the data persisted: 
kubectl exec pv-pod2 -- cat /usr/share/nginx/html/index.html 
Expected Outcome: You'll understand how Persistent Volumes and Persistent Volume Claims 
work to provide persistent storage for your applications. 
17 | MK 
�
�
Assessment & Next Steps 
Self-Assessment Questions 
After completing the hands-on tasks, try to answer these questions: 
1. What is the difference between a Pod and a Deployment? 
2. Why would you use a ConfigMap instead of hardcoding configuration values in your 
application? 
3. What are the three main types of Services in Kubernetes and when would you use 
each? 
4. What is the relationship between a PersistentVolume and a PersistentVolumeClaim? 
5. How can you scale a Deployment and why is this useful? 
Next Steps for Learning 
Once you've completed these beginner tasks, consider exploring: 
1. Kubernetes Namespaces - Organizing your resources 
2. Resource Limits and Requests - Managing compute resources 
3. Ingress Controllers - Advanced networking and routing 
4. StatefulSets - For stateful applications 
5. Helm - Package manager for Kubernetes 
6. Kubernetes RBAC - Role-Based Access Control for security 
7. Custom Resource Definitions (CRDs) - Extending Kubernetes API 
Recommended Resources 
● Kubernetes Official Documentation 
● Kubernetes: Up and Running book 
● Kubernetes The Hard Way for advanced learning 
● Kubernetes Playground on Katacoda 
�
�
Troubleshooting Common Issues 
Pod Stuck in Pending State 
● Cause: Insufficient resources or PersistentVolumeClaim not bound 
● Solution: Check events with kubectl describe pod <pod-name> and ensure 
sufficient resources 
Pod Stuck in CrashLoopBackOff 
18 | MK 
● Cause: Container exits immediately after starting 
● Solution: Check logs with kubectl logs <pod-name> and fix application errors 
Service Not Accessible 
● Cause: Incorrect selector labels or port configuration 
● Solution: Verify service and pod labels match with kubectl describe service 
<service-name> 
Unable to Pull Image 
● Cause: Image not found or private registry credentials missing 
● Solution: Check image name/tag and ensure proper ImagePullSecrets are configured 
Storage Issues 
● Cause: PV not available or incompatible access modes 
● Solution: Check PV/PVC status with kubectl get pv,pvc and ensure compatibility 
19 | MK 
