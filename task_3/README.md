# Task 3
### [Read more about CSI](https://habr.com/ru/company/flant/blog/424211/)
### Create pv in kubernetes
```bash
kubectl apply -f pv.yaml
```
```bash
rinx@kuber-lab01:~/education/task_3$ kubectl apply -f pv.yaml
persistentvolume/minio-deployment-pv created
```

### Check our pv
```bash
kubectl get pv
```
### my output
```bash
rinx@kuber-lab01:~/education/task_3$ kubectl get pv
NAME                  CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM   STORAGECLASS   REASON   AGE
minio-deployment-pv   5Gi        RWO            Retain           Available    
```
### Create pvc
```bash
kubectl apply -f pvc.yaml
```
```bash
rinx@kuber-lab01:~/education/task_3$ kubectl apply -f pvc.yaml
persistentvolumeclaim/minio-deployment-claim created
```
### Check our output in pv 
```bash
kubectl get pv
```
```bash
rinx@kuber-lab01:~/education/task_3$ kubectl get pv
NAME                  CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                            STORAGECLASS   REASON   AGE
minio-deployment-pv   5Gi        RWO            Retain           Bound    default/minio-deployment-claim                           2m41s
```

Output is change. PV get status bound.
### Check pvc
```bash
kubectl get pvc
```
```bash
rinx@kuber-lab01:~/education/task_3$ kubectl get pvc
NAME                     STATUS   VOLUME                CAPACITY   ACCESS MODES   STORAGECLASS   AGE
minio-deployment-claim   Bound    minio-deployment-pv   5Gi        RWO                           106s
```

### Apply deployment minio
```bash
kubectl apply -f deployment.yaml
```
### Apply svc nodeport
```bash
kubectl apply -f minio-nodeport.yaml
```
Open minikup_ip:node_port in you browser
### Apply statefulset
```bash
kubectl apply -f statefulset.yaml
```
### Check pod and statefulset
```bash
kubectl get pod
kubectl get sts
```

### Homework
* We published minio "outside" using nodePort. Do the same but using ingress.
* Publish minio via ingress so that minio by ip_minikube and nginx returning hostname (previous job) by path ip_minikube/web are available at the same time.
* Create deploy with emptyDir save data to mountPoint emptyDir, delete pods, check data.
* Optional. Raise an nfs share on a remote machine. Create a pv using this share, create a pvc for it, create a deployment. Save data to the share, delete the deployment, delete the pv/pvc, check that the data is safe.
