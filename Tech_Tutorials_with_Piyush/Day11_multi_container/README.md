# Day 11/40 - Multi Container Pod Kubernetes - Sidecar vs Init Container

## Check out the video below for Day11 👇

[![Day11/40 - Multi Container Pod Kubernetes - Sidecar vs Init Container](https://img.youtube.com/vi/yRiFq1ykBxc/sddefault.jpgg)](https://youtu.be/yRiFq1ykBxc)

What is the use of init container?

The init containers option is available in Kubernetes environments used to run additional containers at startup that helps initialize an application. Once the init containers have completed their initialization tasks, they terminate but leave the application container(s) running.

## Sample YAML used in the demo

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
  labels:
    app.kubernetes.io/name: MyApp
spec:
  containers:
  - name: myapp-container
    image: busybox:1.28
    env:
    - name: FIRSTNAME
      value: "Piyush"
    command: ['sh', '-c', 'echo The app is running! && sleep 3600']
  initContainers:
  - name: init-myservice
    image: busybox:1.28
    command: ['sh', '-c'] #command to run
    args: ['until nslookup myservice.default.svc.cluster.local; do echo waiting for myservice; sleep 2; done']
  - name: init-mydb
    image: busybox:1.28
    command: ['sh', '-c']
    args: ['until nslookup mydb.default.svc.cluster.local; do echo waiting for mydb; sleep 2; done']
```


```yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
  labels:
    app.kubernetes.io/name: MyApp
spec:
  containers:
  - name: myapp-container
    image: busybox:1.28
    env:
    command: ['sh', '-c', 'echo The app is running! && sleep 3600']
  initContainers:
  - name: init-myservice
    image: busybox:1.28
    command: ['sh', '-c'] # command to run
    args: # arguments to the command
      - > # multi-line string
        until nslookup myservice.default.svc.cluster.local; do
         echo waiting for myservice;
         sleep 2;
        done;
```

Note: We cannot add or remove (update) initContainer once it is created. solution! we need to create new changes. 