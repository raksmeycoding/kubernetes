## 🚧 Step-by-Step Guide

### ✅ Step 1: Create two namespaces `ns1` and `ns2`

```bash
kubectl create namespace ns1
kubectl create namespace ns2
```

---

### ✅ Step 2: Create Deployments in both namespaces

We'll deploy `nginx` with **1 replica** in each namespace.

#### 📝 deploy-ns1.yaml:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: deploy-ns1
  namespace: ns1
spec:
  replicas: 1
  selector:
    matchLabels:
      app: myapp
  template:
    metadata:
      labels:
        app: myapp
    spec:
      containers:
        - name: nginx
          image: nginx
```

#### 📝 deploy-ns2.yaml:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: deploy-ns2
  namespace: ns2
spec:
  replicas: 1
  selector:
    matchLabels:
      app: myapp
  template:
    metadata:
      labels:
        app: myapp
    spec:
      containers:
        - name: nginx
          image: nginx
```

Apply both:

```bash
kubectl apply -f deploy-ns1.yaml
kubectl apply -f deploy-ns2.yaml
```

---

### ✅ Step 3: Get the IP of each pod

```bash
kubectl get pods -n ns1 -o wide
kubectl get pods -n ns2 -o wide
```

Note the `IP` column for each pod.

---

### ✅ Step 4: Test pod-to-pod communication

Let’s `exec` into the `ns1` pod and `curl` the IP of the pod in `ns2`.

```bash
kubectl exec -n ns1 -it <POD_NAME_NS1> -- /bin/sh
# Inside pod:
curl <IP_OF_NS2_POD>
```

✅ You should see the **Nginx welcome HTML** in the output. Exit the shell.

---

### ✅ Step 5: Scale both deployments to 3 replicas

```bash
kubectl scale deployment deploy-ns1 --replicas=3 -n ns1
kubectl scale deployment deploy-ns2 --replicas=3 -n ns2
```

Verify:

```bash
kubectl get pods -n ns1
kubectl get pods -n ns2
```

---

### ✅ Step 6: Create services in both namespaces

#### 📝 svc-ns1.yaml

```yaml
apiVersion: v1
kind: Service
metadata:
  name: svc-ns1
  namespace: ns1
spec:
  selector:
    app: myapp
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
```

#### 📝 svc-ns2.yaml

```yaml
apiVersion: v1
kind: Service
metadata:
  name: svc-ns2
  namespace: ns2
spec:
  selector:
    app: myapp
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
```

Apply both:

```bash
kubectl apply -f svc-ns1.yaml
kubectl apply -f svc-ns2.yaml
```

---

### ✅ Step 7: Cross-namespace service curl using IP

First, get service IPs:

```bash
kubectl get svc -n ns1
kubectl get svc -n ns2
```

Then `exec` into a pod in one namespace and curl the **service IP** of the other:

```bash
kubectl exec -n ns1 -it <POD_NAME> -- /bin/sh
# Inside:
curl <CLUSTER_IP_OF_svc-ns2>
kubectl exec -n ns1 -it deployment-ns1-5b5df85c44-5gm4b -- sh
curl 10.244.1.4 # other pod in another namespace
```

✅ You should get a response.

---

### ❌ Step 8: Curl by **service name only** (this fails)

Inside a pod in `ns1`, run:

```bash
curl svc-ns2
```

❌ This will fail — because Kubernetes doesn’t resolve cross-namespace services by short names.

---

### ✅ Step 9: Curl by FQDN (Fully Qualified Domain Name)

Try this instead:

```bash
curl svc-ns2.ns2.svc.cluster.local
```

✅ This will work.

> Format: `service-name.namespace.svc.cluster.local`

---

### ✅ Step 10: Clean up

```bash
kubectl delete namespace ns1
kubectl delete namespace ns2
```

This deletes everything inside.

---

## 📘 Final `README.md` Style Documentation

# Task 10/40: Kubernetes Namespaces and Networking

In this task, we explored Kubernetes namespaces, inter-pod networking, and cross-namespace service communication.

## ✅ What We Did

### 1. Created Namespaces
```bash
kubectl create namespace ns1
kubectl create namespace ns2
````

### 2. Deployed Nginx in Each Namespace

Used `nginx` deployment with 1 replica in `ns1` and `ns2`.

### 3. Verified Pod-to-Pod Communication

Exec'd into a pod in `ns1` and `curl`ed a pod IP in `ns2`.

### 4. Scaled Deployments to 3 Replicas

```bash
kubectl scale deployment deploy-ns1 --replicas=3 -n ns1
kubectl scale deployment deploy-ns2 --replicas=3 -n ns2
```

### 5. Created ClusterIP Services for Both Deployments

Service names: `svc-ns1` and `svc-ns2`

### 6. Tested Cross-Service Communication via:

* ✅ **ClusterIP**
* ❌ **Service short name** across namespaces
* ✅ **FQDN** (`svc-ns2.ns2.svc.cluster.local`)

### 7. Cleaned Up

```bash
kubectl delete namespace ns1
kubectl delete namespace ns2
```

---

## 📘 Learnings

* Namespaces isolate resources but allow pod-to-pod communication.
* DNS resolution across namespaces requires FQDN format.
* Services allow stable access to backend pods even if pods restart.

---

## 📢 Social Share Template

> Today I explored Kubernetes Namespaces in #40DaysOfKubernetes 🌐
> ✅ Namespaces
> ✅ Deployments in isolated namespaces
> ✅ Pod-to-Pod and Service-to-Service communication
> ✅ DNS FQDN resolution
> 🧠 Learned how Kubernetes organizes resources logically
>
> 🔗 Watch the tutorial: [https://youtu.be/RORhczcOrWs](https://youtu.be/RORhczcOrWs)
>
> @PiyushSachdeva @CloudOps Community #Kubernetes #DevOps #CloudNative

---

Let me know if you'd like this turned into a blog layout or want visuals/diagrams too!
