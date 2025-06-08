# Different Ways of Creating a Kubernetes Object

Kubernetes objects like Pods can be created in two main ways:

1. **Imperative way** – by directly issuing commands
2. **Declarative way** – by writing and applying manifest files (YAML)

---

## 1. Imperative Way (Through Commands or API Calls)

The **imperative approach** allows you to create and manage Kubernetes resources quickly using `kubectl` commands.

### 1.1 Create a Pod Imperatively

```bash
kubectl run nginx --image=nginx
```

> This command creates a Pod named `nginx` using the official NGINX image.

### 1.2 Using `--dry-run`

To simulate the creation without actually deploying the Pod:

```bash
kubectl run nginx --image=nginx --dry-run=client
```

This is useful for generating YAML files or testing commands safely.

### 1.3 Generate YAML Output from the Command

View the YAML definition that would be created:

```bash
kubectl run nginx --image=nginx --dry-run=client -o yaml
```

To save this output to a file:

```bash
kubectl run nginx --image=nginx --dry-run=client -o yaml > new-pod.yaml
```

Apply the generated YAML (optional step):

```bash
kubectl apply -f new-pod.yaml
```

### 1.4 Verify the Pod

After running the command, check the Pod status:

```bash
kubectl get pods
```

---

## 2. Declarative Way (Using Manifest Files)

The **declarative approach** involves writing YAML manifest files that describe the desired state of Kubernetes resources. These files can be versioned and reused across environments.

![Declarative Approach](./img.png)

### Example: Pod Manifest File

Below is a sample YAML manifest used in this lesson:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
  labels:
    env: demo
    type: frontend
spec:
  containers:
    - name: nginx-container
      image: nginx
      ports:
        - containerPort: 80
```

Save this content as `pod.yaml`, then apply it with:

```bash
kubectl apply -f pod.yaml
```

### Check Pod Status

After applying:

```bash
kubectl get pods
```

---

## Summary

| Method        | Description                               | Example Command / File                        |
|---------------|-------------------------------------------|------------------------------------------------|
| Imperative    | Directly create resources via `kubectl`   | `kubectl run nginx --image=nginx`             |
| Dry Run       | Preview YAML output without applying      | `kubectl run nginx --image=nginx --dry-run=client -o yaml` |
| Declarative   | Define YAML manifest and apply            | `kubectl apply -f pod.yaml`                   |

---

## Best Practices

- Use **imperative** commands for quick testing and learning.
- Use **declarative** manifests for production and CI/CD pipelines.
- Always **validate YAML** with `kubectl apply --dry-run=client -f yourfile.yaml` before applying.
- Prefer **version-controlled YAML files** for team collaboration and reproducibility.
