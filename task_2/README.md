# Task 2

# Homework

* In Minikube in namespace kube-system, there are many different pods running. Your task is to figure out who creates them, and who makes sure they are running (restores them after deletion).

**rinx@kuber-lab01:~/education/task_2$ kubectl describe pods -n kube-system | grep "^Name:"**
```
Name:                 coredns-78fcd69978-bbhld
Name:                 etcd-minikube
Name:                 kube-apiserver-minikube
Name:                 kube-controller-manager-minikube
Name:                 kube-proxy-gklq9
Name:                 kube-scheduler-minikube
Name:                 metrics-server-dbf765b9b-5sbdm
Name:         storage-provisioner
```
**rinx@kuber-lab01:~/education/task_2$ kubectl describe pods -n kube-system | grep "Controlled By"**
```
Controlled By:  ReplicaSet/coredns-78fcd69978
Controlled By:  Node/minikube
Controlled By:  Node/minikube
Controlled By:  Node/minikube
Controlled By:  DaemonSet/kube-proxy
Controlled By:  Node/minikube
Controlled By:  ReplicaSet/metrics-server-dbf765b9b
rinx@kuber-lab01:~/education/task_2$
```


* Implement Canary deployment of an application via Ingress. Traffic to canary deployment should be redirected if you add "canary:always" in the header, otherwise it should go to regular deployment.
Set to redirect a percentage of traffic to canary deployment.

```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: ingress-web
  annotations:
    kubernetes.io/ingress.class: "nginx"
    ingress.nginx.kubernetes.io/canary: "true"
    ingress.nginx.kubernetes.io/canary-by-header: "canary:always"
    ingress.nginx.kubernetes.io/canary-weight: "15"
spec:
  rules:
  - http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
             name: web
             port: 
                number: 80
```
![Screenshot](https://user-images.githubusercontent.com/3485151/143106169-077e5dfe-2628-42cd-9aa9-4d8da9390d3e.JPG)
