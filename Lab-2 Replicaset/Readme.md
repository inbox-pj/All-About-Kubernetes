# Lab 2 - REPLICA SET
### Set up your Replicaset

**Pod inside Replicaset**
* We create the ReplicaSet by giving the file to kubectl
```
[iid@iid Lab-2 Replicaset]$ kubectl create -f lab-02-rs-01.yaml 
replicaset.apps/lab-02-rs-01 created
```
* we can see the objects that were created
```
[iid@iid Lab-2 Replicaset]$ kubectl get pods,replicasets --show-labels
NAME                     READY   STATUS    RESTARTS   AGE   LABELS
pod/lab-02-rs-01-g8zqm   1/1     Running   0          49s   app=lab-02-rs-01
pod/lab-02-rs-01-p2znb   1/1     Running   0          49s   app=lab-02-rs-01

NAME                           DESIRED   CURRENT   READY   AGE   LABELS
replicaset.apps/lab-02-rs-01   2         2         2       49s   app=lab-02-rs-01
```
* delete a ReplicaSet is by killing a pod
```
[iid@iid Lab-2 Replicaset]$ kubectl delete pod/lab-02-rs-01-g8zqm
pod "lab-02-rs-01-g8zqm" deleted
``` 
* we can see the objects that were newly created, kubernates replaces the deleted pod with new one
```
[iid@iid Lab-2 Replicaset]$ kubectl get pods,replicasets --show-labels
NAME                     READY   STATUS    RESTARTS   AGE    LABELS
pod/lab-02-rs-01-p2znb   1/1     Running   0          102s   app=lab-02-rs-01
pod/lab-02-rs-01-rqjpb   1/1     Running   0          13s    app=lab-02-rs-01

NAME                           DESIRED   CURRENT   READY   AGE    LABELS
replicaset.apps/lab-02-rs-01   2         2         2       102s   app=lab-02-rs-01
``` 
* get the replicaset information
```
[iid@iid Lab-2 Replicaset]$ kubectl get rs
NAME           DESIRED   CURRENT   READY   AGE
lab-02-rs-01   2         2         2       2m29s
```
* get Pod information
```
[iid@iid Lab-2 Replicaset]$ kubectl get pods
NAME                 READY   STATUS    RESTARTS   AGE
lab-02-rs-01-p2znb   1/1     Running   0          2m40s
lab-02-rs-01-rqjpb   1/1     Running   0          71s
```
* grep a string inside kubernates objects
```
[iid@iid Lab-2 Replicaset]$ kubectl get all | grep lab
pod/lab-02-rs-01-p2znb   1/1     Running   0          2m53s
pod/lab-02-rs-01-rqjpb   1/1     Running   0          84s
replicaset.apps/lab-02-rs-01   2         2         2       2m53s
```


**Scaling**
* Let's scale up our pod up to ten replicas
```
kubectl scale scale rs lab-02-rs-01 --replicas=10
```
* watch kubectl objects in attached mode
```
watch kubectl get all
```
* let's scale down our pod upto 1 replica only
```
kubectl scale rs lab-02-rs-01 --replicas=1
```
* watch kubectl objects in attached mode
```
watch kubectl get all
```
* delete all replicaset
```
kubectl delete replicaset lab-02-rs-01
```
* delete the pod left behind
```
kubectl delete pod lab-02-rs-01
```