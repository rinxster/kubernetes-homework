# Task 1.1

rinx@kuber-lab01:~/education/task_1$ kubectl version --client
```
Client Version: version.Info{Major:"1", Minor:"22", GitVersion:"v1.22.4", GitCommit:"b695d79d4f967c403a96986f1750a35eb75e75f1", GitTreeState:"clean", BuildDate:"2021-11-17T15:48:33Z", GoVersion:"go1.16.10", Compiler:"gc", Platform:"linux/amd64"}
```

rinx@kuber-lab01:~/education/task_1$ kubectl version --client
```
Client Version: version.Info{Major:"1", Minor:"22", GitVersion:"v1.22.4", GitCommit:"b695d79d4f967c403a96986f1750a35eb75e75f1", GitTreeState:"clean", BuildDate:"2021-11-17T15:48:33Z", GoVersion:"go1.16.10", Compiler:"gc", Platform:"linux/amd64"}
```

rinx@kuber-lab01:~/education/task_1$ minikube start --driver=virtualbox
```
* minikube v1.24.0 on Ubuntu 20.04 (kvm/amd64)
* Using the virtualbox driver based on existing profile
* Starting control plane node minikube in cluster minikube
* Updating the running virtualbox "minikube" VM ...
* Preparing Kubernetes v1.22.3 on Docker 20.10.8 ...
  - Using image kubernetesui/dashboard:v2.3.1
  - Using image kubernetesui/metrics-scraper:v1.0.7
  - Using image gcr.io/k8s-minikube/storage-provisioner:v5
* Verifying Kubernetes components...
* Enabled addons: storage-provisioner, default-storageclass, dashboard
* Done! kubectl is now configured to use "minikube" cluster and "default" namespace by default
```
rinx@kuber-lab01:~/education/task_1$ kubectl cluster-info
```Kubernetes control plane is running at https://192.168.59.101:8443
CoreDNS is running at https://192.168.59.101:8443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.
```
rinx@kuber-lab01:~/education/task_1$ kubectl get nodes
```
NAME       STATUS   ROLES                  AGE    VERSION
minikube   Ready    control-plane,master   147m   v1.22.3
```
rinx@kuber-lab01:~/education/task_1$ kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.3.1/aio/deploy/recommended.yaml
```
namespace/kubernetes-dashboard configured
serviceaccount/kubernetes-dashboard configured
service/kubernetes-dashboard configured
secret/kubernetes-dashboard-certs configured
secret/kubernetes-dashboard-csrf configured
secret/kubernetes-dashboard-key-holder configured
configmap/kubernetes-dashboard-settings configured
role.rbac.authorization.k8s.io/kubernetes-dashboard configured
clusterrole.rbac.authorization.k8s.io/kubernetes-dashboard configured
rolebinding.rbac.authorization.k8s.io/kubernetes-dashboard configured
deployment.apps/kubernetes-dashboard configured
service/dashboard-metrics-scraper configured
Warning: spec.template.metadata.annotations[seccomp.security.alpha.kubernetes.io/pod]: deprecated since v1.19; use the "seccompProfile" field instead
deployment.apps/dashboard-metrics-scraper configured
The ClusterRoleBinding "kubernetes-dashboard" is invalid: roleRef: Invalid value: rbac.RoleRef{APIGroup:"rbac.authorization.k8s.io", Kind:"ClusterRole", Name:"kubernetes-dashboard"}: cannot change roleRef
```

rinx@kuber-lab01:~$ kubectl get nodes
```
NAME       STATUS   ROLES                  AGE    VERSION
minikube   Ready    control-plane,master   173m   v1.22.3
```

Connected to Dashboard
kubectl proxy
In browser connect to http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/

![screenshot](https://user-images.githubusercontent.com/3485151/142850923-b90da0c9-8818-4e93-83de-d747816ac1ea.jpg)


# Task 1.2
# Create a deployment nginx. Set up two replicas. Remove one of the pods, see what happens.
# Task 1.2

# Kubernetes resources introduction
```bash
kubectl run web --image=nginx:latest
```
- take a look at created resource in cmd "kubectl get pods"
```
rinx@kuber-lab01:~$ k get pod
NAME   READY   STATUS    RESTARTS   AGE
web    1/1     Running   0          10s
```
- take a look at created resource in Dashboard
![Screenshot](https://user-images.githubusercontent.com/3485151/142851640-ede73b58-a0e1-4b6a-8893-137475089425.jpg)
```

```
- take a look at created resource in cmd
```bash
minikube ssh
docker container ls
```


## [Specification](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.18/)
```bash
kubectl explain pods.spec
```
Apply manifests (download from repository)
```bash
kubectl apply -f pod.yaml
kubectl apply -f rs.yaml
```
Look at pod
```bash
kubectl get pod
```
# You can create simple manifest from cmd
```bash
kubectl run web --image=nginx:latest --dry-run=client -o yaml
```
### Homework
* Create a deployment nginx. Set up two replicas. Remove one of the pods, see what happens.
