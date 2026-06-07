## Kubernetes (K8S) & Microservices 

### 1. Architectural Styles: How We Build Software

Before looking at the shipping network (Kubernetes), let’s look at the two ways companies pack and ship their products (software applications).
Monolithic Architecture (The Traditional Mall)
Think of a Monolith as a giant, single-building shopping mall.
How it works: The grocery store, the movie theater, the clothing shop, and the restrooms all share the same building, the same electricity, and the same foundation.
The Problem: If the grocery store suffers a localized electrical fire, the entire mall has to close down for safety. If the movie theater gets packed on Friday night, you cannot easily expand just the theater; you would have to duplicate the entire mall building.
Microservices Architecture (The High-Street Shops)
Think of Microservices as separate, independent boutique shops lined up on a street.
How it works: The grocery store is in its own building. The movie theater is across the street. They function independently and talk to each other via phones (APIs).
The Benefit: If the grocery store gets a leak, the movie theater stays open. If the theater gets busy, you can expand just that single building without touching the grocery store.

### 2. What is Kubernetes (K8S)?
If your application is split into dozens of tiny, independent microservices, managing them manually is a nightmare.
Kubernetes is your Global Shipping Port Authority. It is an automated management system that coordinates where your software sits, ensures it has power, scales it up when busy, and replaces it instantly if it breaks.

### 3. Local Installation & Setup (Your Mini Port)
You can run a complete, miniature version of this shipping port right on your personal laptop for testing.
- Step 1: Docker Desktop (The Foundation)
Download and install Docker Desktop.
Open Settings (the gear icon).
Click on Kubernetes in the left sidebar.
Check the box that says "Enable Kubernetes" and click Apply & Restart.
- Step 2: Kind (Kubernetes in Docker)
kind is a tool that lets you spin up local Kubernetes clusters instantly using default settings.
Open your terminal and run: kind create cluster
This automatically creates a default single-node cluster (one truck acting as both management and cargo space).
⚠️ Emergency Reset Button: If you install a network mesh (like Istio) and your configuration gets tangled up or broken, do not panic. Simply open Docker Desktop settings, go to the Kubernetes tab, and click "Reset Kubernetes Cluster" to wipe the slate clean and start fresh.

### 4. The Cluster Layout: Control Plane vs. Data Plane
A Kubernetes cluster is divided into two distinct zones: The Management Office and The Cargo Docks.
```
+------------------------------------------------------------+

|               CONTROL PLANE (Master Node)                  |
|               "The Port Authority Office"                  |
|                                                            |
|  +------------+   +------------+   +-----------+   +----+  |
|  | API Server |   | Scheduler  |   | Controller|   |etcd|  |
|  +------------+   +------------+   +-----------+   +----+  |
+-----------------------------+------------------------------+

                              |
                     Commands & Status
                              |
+-----------------------------v------------------------------+

|                DATA PLANE (Worker Nodes)                   |
|                    "The Cargo Docks"                       |
|                                                            |
|  +--------------------+          +--------------------+    |
|  |    WORKER NODE 1   |          |    WORKER NODE 2   |    |
|  |  +--------------+  |          |  +--------------+  |    |
|  |  |   Kubelet    |  |          |  |   Kubelet    |  |    |
|  |  +--------------+  |          |  +--------------+  |    |
|  |  +--------------+  |          |  +--------------+  |    |
|  |  |  Kube-Proxy  |  |          |  |  Kube-Proxy  |  |    |
|  |  +--------------+  |          |  +--------------+  |    |
|  |  +--------------+  |          |  +--------------+  |    |
|  |  | Containerd   |  |          |  | Containerd   |  |    |
|  |  +--------------+  |          |  +--------------+  |    |
|  +--------------------+          +--------------------+    |
+------------------------------------------------------------+
```
### 5. Inside the Control Plane (The Master Node)
This is the brain of the cluster. It doesn't run your website; it makes all the executive decisions.
API Server (The Reception Desk): The only entry point to the office. When you want to deploy a new app, you submit a request to the API Server. It validates who you are and passes the order inside.
ETCD (The Master Logbook): A secure digital vault. It holds the absolute truth of the entire port. It records exactly how many apps should be running, where they are, and their current health.
Scheduler (The Logistics Planner): This manager looks at your new app request, checks which Worker Nodes (trucks) have free space (CPU/Memory), and assigns the app to the best available machine.
Controller Manager (The Quality Inspector): This office constantly compares the live port status against the Master Logbook. If you ordered 3 copies of an app to run, and 1 crashes, the inspector instantly notices and orders a replacement.
The "Watch" System: How They Communicate - Think of the API Server as having a giant, live-updating Status Board in the main lobby. The Scheduler and Controller Manager pull up chairs, sit in front of this board, and watch it like hawks.
   ```
     +--------------------------------------------------------+

    |                     CONTROL PLANE                      |
    |                                                        |
    |               +────────────────────────+               |
    |               |       API SERVER       |               |
    |               |  (The Live Status Board)               |
    |               +───────────▲────────────+               |
    |                           │                            |
    |       ┌───────────────────┼───────────────────┐        |
    |       │ Watches Board     │ Watches Board     │        |
    |       ▼                   │                   ▼        |
    | +───────────+             │             +────────────+ |
    | | SCHEDULER |             │             | CONTROLLER | |
    | +───────────+             │             |  MANAGER   | |
    |                           │             +────────────+ |
    +---------------------------│----------------------------+
                                │
                        kubectl get pods
                                │
                        [ You / kubectl ]
```

