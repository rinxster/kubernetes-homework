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
$minikube ssh
$docker container ls
```
```
rinx@kuber-lab01:~$ minikube ssh
                         _             _
            _         _ ( )           ( )
  ___ ___  (_)  ___  (_)| |/')  _   _ | |_      __
/' _ ` _ `\| |/' _ `\| || , <  ( ) ( )| '_`\  /'__`\
| ( ) ( ) || || ( ) || || |\`\ | (_) || |_) )(  ___/
(_) (_) (_)(_)(_) (_)(_)(_) (_)`\___/'(_,__/'`\____)

$ docker container ls
CONTAINER ID   IMAGE                    COMMAND                  CREATED          STATUS          PORTS     NAMES
8d73ac068604   nginx                    "/docker-entrypoint.…"   3 minutes ago    Up 3 minutes              k8s_web_web_default_917602a7-0246-406e-b161-3baa7ff3df5a_0
e802133e8cb5   k8s.gcr.io/pause:3.5     "/pause"                 3 minutes ago    Up 3 minutes              k8s_POD_web_default_917602a7-0246-406e-b161-3baa7ff3df5a_0
42bd462bd101   kubernetesui/dashboard   "/dashboard --insecu…"   10 minutes ago   Up 10 minutes             k8s_kubernetes-dashboard_kubernetes-dashboard-67484c44f6-w4qlh_kubernetes-dashboard_712c2961-34b3-44d2-b32c-98ec58d07e2f_0
cab509fb196b   48d79e554db6             "/metrics-sidecar"       10 minutes ago   Up 10 minutes             k8s_dashboard-metrics-scraper_dashboard-metrics-scraper-856586f554-vz22g_kubernetes-dashboard_8a785b20-5301-470e-92e9-ca622ecaa2e9_0
f1994a02a4cb   k8s.gcr.io/pause:3.5     "/pause"                 10 minutes ago   Up 10 minutes             k8s_POD_dashboard-metrics-scraper-856586f554-vz22g_kubernetes-dashboard_8a785b20-5301-470e-92e9-ca622ecaa2e9_0
5f9c4eb3c6d2   k8s.gcr.io/pause:3.5     "/pause"                 10 minutes ago   Up 10 minutes             k8s_POD_kubernetes-dashboard-67484c44f6-w4qlh_kubernetes-dashboard_712c2961-34b3-44d2-b32c-98ec58d07e2f_0
d44ccd3f06dd   6e38f40d628d             "/storage-provisioner"   14 minutes ago   Up 14 minutes             k8s_storage-provisioner_storage-provisioner_kube-system_661580b2-7328-44f8-95e6-14eb1799ca77_8
3e811e802d6a   f73640fb5061             "/metrics-server --c…"   14 minutes ago   Up 14 minutes             k8s_metrics-server_metrics-server-dbf765b9b-5sbdm_kube-system_b6fd7ca2-5ec0-44f7-9da5-b453b011d59d_5
c95aa2cf1240   8d147537fb7d             "/coredns -conf /etc…"   15 minutes ago   Up 15 minutes             k8s_coredns_coredns-78fcd69978-bbhld_kube-system_839c8d72-51fa-4fbc-b5f3-425c14d8fc3a_2
575f7f8e4cf2   6120bd723dce             "/usr/local/bin/kube…"   15 minutes ago   Up 15 minutes             k8s_kube-proxy_kube-proxy-gklq9_kube-system_61517f3f-3c04-4ad4-87e8-e911c5cb3d70_2
ee636aed469e   k8s.gcr.io/pause:3.5     "/pause"                 15 minutes ago   Up 15 minutes             k8s_POD_metrics-server-dbf765b9b-5sbdm_kube-system_b6fd7ca2-5ec0-44f7-9da5-b453b011d59d_2
a16fa77da2fd   k8s.gcr.io/pause:3.5     "/pause"                 15 minutes ago   Up 15 minutes             k8s_POD_kube-proxy-gklq9_kube-system_61517f3f-3c04-4ad4-87e8-e911c5cb3d70_2
1914f6119e8a   k8s.gcr.io/pause:3.5     "/pause"                 15 minutes ago   Up 15 minutes             k8s_POD_storage-provisioner_kube-system_661580b2-7328-44f8-95e6-14eb1799ca77_2
03d5f48946a8   k8s.gcr.io/pause:3.5     "/pause"                 15 minutes ago   Up 15 minutes             k8s_POD_coredns-78fcd69978-bbhld_kube-system_839c8d72-51fa-4fbc-b5f3-425c14d8fc3a_2
908c03eb9c94   0aa9c7e31d30             "kube-scheduler --au…"   16 minutes ago   Up 15 minutes             k8s_kube-scheduler_kube-scheduler-minikube_kube-system_eee9e2da42102bf0a05e1e7b00e318bf_2
1f9a45884ee2   004811815584             "etcd --advertise-cl…"   16 minutes ago   Up 15 minutes             k8s_etcd_etcd-minikube_kube-system_00dc33d4408782eee144d5292123d3a3_2
ccbb29396308   05c905cef780             "kube-controller-man…"   16 minutes ago   Up 15 minutes             k8s_kube-controller-manager_kube-controller-manager-minikube_kube-system_cf61f8185359bbfecb994d4d92683b56_2
e56cded0199d   53224b502ea4             "kube-apiserver --ad…"   16 minutes ago   Up 16 minutes             k8s_kube-apiserver_kube-apiserver-minikube_kube-system_3c194c47ee00479b5eb37aa600a476a4_2
7b69c947c8a6   k8s.gcr.io/pause:3.5     "/pause"                 16 minutes ago   Up 16 minutes             k8s_POD_kube-scheduler-minikube_kube-system_eee9e2da42102bf0a05e1e7b00e318bf_2
82ddbc1d1d8e   k8s.gcr.io/pause:3.5     "/pause"                 16 minutes ago   Up 16 minutes             k8s_POD_kube-controller-manager-minikube_kube-system_cf61f8185359bbfecb994d4d92683b56_2
c67ca87a88b9   k8s.gcr.io/pause:3.5     "/pause"                 16 minutes ago   Up 16 minutes             k8s_POD_kube-apiserver-minikube_kube-system_3c194c47ee00479b5eb37aa600a476a4_2
e8906c290a40   k8s.gcr.io/pause:3.5     "/pause"                 16 minutes ago   Up 16 minutes             k8s_POD_etcd-minikube_kube-system_00dc33d4408782eee144d5292123d3a3_2
$
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
