
# Navigating the Gateways: Unveiling Kubernetes Ingress Controllers

**Oct 4, 2024**

---

> “A ship in harbor is safe, but that is not what ships are built for.”
> — John A. Shedd

In the vast ocean of microservices and container orchestration, Kubernetes stands as a lighthouse, guiding ships through stormy seas. But even lighthouses need gateways — entrances that allow ships to dock safely at the harbor. In the realm of Kubernetes, Ingress Controllers serve as these gateways, managing the flow of external traffic to your cluster. As I delve into this intricate dance of traffic management, I realize that Ingress Controllers are not just technical components; they are the guardians of our digital harbors, ensuring safe passage for data in a world that’s both vast and unpredictable.

---

## The Labyrinth of Kubernetes Networking

I remember the first time I deployed an application on Kubernetes. It felt like constructing a city from scratch — a city where every service, pod, and node had its place. Yet, despite the city’s complexity, it lacked roads connecting it to the outside world. Exposing services to external traffic was like trying to find a secret doorway in an ever-shifting maze.

That’s where Ingress Controllers come into play. They are the architects who build bridges and gateways, allowing the world to interact with your Kubernetes city seamlessly. Without them, your applications remain isolated, inaccessible behind the fortress walls of the cluster.

---

## Why Ingress Controllers Matter

In a world where connectivity is king, managing how external users access your services is crucial. You could expose each service individually using NodePorts or LoadBalancers, but that approach is like giving every house in a city its own highway exit — impractical and chaotic.

Ingress Controllers provide a more elegant solution. They act as a single entry point, routing traffic based on rules you define. It’s like having a well-organized train station where passengers are directed to the right platforms efficiently. This not only simplifies your infrastructure but also enhances security and scalability.

---

## Setting Sail with Ingress Controllers: A Step-by-Step Guide

Embarking on the journey to implement an Ingress Controller might seem daunting, but with a compass in hand and a map to follow, the path becomes clearer. Let’s navigate this journey together.

### Prerequisites
- A running Kubernetes cluster (version 1.19 or later recommended)
- `kubectl` command-line tool configured to communicate with your cluster

---

### Step 1: Deploy a Sample Application

First, we’ll deploy a simple application to demonstrate how Ingress Controllers work. Let’s create two services: `service-one` and `service-two`.

#### Create Deployment and Service for `service-one`:

```yaml
# service-one-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: service-one
spec:
  replicas: 2
  selector:
    matchLabels:
      app: service-one
  template:
    metadata:
      labels:
        app: service-one
    spec:
      containers:
      - name: service-one
        image: hashicorp/http-echo
        args:
        - "-text=Hello from Service One"
        ports:
        - containerPort: 5678
---
# service-one-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: service-one
spec:
  selector:
    app: service-one
  ports:
  - protocol: TCP
    port: 80
    targetPort: 5678
```

Apply the configuration:

 
kubectl apply -f service-one-deployment.yaml
kubectl apply -f service-one-service.yaml
```

#### Repeat for `service-two`:

```yaml
# service-two-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: service-two
spec:
  replicas: 2
  selector:
    matchLabels:
      app: service-two
  template:
    metadata:
      labels:
        app: service-two
    spec:
      containers:
      - name: service-two
        image: hashicorp/http-echo
        args:
        - "-text=Hello from Service Two"
        ports:
        - containerPort: 5678
---
# service-two-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: service-two
spec:
  selector:
    app: service-two
  ports:
  - protocol: TCP
    port: 80
    targetPort: 5678
```

Apply the configuration:

 
kubectl apply -f service-two-deployment.yaml
kubectl apply -f service-two-service.yaml
```

---

### Step 2: Install an Ingress Controller

Kubernetes doesn’t come with a built-in Ingress Controller. You have to choose one based on your needs. For this guide, we’ll use the NGINX Ingress Controller.

Install NGINX Ingress Controller:

 
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/cloud/deploy.yaml
```

This command deploys the NGINX Ingress Controller in your cluster. It might take a few minutes for all components to be up and running.

---

### Step 3: Create Ingress Resources

Now, we’ll define how traffic should be routed to our services.

```yaml
# ingress.yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: example-ingress
spec:
  rules:
  - host: service-one.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: service-one
            port:
              number: 80
  - host: service-two.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: service-two
            port:
              number: 80
```

Apply the Ingress resource:

 
kubectl apply -f ingress.yaml
```

---

### Step 4: Update DNS or Hosts File

For testing purposes, update your local hosts file to point the example domains to the Ingress Controller's IP address.

1. Get the Ingress Controller’s External IP:

    
   kubectl get service ingress-nginx-controller -n ingress-nginx
   ```

2. Add the following lines to your `/etc/hosts` file:

   ```
   <EXTERNAL-IP>  service-one.example.com
   <EXTERNAL-IP>  service-two.example.com
   ```

---

### Step 5: Test the Setup

Test accessing your services through the Ingress Controller:

 
curl http://service-one.example.com
# Output: Hello from Service One

curl http://service-two.example.com
# Output: Hello from Service Two
```

---

## The Symphony of Traffic Management

Implementing an Ingress Controller is like conducting a symphony, where every note and instrument must be in harmony. The controller listens to the rules you’ve defined and orchestrates the flow of data, ensuring that every request reaches its intended destination.

---

## Beyond the Basics: Securing Your Ingress

Enable TLS:

1. Create a TLS secret:

    
   kubectl create secret tls example-tls --key tls.key --cert tls.crt
   ```

2. Update your Ingress resource:

   ```yaml
   spec:
     tls:
     - hosts:
       - service-one.example.com
       - service-two.example.com
       secretName: example-tls
   ```

---

## Reflections on the Journey

As I navigated through the implementation of Kubernetes Ingress Controllers, I couldn’t help but draw parallels to life’s own gateways — the decisions and paths that lead us to new experiences.

---

## Embracing the Clouds

In the grand tapestry of cloud computing, Kubernetes Ingress Controllers are the gatekeepers that make seamless connectivity possible.

> “A smooth sea never made a skilled sailor.”
> — Franklin D. Roosevelt
