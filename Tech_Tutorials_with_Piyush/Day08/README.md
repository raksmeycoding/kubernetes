# Task 8/40 – Deployments, ReplicaSets, Rollouts, and Rollbacks in Kubernetes

In this lesson, you’ll work with **ReplicaSets** and **Deployments** in Kubernetes. You’ll also explore rolling updates, rollbacks, and how to troubleshoot invalid YAML files.

> 💡 **Tip**: If you don’t already have a Kubernetes cluster, you can create a local cluster using [this video tutorial](https://youtu.be/RORhczcOrWs).

---

## 🧱 Section 1: ReplicaSet Basics

### ✅ Step 1: Create a ReplicaSet

Create a ReplicaSet with the NGINX image and 3 replicas:

```bash
kubectl create rs nginx-rs --image=nginx --replicas=3
```

> Or generate and apply YAML for more control.

### ✅ Step 2: Update Replicas to 4 via YAML

Edit the ReplicaSet manifest:

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: nginx-rs
spec:
  replicas: 4
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
          image: nginx
```

Apply it:

```bash
kubectl apply -f replicaset.yaml
```

### ✅ Step 3: Update Replicas to 6 via Command

```bash
kubectl scale rs nginx-rs --replicas=6
```

---

## 🚀 Section 2: Deployments with Rollout and Rollback
Great question. Let me break it down clearly — this is a **step-by-step Kubernetes exercise** testing your understanding of Deployments, scaling, rolling updates, rollback, and troubleshooting.

---

### ✅ **What They Want You To Do**

They want you to **demonstrate your ability to manage a Kubernetes Deployment** in a production-like workflow. Here's what each step means and what they're testing:

---

### 1. **Create a Deployment named `nginx` with 3 replicas**

* **Goal**: Create a Deployment resource using YAML or `kubectl`.
* **Expectation**:

    * 3 Pods will be created.
    * Each Pod runs the container `nginx:1.23.0` and is named `nginx`.
    * The Deployment has a label: `tier=backend`.
    * The Pods have the label: `app=v1`.
* **Why**: This ensures you understand how to define metadata, selectors, templates, and manage replicas.

---

### 2. **List the Deployment and ensure 3 replicas are running**

* **Goal**: Use `kubectl get` commands.
* **Expectation**: You'll confirm your Deployment is active and 3 Pods are running correctly.
* **Why**: Verifies your Deployment was applied correctly and that you know how to inspect Kubernetes resources.

---

### 3. **Update the image to `nginx:1.23.4`**

* **Goal**: Perform a rolling update.
* **Expectation**: Modify the Deployment to use a newer version of the container image.
* **Why**: Demonstrates that you understand zero-downtime updates using `kubectl set image`.

---

### 4. **Verify that the change has been rolled out to all replicas**

* **Goal**: Use `kubectl rollout status`.
* **Expectation**: Show that all Pods are updated to `nginx:1.23.4`.
* **Why**: Confirms you understand how to monitor rollout progress and completion.

---

### 5. **Assign the change cause "Pick up patch version" to the revision**

* **Goal**: Annotate the Deployment with the reason for the change.
* **Expectation**: Add a human-readable note (aka change cause).
* **Why**: This is best practice for audit/history tracking, especially when troubleshooting.

---

### 6. **Scale the Deployment to 5 replicas**

* **Goal**: Increase the number of Pods.
* **Expectation**: Run a scale command or update the YAML.
* **Why**: Tests your ability to scale applications up/down on demand.

---

### 7. **Have a look at the Deployment rollout history**

* **Goal**: Inspect past changes and versions.
* **Expectation**: Use `kubectl rollout history` to see what changes occurred and when.
* **Why**: Shows you understand Kubernetes maintains a revision history for Deployments.

---

### 8. **Revert the Deployment to revision 1**

* **Goal**: Use a rollback command.
* **Expectation**: Roll back to the original image version (`nginx:1.23.0`).
* **Why**: Validates your ability to recover from bad changes or failed rollouts.

---

### 9. **Ensure that the Pods use the image `nginx:1.23.0`**

* **Goal**: Confirm the rollback was successful.
* **Expectation**: Inspect the current Pods and Deployment to verify the image version is back to the original.
* **Why**: Proves you know how to verify the actual container state matches what you intended.


### 🔍 Summary: What They're Testing

| Concept                   | Are You Being Tested On? |
| ------------------------- | ------------------------ |
| Creating Deployments      | ✅ Yes                    |
| Labeling and selectors    | ✅ Yes                    |
| Image updates (rolling)   | ✅ Yes                    |
| Rollout status monitoring | ✅ Yes                    |
| Deployment annotations    | ✅ Yes                    |
| Scaling Deployments       | ✅ Yes                    |
| Rollout history tracking  | ✅ Yes                    |
| Rollbacks                 | ✅ Yes                    |
| Pod verification          | ✅ Yes                    |


### ✅ Step 1: Create a Deployment

```bash
kubectl create deployment nginx \
  --image=nginx:1.23.0 \
  --replicas=3 \
  --dry-run=client -o yaml > nginx-deployment.yaml
```

Update the YAML to add proper labels:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
  labels:
    tier: backend
spec:
  replicas: 3
  selector:
    matchLabels:
      app: v1
  template:
    metadata:
      labels:
        app: v1
    spec:
      containers:
        - name: nginx
          image: nginx:1.23.0
```

Apply it:

```bash
kubectl apply -f nginx-deployment.yaml
```

---

### ✅ Step 2: Verify the Deployment

```bash
kubectl get deployments
kubectl get pods -l app=v1
```

---

### ✅ Step 3: Update the Image to `nginx:1.23.4`

```bash
kubectl set image deployment/nginx nginx=nginx:1.23.4
```

---

### ✅ Step 4: Verify the Rollout

```bash
kubectl rollout status deployment/nginx
```

---

### ✅ Step 5: Record the Rollout Change Cause

```bash
kubectl annotate deployment nginx kubernetes.io/change-cause="Pick up patch version"
```

> You can also use `--record` flag in older versions.

---

### ✅ Step 6: Scale the Deployment to 5 Replicas

```bash
kubectl scale deployment nginx --replicas=5
```

---

### ✅ Step 7: View Rollout History

```bash
kubectl rollout history deployment/nginx
```

---

### ✅ Step 8: Rollback to Revision 1

```bash
kubectl rollout undo deployment/nginx --to-revision=1
```

---

### ✅ Step 9: Verify the Pods are Running `nginx:1.23.0`

```bash
kubectl describe deployment nginx | grep Image
kubectl get pods -o wide
```

---

## 🛠️ Section 3: Troubleshooting Broken YAML Files

### 🧪 Problem YAML 1 (Fix Incorrect `apiVersion` and Missing `spec.selector`)

```yaml
# ❌ Original - Invalid
apiVersion: v1
kind: Deployment
metadata:
  name: nginx-deploy
  labels:
    env: demo
spec:
  template:
    metadata:
      labels:
        env: demo
      name: nginx
    spec:
      containers:
        - image: nginx
          name: nginx
          ports:
            - containerPort: 80
  replicas: 3
  selector:
    matchLabels:
      env: demo
```

✅ **Fixes**:
- `apiVersion` should be `apps/v1`
- `template.metadata.name` is invalid (remove it)
- Double-check the `selector`

### ✅ Fixed YAML:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deploy
  labels:
    env: demo
spec:
  replicas: 3
  selector:
    matchLabels:
      env: demo
  template:
    metadata:
      labels:
        env: demo
    spec:
      containers:
        - name: nginx
          image: nginx
          ports:
            - containerPort: 80
```

---

### 🧪 Problem YAML 2 (Mismatch in `selector.matchLabels` and `template.labels`)

```yaml
# ❌ Original - Labels do not match
selector:
  matchLabels:
    env: dev
template:
  metadata:
    labels:
      env: demo
```

✅ **Fix**: Ensure labels in `selector.matchLabels` and `template.metadata.labels` are identical.

### ✅ Fixed YAML:

```yaml
selector:
  matchLabels:
    env: demo
template:
  metadata:
    labels:
      env: demo
```

---

## 🧠 Share Your Learnings

1. Write a blog post summarizing this task.
2. Share it on social media (LinkedIn, Twitter/X).
3. Tag your mentors and community:
    - [@PiyushSachdeva](https://www.linkedin.com/in/piyush-sachdeva)
    - [@CloudOps Community](https://www.linkedin.com/company/thecloudopscomm)
4. Use the hashtag **#40DaysOfKubernetes**
5. Embed the lesson video to give visual support for readers.

---

## 📝 Blog Focus Suggestions

- Explain the difference between ReplicaSet and Deployment
- Show how rollouts work and when you'd roll back
- Include screenshots or `kubectl get` command outputs
- Help readers understand why the label matching in `selector` is critical

---

Happy learning! 🚀

---

# 🧪 Understanding `--dry-run=client` in Kubernetes

In Kubernetes, the `--dry-run=client` flag is used with `kubectl` commands to **preview resources without actually applying them to the cluster**. It helps validate and generate resource configurations locally.

---

### ✅ Key Aspects of `--dry-run=client`

- **Validation**  
  Checks the syntax and structure of your YAML or command to ensure it's formatted correctly.

- **Preview**  
  Displays what the object would look like if it were applied to the cluster.

- **No Changes Made**  
  Does not create, modify, or delete any resources on the cluster.

- **Local Execution**  
  Validation happens locally on your machine without interacting with the Kubernetes API server.

- **Syntax and Configuration Checks**  
  Useful for validating local files and configurations before applying them.

---

### 🎯 Benefits of Using `--dry-run=client`

- **🧠 Error Detection**  
  Catch mistakes early in your YAML or `kubectl` command syntax.

- **📝 Template Generation**  
  Quickly generate YAML templates by redirecting the output.

- **🛡️ Reduced Risk**  
  Prevents accidental misconfigurations from reaching the cluster.

---

### 📌 Example Command

```bash
kubectl apply -f my-pod.yaml --dry-run=client -o yaml > my-pod-preview.yaml
