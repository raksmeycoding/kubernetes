```bash
#To see the namespaces in your cluster
kubectl get namespace

#If you want to list all pods across all namespaces
kubectl get pods --all-namespaces

# will list pods in my-namespace
kubectl get pods --namespace=my-namespace

# get deployment in specific namespace
kubectl get deployment -n ns1


# describe or debug deployment in specific namespace
kubectl describe -n ns1 deployment deployment-ns1

# get services in a specific namespace
kubectl get svc -n ns1

# user to his command to test curl to other service in cluster
kubectl run curlbox --image=curlimages/curl:8.7.1 -it --restart=Never --rm -- sh
# or this one below 👇
kubectl run curlbox --image=alpine -it --restart=Never --rm -- sh
curl echo-service # to curl to other service in cluster 
wget -qO- http://echo-service # or this one


# to debug, using command below
kubectl describe pod  curlbox

```