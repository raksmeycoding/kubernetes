# The Command Used in This Section 
```bash
# To crate pod
kubectl create -f .\pod.yaml

# To check pod is created or not
kubectl get pod

# To debug that pod 
kubectl describe pod myapp

# To check some logs in pod/myapp
kubectl logs pod/myapp

# To check some logs in initContainer
kubectl logs pod/myapp -c init-myservice

# To see list of env in that pod 
kubectl exec -it myapp -- printenv

# To use some command in that pod and print env/value
kubectl exec -it myapp -- sh 
echo $FIRSTNAME


# To update changes of pod
# If pod is created with initContainer, we cannot update, add, or remove (apply, or update) initContainer, so we need to create new changes
kubectl apply -f pod.yaml # to udate changes 

```

---

```bash
# Declarative command to create K8s Objects
kubectl create deploy nginx-deploy --image nignx --port 80

# Create redis deployment using declarative style
kubectl create deploy mydb --image redis --port 80

# Get deploy to check whether is created or not
kubectl get deploy

# Create service to expose nginx using declarative style
 kubectl expose deploy nginx-deploy --name myservice --port 80
 kubectl expose deploy mydb --name mydb --port 80
```