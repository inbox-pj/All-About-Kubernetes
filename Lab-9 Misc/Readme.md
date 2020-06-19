# Lab 7 - Miscellaneous

## Managing Lables
###### Adding labels during build time
You can add labels to pods, services and deployments either at build time or at run time. If you're adding labels at build time, 
you can add a label section in the metadata portion as mentioned in lab-02-rs-01.yaml.

Now You can deploy the code above by using the command `kubectl create -f lab-02-rs-01.yml`

###### Viewing labels
We can add the `--show-labels` option to your kubectl get command as shown here: `kubectl get pods --show-labels`.


###### Adding labels to running pods
To add labels to a running pod, you can use the `kubectl label` command as follows: `kubectl label po/lab-09-misc-01-dev app=lab-09-misc-01-development`. 

This adds the label `development` with the value `lab-09-misc-01-dev` to the pod.

To update the value of a label, use the `--overwrite` flag in the command as follows: `kubectl label po/lab-09-misc-01-dev app=lab-09-misc-01-devevelopment --overwrite` 

###### Deleting a label
To remove an existing label, just add a `-` to the end of the label key as follows: `kubectl label po/lab-09-misc-01-dev app-`. This will remove the app label from the lab-09-misc-01-dev pod.

###### Searching by labels
* You can search for labels with the flag `--selector` (or `-l`). If you want to search for all the pods that are running in production, you can run `kubectl get pods --selector env=development` as shown below:

`kubectl get pods --selector env=development`

* Similarly, to get all pods by dev lead pjaiswal1, you'd add `dev-lead=pjaiswal1` to the selector as shown below.

`kubectl get pods --selector dev-lead=pjaiswal1`

* You can also do more complicated searches, like finding any pods owned by pjaiswal in the development tier, by the following query `dev-lead=pjaiswal,env=development`:

`kubectl get pods -l dev-lead=pjaiswal,env=development`

* Or, any apps not owned by pjaiswal in staging (using the ! construct):

`kubectl get pods -l dev-lead!=pjaiswal,env=sit`

* Querying also supports the `in` keyword

`kubectl get pods -l 'release-version in (1.0,2.0)'`

* Or a more complicated example:

`kubectl get pods -l "release-version in (1.0,2.0),team in (marketing)"`

* The opposite of "in" is "notin" as shown in this example:

`kubectl get pods -l 'release-version notin (1.0)'`

* Finally, sometimes label might not have a value assigned to it, but we can still search by label name.

`kubectl get pods -l 'release-version'`  

###### Extending the label concept to deployments/services

As a bonus, labels will also work with `kubectl get services/deployments/all --show-labels` and will return labels for your services, deployments or all objects.

`kubectl get all --show-labels`

And, you can delete pods, services or deployments by label as well! For example `kubectl delete pods -l dev-lead=pjaiswal` will delete all pods who's dev-lead was pjasiwal. 


## Kubernatis Dashboard
The Kubernetes Dashboard is a simple, web user interface for Kubernetes clusters. It allows users to manage and troubleshoot applications running in the cluster, as well as manage the cluster itself.
It comes as `addons` and by default it's disabled.

```
[iid@iid All-About-Kubernatis]$ minikube addons list
|-----------------------------|----------|--------------|
|         ADDON NAME          | PROFILE  |    STATUS    |
|-----------------------------|----------|--------------|
| ambassador                  | minikube | disabled     |
| dashboard                   | minikube | disabled     |
| default-storageclass        | minikube | enabled ✅   |
| efk                         | minikube | disabled     |
| freshpod                    | minikube | disabled     |
| gvisor                      | minikube | disabled     |
| helm-tiller                 | minikube | disabled     |
| ingress                     | minikube | disabled     |
| ingress-dns                 | minikube | disabled     |
| istio                       | minikube | disabled     |
| istio-provisioner           | minikube | disabled     |
| logviewer                   | minikube | disabled     |
| metallb                     | minikube | disabled     |
| metrics-server              | minikube | disabled     |
| nvidia-driver-installer     | minikube | disabled     |
| nvidia-gpu-device-plugin    | minikube | disabled     |
| olm                         | minikube | disabled     |
| registry                    | minikube | disabled     |
| registry-aliases            | minikube | disabled     |
| registry-creds              | minikube | disabled     |
| storage-provisioner         | minikube | enabled ✅   |
| storage-provisioner-gluster | minikube | disabled     |
|-----------------------------|----------|--------------|
```

