# Lab 4 - Service

###References
* [Kubernetes Service](https://kubernetes.io/docs/concepts/services-networking/service/)

### Just deployment
* Create namespace
```
k[iid@iid Lab-3 Deployment]$ kubectl get ns
NAME              STATUS   AGE
default           Active   24h
kube-node-lease   Active   24h
kube-public       Active   24h
kube-system       Active   24h

[iid@iid Lab-3 Deployment]$ kubectl create ns pjaiswal
namespace/pjaiswal created

[iid@iid Lab-3 Deployment]$ kubectl get ns
NAME              STATUS   AGE
default           Active   24h
kube-node-lease   Active   24h
kube-public       Active   24h
kube-system       Active   24h
pjaiswal          Active   3s
``` 
* Create kubectl service
```
[iid@iid Lab-4 Service]$ kubectl create -f lab-04-svc-01.yaml 
service/lab-04-svc-01 created

[iid@iid Lab-4 Service]$ kubectl get deployment,replicaset,pod,service --show-labels
NAME                    TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)    AGE   LABELS
service/kubernetes      ClusterIP   10.96.0.1       <none>        443/TCP    24h   component=apiserver,provider=kubernetes
service/lab-04-svc-01   ClusterIP   10.109.96.132   <none>        8668/TCP   24s   <none>
```
* create service in its own namespace
```
[iid@iid Lab-4 Service]$ kubectl delete svc lab-04-svc-01
service "lab-04-svc-01" deleted

[iid@iid Lab-4 Service]$ kubectl create -f lab-04-svc-03.yaml 
service/lab-04-svc-03 created

[iid@iid Lab-4 Service]$ kubectl get deployment,replicaset,pod,service --show-labels
NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE   LABELS
service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   24h   component=apiserver,provider=kubernetes
[iid@iid Lab-4 Service]$ kubectl get deployment,replicaset,pod,service --show-labels --all-namespaces
NAMESPACE     NAME                      READY   UP-TO-DATE   AVAILABLE   AGE   LABELS
kube-system   deployment.apps/coredns   2/2     2            2           24h   k8s-app=kube-dns

NAMESPACE     NAME                                 DESIRED   CURRENT   READY   AGE   LABELS
kube-system   replicaset.apps/coredns-66bff467f8   2         2         2       24h   k8s-app=kube-dns,pod-template-hash=66bff467f8

NAMESPACE     NAME                                   READY   STATUS    RESTARTS   AGE   LABELS
kube-system   pod/coredns-66bff467f8-4779g           1/1     Running   1          24h   k8s-app=kube-dns,pod-template-hash=66bff467f8
kube-system   pod/coredns-66bff467f8-vtkjc           1/1     Running   1          24h   k8s-app=kube-dns,pod-template-hash=66bff467f8
kube-system   pod/etcd-minikube                      1/1     Running   1          24h   component=etcd,tier=control-plane
kube-system   pod/kube-apiserver-minikube            1/1     Running   1          24h   component=kube-apiserver,tier=control-plane
kube-system   pod/kube-controller-manager-minikube   1/1     Running   1          24h   component=kube-controller-manager,tier=control-plane
kube-system   pod/kube-proxy-hm77r                   1/1     Running   1          24h   controller-revision-hash=58b47d6fd4,k8s-app=kube-proxy,pod-template-generation=1
kube-system   pod/kube-scheduler-minikube            1/1     Running   1          24h   component=kube-scheduler,tier=control-plane
kube-system   pod/storage-provisioner                1/1     Running   2          24h   addonmanager.kubernetes.io/mode=Reconcile,integration-test=storage-provisioner

NAMESPACE     NAME                    TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)                  AGE   LABELS
default       service/kubernetes      ClusterIP   10.96.0.1        <none>        443/TCP                  24h   component=apiserver,provider=kubernetes
kube-system   service/kube-dns        ClusterIP   10.96.0.10       <none>        53/UDP,53/TCP,9153/TCP   24h   k8s-app=kube-dns,kubernetes.io/cluster-service=true,kubernetes.io/name=KubeDNS
pjaiswal      service/lab-04-svc-03   ClusterIP   10.105.204.213   <none>        8668/TCP                 29s   <none>

[iid@iid Lab-4 Service]$ kubectl get deployment,replicaset,pod,service --show-labels --all-namespaces
NAMESPACE     NAME                      READY   UP-TO-DATE   AVAILABLE   AGE   LABELS
kube-system   deployment.apps/coredns   2/2     2            2           24h   k8s-app=kube-dns

NAMESPACE     NAME                                 DESIRED   CURRENT   READY   AGE   LABELS
kube-system   replicaset.apps/coredns-66bff467f8   2         2         2       24h   k8s-app=kube-dns,pod-template-hash=66bff467f8

NAMESPACE     NAME                                   READY   STATUS    RESTARTS   AGE   LABELS
kube-system   pod/coredns-66bff467f8-4779g           1/1     Running   1          24h   k8s-app=kube-dns,pod-template-hash=66bff467f8
kube-system   pod/coredns-66bff467f8-vtkjc           1/1     Running   1          24h   k8s-app=kube-dns,pod-template-hash=66bff467f8
kube-system   pod/etcd-minikube                      1/1     Running   1          24h   component=etcd,tier=control-plane
kube-system   pod/kube-apiserver-minikube            1/1     Running   1          24h   component=kube-apiserver,tier=control-plane
kube-system   pod/kube-controller-manager-minikube   1/1     Running   1          24h   component=kube-controller-manager,tier=control-plane
kube-system   pod/kube-proxy-hm77r                   1/1     Running   1          24h   controller-revision-hash=58b47d6fd4,k8s-app=kube-proxy,pod-template-generation=1
kube-system   pod/kube-scheduler-minikube            1/1     Running   1          24h   component=kube-scheduler,tier=control-plane
kube-system   pod/storage-provisioner                1/1     Running   2          24h   addonmanager.kubernetes.io/mode=Reconcile,integration-test=storage-provisioner

NAMESPACE     NAME                    TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)                  AGE   LABELS
default       service/kubernetes      ClusterIP   10.96.0.1        <none>        443/TCP                  24h   component=apiserver,provider=kubernetes
kube-system   service/kube-dns        ClusterIP   10.96.0.10       <none>        53/UDP,53/TCP,9153/TCP   24h   k8s-app=kube-dns,kubernetes.io/cluster-service=true,kubernetes.io/name=KubeDNS
pjaiswal      service/lab-04-svc-03   ClusterIP   10.105.204.213   <none>        8668/TCP                 35s   <none>
```

###Publishing Services (ServiceTypes)
Kubernetes ```ServiceTypes``` allow you to specify what kind of Service you want. The default is ```ClusterIP```. \
```Type``` values and their behaviors are:
* ```ClusterIP```: Exposes the Service on a cluster-internal IP. Choosing this value makes the Service only reachable from within the cluster. This is the default ```ServiceType```.
* ```NodePort```: Exposes the Service on each Node's IP at a static port (the ```NodePort```). A ```ClusterIP``` Service, to which the ```NodePort``` Service routes, is automatically created. You'll be able to contact the ```NodePort``` Service, from outside the cluster, by requesting ```<NodeIP>:<NodePort>```.
* ```LoadBalancer```: Exposes the Service externally using a cloud provider's load balancer. ```NodePort``` and ```ClusterIP``` Services, to which the external load balancer routes, are automatically created.
* ```ExternalName```: Maps the Service to the contents of the ```externalName``` field (e.g. foo.bar.example.com), by returning a ```CNAME``` record 


### Scalling the application
There are cases where the pod crashes and application went down untill pod recovers. To overcome this, inbuilt property of Kubernetes, called replica sets, to solve this issue for deployments. 

To run a replica set for our deployment, run the below command to add additional replicas for our deployment, which effectively means ten pods running for a single deployment.
```
kubectl scale --replicas=10 deploy/lab-04-svc-dep-deployment
```
 