### 6. Inside the Data Plane (The Worker Nodes)
These are the actual machinery, computers, and servers where your application boxes (Pods) live and work.
Kubelet (The Dock Manager): A tiny supervisor running on every worker node. It takes direct orders from the Control Plane's API Server. It physically spins up your apps, monitors their health, and reports back to headquarters.
Kube-Proxy (The Traffic Police): The network coordinator on the node. It assigns IP addresses and manages traffic rules, making sure external internet users get routed to the correct app container inside the machine.
Containerd / Container d (The Cargo Crane): The low-level software utility. When the Kubelet says "run this app," Containerd acts as the heavy machinery that grabs the application package, unwraps it, and boots it up inside the server.

### 7. The Mediator Role (kubctl utility)
The Mediator Role How kubectl Connects You to the Master Node You cannot talk directly to the ETCD logbook or the Scheduler. Everything must go through the API Server (The Front Reception Desk). kubectl is the certified courier that speaks the exact language of the front desk.
``` 
   [ You / Kubectl ] 
         │
         ▼
  +--------------+
  |   kubectl    |  ◄─── (The Mediator: Takes your command, packs it
  +--------------+        into a standard format, and drives to the port)
         │
         ▼  [ Secure HTTPS Request ]
  +--------------------------------------------------+
  | CONTROL PLANE (Master Node)                      |
  |                                                  |
  |    +───────────────────+                         |
  |    |    API Server     |  ◄─── (Receives and processes your command)
  |    +───────────────────+                         |
  +--------------------------------------------------+
```
### 8. Top 51 essential kubectl commands 
Initial Setup & Context Management (The Wallet)
```
kubectl version — Displays the current client and server Kubernetes versions.
kubectl cluster-info — Shows the master network address and active control plane services.
kubectl config get-contexts — Lists all available cluster connections and saved credentials.
kubectl config current-context — Identifies the specific cluster you are currently targeting.
kubectl config use-context <name> — Switches your active target context to a different cluster.
kubectl api-resources — Lists all resource types supported by the cluster (e.g., pods, services).
```
Creating & Managing Resources (The Blueprint Delivery)
```
kubectl apply -f <file.yaml> — Creates or updates infrastructure objects defined in a YAML configuration file.
kubectl delete -f <file.yaml> — Destroys all cluster infrastructure objects defined in that specific file.
kubectl run <name> --image=<img> — Spins up a single, isolated ad-hoc Pod using a designated container image.
kubectl create deployment <name> --image=<img> — Spins up a managed deployment strategy to run scaled copies of an application.
kubectl expose deployment <name> --port=<num> — Instantly builds a network service balancer around your deployment to route traffic.
```
Basic Viewing & Inspection (The Status Checks)
```
kubectl get nodes — Lists all physical or virtual computer servers serving the cluster.
kubectl get pods — Lists all running application container wrappers in the current workspace namespace.
kubectl get pods -o wide — Displays a list of running pods enriched with IP addresses and host node assignments.
kubectl get deployments — Shows the current target vs. actual status of running scaled applications.
kubectl get services — Displays all exposed persistent internal and external application IP pathways.
kubectl get namespaces — Shows all logical tenant isolation barriers separating environments.
kubectl get all — Fetches a comprehensive dashboard summary of every active resource running in your namespace. 
```
Filtering & Custom Scopes (The Magnifying Glass) 
```
kubectl get pods -A — Lists all active pods running across every internal namespace in the system.
kubectl get pods -n <name> — Restricts your viewing list strictly to one targeted namespace group.
kubectl get pods -l app=<value> — Filters and fetches only objects matching a specific organizational tag label.
kubectl get pod <name> -o yaml — Extracts the full, live configuration parameters of an asset into a structured format.
kubectl get pods --field-selector=status.phase=Running — Filters and retrieves objects based on specific live state properties. 
```
Deep Troubleshooting & Diagnostics (The Inspector Toolkit)
```
kubectl describe node <name> — Reports detailed hardware performance, capacity allocations, and low-level system events.
kubectl describe pod <name> — Provides detailed event logs explaining why a box is crashing or failing to start.
kubectl logs <name> — Prints the standard structural application printouts and error statements.
kubectl logs -f <name> — Establishes a live, streaming output link directly into the application's runtime logs.
kubectl logs <name> --previous — Retrieves terminal output messages written by an application immediately before its last crash.
kubectl top node — Displays real-time CPU and Memory resource consumption scores across cluster machines.
kubectl top pod — Pinpoints exactly which application containers are hogging the most host memory or computing power. 
```
Direct Interaction & Execution (The Remote Control)
```
kubectl exec -it <name> -- sh — Opens a live interactive terminal gateway directly inside an active running application container.
kubectl exec <name> -- <command> — Forces a container to run an isolated OS instruction without entering its filesystem shell.
kubectl port-forward <pod-name> <local>:<remote> — Builds a direct, temporary proxy tunnel mapping a cluster app port onto your personal laptop.
kubectl attach <name> — Connects directly to a running process stream to witness real-time inner program actions.
kubectl cp <source> <dest> — Securely copies physical files back and forth between your local computer and a remote container filesystem. 
```
Dynamic Modifications & Scaling (The Adjuster)
```
kubectl scale deployment <name> --replicas=<num> — Instructs the cluster to instantly add or reduce identical application instances.
kubectl edit <resource> <name> — Opens the live cluster data manifest inside a terminal text editor for instant runtime updates.
kubectl label pod <name> status=ready — Appends searchable organizational metadata tags directly onto active objects.
kubectl annotate pod <name> description="text" — Injects informational documentation notes directly onto cluster assets.
kubectl rollout status deployment/<name> — Watches the live milestone progress of an evolving application software upgrade.
kubectl rollout undo deployment/<name> — Triggers an immediate rollback to the previous safe code configuration version when an update fails.
kubectl rollout restart deployment/<name> — Forces a clean, rolling replacement restart of all containers tied to a deployment. 
```
Resource Lifecycles & Security (The Cleanup & Access Crews)
```
kubectl delete pod <name> — Gracefully terminates an active container box, triggering automated infrastructure self-healing.
kubectl delete namespace <name> — Wipes clean an entire logical workspace and everything stored inside it.
kubectl get secrets — Lists specialized encrypted security stores holding API keys or database passwords.
kubectl get configmaps — Lists text-based operational variable sheets shared among running applications.
kubectl auth can-i <verb> <resource> — Simulates an action to test whether your current identity holds administrative security permissions. 
```
Advanced Maintenance (The Dock Operations)
```
kubectl cordon <node-name> — Marks a specific machine as unschedulable to stop any new boxes from being loaded onto it.
kubectl uncordon <node-name> — Re-opens a blacklisted node back into service to allow future container assignments.
kubectl drain <node-name> --ignore-daemonsets — Evacuates all active cargo pods safely off a server machine ahead of hardware maintenance.
```
kubectl explain pods — Queries the central documentation engine to output reference schemas and valid fields for any object type. 
