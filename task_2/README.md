# Task 2

### ConfigMap & Secrets

$ kubectl create secret generic connection-string --from-literal=DATABASE_URL=postgres://connect --dry-run=client -o yaml > secret.yaml
$ kubectl create configmap user --from-literal=firstname=firstname --from-literal=lastname=lastname --dry-run=client -o yaml > cm.yaml
$ kubectl apply -f secret.yaml
```bash
secret/connection-string configured
```
$ kubectl apply -f cm.yaml
```
configmap/user created
```
$ kubectl apply -f pod.yaml
```
pod/nginx created
```

## Check env in pod
```bash
$kubectl exec -it nginx -- bash
$printenv
```
```bash
rinx@kuber-lab01:~/education/task_2$ kubectl exec -it nginx -- bash
printenvroot@nginx:/# printenv
KUBERNETES_SERVICE_PORT_HTTPS=443
KUBERNETES_SERVICE_PORT=443
DATABASE_URL=postgres://connect
HOSTNAME=nginx
PWD=/
PKG_RELEASE=1~bullseye
HOME=/root
KUBERNETES_PORT_443_TCP=tcp://10.96.0.1:443
NJS_VERSION=0.7.0
TERM=xterm
SHLVL=1
KUBERNETES_PORT_443_TCP_PROTO=tcp
KUBERNETES_PORT_443_TCP_ADDR=10.96.0.1
lastname=lastname
KUBERNETES_SERVICE_HOST=10.96.0.1
KUBERNETES_PORT=tcp://10.96.0.1:443
KUBERNETES_PORT_443_TCP_PORT=443
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
firstname=firstname
NGINX_VERSION=1.21.4
_=/usr/bin/printenv
root@nginx:/#
```

### Create deployment with simple application
```bash
kubectl apply -f nginx-configmap.yaml
kubectl apply -f deployment.yaml
```
```bash
rinx@kuber-lab01:~/education/task_2$ kubectl apply -f nginx-configmap.yaml
configmap/nginx-configmap created
rinx@kuber-lab01:~/education/task_2$ kubectl apply -f deployment.yaml
deployment.apps/web created
```
### Get pod ip address
```bash
kubectl get pods -o wide
```
```bash
rinx@kuber-lab01:~/education/task_2$ kubectl get pods -o wide
NAME                   READY   STATUS    RESTARTS   AGE   IP           NODE       NOMINATED NODE   READINESS GATES
nginx                  1/1     Running   0          12m   172.17.0.3   minikube   <none>           <none>
web-5584c6c5c6-flgfw   1/1     Running   0          76s   172.17.0.4   minikube   <none>           <none>
web-5584c6c5c6-h68pp   1/1     Running   0          76s   172.17.0.5   minikube   <none>           <none>
web-5584c6c5c6-w2l5l   1/1     Running   0          76s   172.17.0.6   minikube   <none>           <none>
```
* Try connect to pod with curl (curl pod_ip_address). What happens?
* From you PC
* From minikube (minikube ssh)
* From another pod (kubectl exec -it $(kubectl get pod |awk '{print $1}'|grep web-|head -n1) bash)
### Create service (ClusterIP)
The command that can be used to create a manifest template
```bash
kubectl expose deployment/web --type=ClusterIP --dry-run=client -o yaml > service_template.yaml
```
Apply manifest
```bash
kubectl apply -f service_template.yaml
```
Get service CLUSTER-IP
```bash
kubectl get svc
```
### Sample output
```bash
NAME         TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.96.0.1        <none>        443/TCP   20h
web          ClusterIP   10.100.170.236   <none>        80/TCP    28s
```
* Try connect to service (curl service_ip_address). What happens?

* From you PC
* From minikube (minikube ssh) (run the command several times)
* From another pod (kubectl exec -it $(kubectl get pod |awk '{print $1}'|grep web-|head -n1) bash) (run the command several times)
### NodePort
```bash
kubectl apply -f service-nodeport.yaml
kubectl get service
```
### Sample output
```bash
NAME         TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
kubernetes   ClusterIP   10.96.0.1        <none>        443/TCP        20h
web          ClusterIP   10.100.170.236   <none>        80/TCP         15m
web-np       NodePort    10.101.147.109   <none>        80:30682/TCP   8s
```
Note how port is specified for a NodePort service
### Checking the availability of the NodePort service type
```bash
minikube ip
curl <minikube_ip>:<nodeport_port>
```
### Headless service
```bash
kubectl apply -f service-headless.yaml
```
### DNS
Connect to any pod
```bash
cat /etc/resolv.conf
```
Compare the IP address of the DNS server in the pod and the DNS service of the Kubernetes cluster.
* Compare headless and clusterip
Inside the pod run nslookup to normal clusterip and headless. Compare the results.
You will need to create pod with dnsutils.
### [Ingress](https://kubernetes.github.io/ingress-nginx/deploy/#minikube)
Enable Ingress controller
```bash
minikube addons enable ingress
```
Let's see what the ingress controller creates for us
```bash
kubectl get pods -n ingress-nginx
kubectl get pod $(kubectl get pod -n ingress-nginx|grep ingress-nginx-controller|awk '{print $1}') -n ingress-nginx -o yaml
```
Create Ingress
```bash
kubectl apply -f ingress.yaml
curl $(minikube ip)
```


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
