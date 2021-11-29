# Task 4
### Check what I can do
```bash
kubectl auth can-i create deployments --namespace kube-system
```
### output
```bash
yes
```
### Configure user authentication using x509 certificates
### Create private key
```bash
openssl genrsa -out k8s_user.key 2048
```
```bash
Generating RSA private key, 2048 bit long modulus (2 primes)
............+++++
...........................................................+++++
e is 65537 (0x010001)
```

### Create a certificate signing request
```bash
openssl req -new -key k8s_user.key \
-out k8s_user.csr \
-subj "/CN=k8s_user"
```
```bash
rinx@kuber-lab01:~/education/task_4$ openssl req -new -key k8s_user.key \
> -out k8s_user.csr \
> -subj "/CN=k8s_user"

```

### Sign the CSR in the Kubernetes CA. We have to use the CA certificate and the key, which are usually in /etc/kubernetes/pki. But since we use minikube, the certificates will be on the host machine in ~/.minikube
```bash
openssl x509 -req -in k8s_user.csr \
-CA ~/.minikube/ca.crt \
-CAkey ~/.minikube/ca.key \
-CAcreateserial \
-out k8s_user.crt -days 500
```
```bash
rinx@kuber-lab01:~/education/task_4$ openssl x509 -req -in k8s_user.csr \
> -CA ~/.minikube/ca.crt \
> -CAkey ~/.minikube/ca.key \
> -CAcreateserial \
> -out k8s_user.crt -days 500
Signature ok
subject=CN = k8s_user
Getting CA Private Key
```


### Create user in kubernetes
```bash
kubectl config set-credentials k8s_user \
--client-certificate=k8s_user.crt \
--client-key=k8s_user.key
```
```bash
rinx@kuber-lab01:~/education/task_4$ kubectl config set-credentials k8s_user \
> --client-certificate=k8s_user.crt \
> --client-key=k8s_user.key
User "k8s_user" set.
```

### Set context for user
```bash
kubectl config set-context k8s_user \
--cluster=minikube --user=k8s_user
```
```bash
rinx@kuber-lab01:~/education/task_4$ kubectl config set-context k8s_user \
> --cluster=minikube --user=k8s_user
Context "k8s_user" created.
```

### Edit ~/.kube/config
```bash
Change path
- name: k8s_user
  user:
    client-certificate: C:\Users\Andrey_Trusikhin\educ\k8s_user.crt
    client-key: C:\Users\Andrey_Trusikhin\educ\k8s_user.key
contexts:
- context:
    cluster: minikube
    user: k8s_user
  name: k8s_user
```
```
rinx@kuber-lab01:~/education/task_4$ ls
README.md  binding.yaml  k8s_user.crt  k8s_user.csr  k8s_user.key
```

### Switch to use new context
```bash
kubectl config use-context k8s_user
```

```bash
rinx@kuber-lab01:~/education/task_4$ kubectl config use-context k8s_user
Switched to context "k8s_user".
```

### Check privileges
```bash
kubectl get node
kubectl get pod
```
### my output
```bash
rinx@kuber-lab01:~/education/task_4$ kubectl get node
Error from server (Forbidden): nodes is forbidden: User "k8s_user" cannot list resource "nodes" in API group "" at the cluster scope
rinx@kuber-lab01:~/education/task_4$ kubectl get pod
Error from server (Forbidden): pods is forbidden: User "k8s_user" cannot list resource "pods" in API group "" in the namespace "default"
```
### Switch to default(admin) context
```bash
kubectl config use-context minikube
```
```bash
rinx@kuber-lab01:~/education/task_4$ kubectl config use-context minikube
Switched to context "minikube".
```

### Bind role and clusterrole to the user
```bash
kubectl apply -f binding.yaml
```
```bash
rinx@kuber-lab01:~/education/task_4$ kubectl apply -f binding.yaml
rolebinding.rbac.authorization.k8s.io/k8s_user created
```
### Check output
```bash
kubectl get pod
```
Now we can see pods
```bash
rinx@kuber-lab01:~/education/task_4$ kubectl get pod
NAME                     READY   STATUS    RESTARTS   AGE
minio-575d987896-97mps   1/1     Running   0          7h20m
minio-state-0            1/1     Running   0          7h19m
web-7b84565f5d-z44x5     1/1     Running   0          42m
```


### Homework
* Create users deploy_view and deploy_edit. Give the user deploy_view rights only to view deployments, pods. Give the user deploy_edit full rights to the objects deployments, pods.
* Create namespace prod. Create users prod_admin, prod_view. Give the user prod_admin admin rights on ns prod, give the user prod_view only view rights on namespace prod.
* Create a serviceAccount sa-namespace-admin. Grant full rights to namespace default. Create context, authorize using the created sa, check accesses.
