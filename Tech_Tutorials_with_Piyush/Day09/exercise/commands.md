```bash

kubectl apply -f service-clusterip.yaml

kubectl get pods --show-labels

# run and keep that pod
kubectl run busybox --image=busybox --restart=Never -it -- wget -O- myapp

kubectl run testbox --rm -it --image=busybox --restart=Never -- sh
# Inside:
nslookup myapp
wget -O- myapp

# --rm to auto-delete after exit
kubectl run busybox --rm -it --image=busybox --restart=Never -- wget -O- myapp

```


```bash
kubectl get pods -n kube-system -l k8s-app=kube-dns
```