Let's enable the `dashboard` by using below command:
```
[iid@iid All-About-Kubernatis]$ minikube addons enable dashboard
 The 'dashboard' addon is enabled
```
We will also enable the metrics server, to see cluster memory and CPU usage. This is enabled on minikube with `minikube addons enable metrics-server`
```
[iid@iid All-About-Kubernatis]$ minikube addons enable metrics-server
 The 'metrics-server' addon is enabled
```
To start up the dashboard, type `minikube dashboard`
```
[iid@iid All-About-Kubernatis]$ minikube dashboard
 Verifying dashboard health ...
 Launching proxy ...
 Verifying proxy health ...
 Opening http://127.0.0.1:34663/api/v1/namespaces/kubernetes-dashboard/services/http:kubernetes-dashboard:/proxy/ in your default browser...
```

This will bring up the dashboard in your browser.


------------
## Launch Single Node Kubernetes Cluster

***Step 1 - Cluster Info***

The cluster can be interacted with using the kubectl CLI. This is the main approach used for managing Kubernetes and the applications running on top of the cluster.

Details of the cluster and its health status can be discovered via `kubectl cluster-info`
```
[iid@iid Lab-9 Misc]$ kubectl cluster-info
Kubernetes master is running at https://192.168.99.104:8443
KubeDNS is running at https://192.168.99.104:8443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.
```

To view the nodes in the cluster using `kubectl get nodes`
```
[iid@iid Lab-9 Misc]$ kubectl get nodes
NAME       STATUS   ROLES    AGE   VERSION
minikube   Ready    master   4d    v1.18.3
``` 

***Step 2 - Deploy Containers***
With a running Kubernetes cluster, containers can now be deployed.

Using `kubectl run`, it allows containers to be deployed onto the cluster - `kubectl create deployment first-deployment --image=katacoda/docker-http-server`
```
[iid@iid Lab-9 Misc]$ kubectl create deployment first-deployment --image=katacoda/docker-http-server
deployment.apps/first-deployment created
```
The status of the `deployment` can be discovered via the running Pods - `kubectl get pods`

Once the container is running it can be exposed via different networking options, depending on requirements. One possible solution is `NodePort`, that provides a dynamic port to a container.

`kubectl expose deployment first-deployment --port=80 --type=NodePort`


***Step 4 - Dashboard***

Enable the dashboard using `Minikube` with the command `minikube addons enable dashboard`, if not enabled yet.

Make the Kubernetes Dashboard available by deploying the following YAML definition.
```
[iid@iid Lab-9 Misc]$ kubectl apply -f lab-09-misc-02.yaml 
namespace/kubernetes-dashboard configured
service/kubernetes-dashboard-katacoda created
```

The Kubernetes dashboard allows you to view your applications in a UI. In this deployment, the dashboard has been made available on port 30000 but may take a while to start.

To see the progress of the Dashboard starting, watch the Pods within the kube-system namespace using `kubectl get pods -n kubernetes-dashboard -w`

Once running, the URL to the dashboard is 

```
[iid@iid Lab-9 Misc]$ minikube service kubernetes-dashboard-katacoda -n kubernetes-dashboard
|----------------------|-------------------------------|-------------|-----------------------------|
|      NAMESPACE       |             NAME              | TARGET PORT |             URL             |
|----------------------|-------------------------------|-------------|-----------------------------|
| kubernetes-dashboard | kubernetes-dashboard-katacoda |          80 | http://192.168.99.104:30000 |
|----------------------|-------------------------------|-------------|-----------------------------|
  Opening service kubernetes-dashboard/kubernetes-dashboard-katacoda in default browser...
[iid@iid Lab-9 Misc]$ 
```
