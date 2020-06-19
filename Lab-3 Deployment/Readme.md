# Lab 3 - DEPLOYMENT

###Simple Deployment
* We can then use ```kubectl apply``` to instruct Kubernetes to create our deployment. We use ```kubectl apply``` instead of ```kubectl create``` because 
apply will track metadata in your deployment. This metadata is useful if you manually make changes to your deployment (without running ```kubectl apply```). 
In those scenarios, ```kubectl apply``` will only make changes to the configurations explicitly defined in the .yml file and will not overwrite your manual changes.
```
[iid@iid Lab-3 Deployment]$ kubectl apply -f lab-03-deploy-01.yaml 
deployment.apps/lab-03-deploy-01 created
```
* Creating the Deployment created a ReplicaSet and five pods just like our ReplicaSet example in previous lab. 
The difference is that we now have a Deployment object that we can interact with.
```
[iid@iid Lab-3 Deployment]$ kubectl get deployment,replicaset,pod --show-labels
NAME                               READY   UP-TO-DATE   AVAILABLE   AGE   LABELS
deployment.apps/lab-03-deploy-01   5/5     5            5           49s   app=lab-03-deploy-01

NAME                                          DESIRED   CURRENT   READY   AGE   LABELS
replicaset.apps/lab-03-deploy-01-7fcdbcfcb6   5         5         5       49s   app=lab-03-deploy-01,pod-template-hash=7fcdbcfcb6

NAME                                    READY   STATUS    RESTARTS   AGE   LABELS
pod/lab-03-deploy-01-7fcdbcfcb6-5b2mr   1/1     Running   0          49s   app=lab-03-deploy-01,pod-template-hash=7fcdbcfcb6
pod/lab-03-deploy-01-7fcdbcfcb6-5gdgj   1/1     Running   0          49s   app=lab-03-deploy-01,pod-template-hash=7fcdbcfcb6
pod/lab-03-deploy-01-7fcdbcfcb6-hnrj5   1/1     Running   0          49s   app=lab-03-deploy-01,pod-template-hash=7fcdbcfcb6
pod/lab-03-deploy-01-7fcdbcfcb6-mpsfh   1/1     Running   0          49s   app=lab-03-deploy-01,pod-template-hash=7fcdbcfcb6
pod/lab-03-deploy-01-7fcdbcfcb6-wpxwg   1/1     Running   0          49s   app=lab-03-deploy-01,pod-template-hash=7fcdbcfcb6
```
* Self healing pod
```
id Lab-3 Deployment]$ kubectl delete pod lab-03-deploy-01-7fcdbcfcb6-5b2mr
pod "lab-03-deploy-01-7fcdbcfcb6-5b2mr" deleted

[iid@iid Lab-3 Deployment]$ kubectl get deployment,replicaset,pod --show-labels
NAME                               READY   UP-TO-DATE   AVAILABLE   AGE     LABELS
deployment.apps/lab-03-deploy-01   5/5     5            5           4m41s   app=lab-03-deploy-01

NAME                                          DESIRED   CURRENT   READY   AGE     LABELS
replicaset.apps/lab-03-deploy-01-7fcdbcfcb6   5         5         5       4m41s   app=lab-03-deploy-01,pod-template-hash=7fcdbcfcb6

NAME                                    READY   STATUS    RESTARTS   AGE     LABELS
pod/lab-03-deploy-01-7fcdbcfcb6-5gdgj   1/1     Running   0          4m41s   app=lab-03-deploy-01,pod-template-hash=7fcdbcfcb6
pod/lab-03-deploy-01-7fcdbcfcb6-hnrj5   1/1     Running   0          4m41s   app=lab-03-deploy-01,pod-template-hash=7fcdbcfcb6
pod/lab-03-deploy-01-7fcdbcfcb6-mpsfh   1/1     Running   0          4m41s   app=lab-03-deploy-01,pod-template-hash=7fcdbcfcb6
pod/lab-03-deploy-01-7fcdbcfcb6-rjpz5   1/1     Running   0          13s     app=lab-03-deploy-01,pod-template-hash=7fcdbcfcb6
pod/lab-03-deploy-01-7fcdbcfcb6-wpxwg   1/1     Running   0          4m41s   app=lab-03-deploy-01,pod-template-hash=7fcdbcfcb6
``` 
* deleting the deployment will delete the entire pod and replicaset
```
iid Lab-3 Deployment]$ kubectl delete deployment lab-03-deploy-01
deployment.apps "lab-03-deploy-01" deleted

[iid@iid Lab-3 Deployment]$ kubectl get deployment,replicaset,pod --show-labels
NAME                                    READY   STATUS        RESTARTS   AGE     LABELS
pod/lab-03-deploy-01-7fcdbcfcb6-5gdgj   0/1     Terminating   0          7m4s    app=lab-03-deploy-01,pod-template-hash=7fcdbcfcb6
pod/lab-03-deploy-01-7fcdbcfcb6-hnrj5   0/1     Terminating   0          7m4s    app=lab-03-deploy-01,pod-template-hash=7fcdbcfcb6
pod/lab-03-deploy-01-7fcdbcfcb6-mpsfh   0/1     Terminating   0          7m4s    app=lab-03-deploy-01,pod-template-hash=7fcdbcfcb6
pod/lab-03-deploy-01-7fcdbcfcb6-rjpz5   0/1     Terminating   0          2m36s   app=lab-03-deploy-01,pod-template-hash=7fcdbcfcb6
pod/lab-03-deploy-01-7fcdbcfcb6-wpxwg   0/1     Terminating   0          7m4s    app=lab-03-deploy-01,pod-template-hash=7fcdbcfcb6

[iid@iid Lab-3 Deployment]$ kubectl get deployment,replicaset,pod --show-labels
No resources found in default namespace.

[iid@iid Lab-3 Deployment]$ kubectl get all
NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   20h
```
* Deployments give us the ability to track the rollout of changes to our pods. Let’s apply the template update and watch the status.

```
kubectl apply edit-last-applied -f lab-03-deploy-01.yaml            // you can edit the last applied configuration using terminal as well

[iid@iid Lab-3 Deployment]$ kubectl apply -f lab-03-deploy-01.1.yaml 
deployment.apps/lab-03-deploy-01 configured

[iid@iid Lab-3 Deployment]$ kubectl rollout status deployment lab-03-deploy-01
Waiting for deployment "lab-03-deploy-01" rollout to finish: 5 of 10 updated replicas are available...
Waiting for deployment "lab-03-deploy-01" rollout to finish: 6 of 10 updated replicas are available...
Waiting for deployment "lab-03-deploy-01" rollout to finish: 7 of 10 updated replicas are available...
Waiting for deployment "lab-03-deploy-01" rollout to finish: 8 of 10 updated replicas are available...
Waiting for deployment "lab-03-deploy-01" rollout to finish: 9 of 10 updated replicas are available...
deployment "lab-03-deploy-01" successfully rolled out

iid@iid Lab-3 Deployment]$ kubectl get deployment,replicaset,pod --show-labels
NAME                               READY   UP-TO-DATE   AVAILABLE   AGE   LABELS
deployment.apps/lab-03-deploy-01   10/10   10           10          13m   app=lab-03-deploy-01

NAME                                          DESIRED   CURRENT   READY   AGE   LABELS
replicaset.apps/lab-03-deploy-01-7fcdbcfcb6   10        10        10      13m   app=lab-03-deploy-01,pod-template-hash=7fcdbcfcb6

NAME                                    READY   STATUS    RESTARTS   AGE   LABELS
pod/lab-03-deploy-01-7fcdbcfcb6-26q2s   1/1     Running   0          81s   app=lab-03-deploy-01,pod-template-hash=7fcdbcfcb6
pod/lab-03-deploy-01-7fcdbcfcb6-6qlcd   1/1     Running   0          13m   app=lab-03-deploy-01,pod-template-hash=7fcdbcfcb6
pod/lab-03-deploy-01-7fcdbcfcb6-8lltz   1/1     Running   0          13m   app=lab-03-deploy-01,pod-template-hash=7fcdbcfcb6
pod/lab-03-deploy-01-7fcdbcfcb6-8mkz9   1/1     Running   0          13m   app=lab-03-deploy-01,pod-template-hash=7fcdbcfcb6
pod/lab-03-deploy-01-7fcdbcfcb6-b2blm   1/1     Running   0          81s   app=lab-03-deploy-01,pod-template-hash=7fcdbcfcb6
pod/lab-03-deploy-01-7fcdbcfcb6-br45l   1/1     Running   0          13m   app=lab-03-deploy-01,pod-template-hash=7fcdbcfcb6
pod/lab-03-deploy-01-7fcdbcfcb6-lkrgj   1/1     Running   0          81s   app=lab-03-deploy-01,pod-template-hash=7fcdbcfcb6
pod/lab-03-deploy-01-7fcdbcfcb6-vgxgd   1/1     Running   0          13m   app=lab-03-deploy-01,pod-template-hash=7fcdbcfcb6
pod/lab-03-deploy-01-7fcdbcfcb6-z2dzd   1/1     Running   0          81s   app=lab-03-deploy-01,pod-template-hash=7fcdbcfcb6
pod/lab-03-deploy-01-7fcdbcfcb6-zkdkc   1/1     Running   0          81s   app=lab-03-deploy-01,pod-template-hash=7fcdbcfcb6

[iid@iid Lab-3 Deployment]$ kubectl rollout history deployment/lab-03-deploy-01 --revision 1
deployment.apps/lab-03-deploy-01 with revision #1
Pod Template:
  Labels:	app=lab-03-deploy-01
	pod-template-hash=7fcdbcfcb6
  Containers:
   lab-03-deploy-01:
    Image:	nginx
    Port:	<none>
    Host Port:	<none>
    Environment:	<none>
    Mounts:	<none>
  Volumes:	<none>
```
* you can undo the rollout changes to previous revision as well:
```
kubectl rollout undo deployment/lab-03-deploy-01 --to-revision=1
```
* print the deployment details in desired format:
```
kubectl get deployment lab-03-deploy-01 -o yaml
``` 
* expose deployment as a service:
```
[iid@iid Lab-3 Deployment]$ kubectl expose deployment lab-03-deploy-01 --type=NodePort --port 80
service/lab-03-deploy-01 exposed

[iid@iid Lab-3 Deployment]$ kubectl get service --show-labels
NAME               TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE   LABELS
kubernetes         ClusterIP   10.96.0.1       <none>        443/TCP        21h   component=apiserver,provider=kubernetes
lab-03-deploy-01   NodePort    10.104.55.199   <none>        80:32505/TCP   90s   app=lab-03-deploy-01
```
* print the service detils in yaml format:
```
kubectl get service/lab-03-deploy-01 -o yaml

```

###Handling application upgrades
* added the `--record` to this because we want to record our rollout history
```
[iid@iid Lab-3 Deployment]$ kubectl apply -f lab-03-deploy-01.yaml --record 
deployment.apps/lab-03-deploy-01 created
```
* To update the Image:
To update the image, I run this command: `kubectl set image deployment/lab-03-deploy-01 lab-03-deploy-01=[new-image-version]`.

* we can check the  rollout history by typing `kubectl rollout history deployment/lab-03-deploy-01`.

* To rollback the deployment, we use the rollout undo command `kubectl rollout undo deployment/lab-03-deploy-01`.
Note:  to rollback to a specific version. add a `--to-revision=version` to the specific version.


###Rolling Update Strategy
As given examples above, here is another use of Rolling update:
```
    kubectl apply -f lab-03-deploy-02.yaml
    kubectl get pods
    kubectl get deploy
    kubectl edit deployment/lab-03-deploy-02-dep
    kubectl set image deployment/lab-03-deploy-02-dep lab-03-deploy-02-cont=nginx:1.9.1
    watch kubectl get pods
    kubectl describe pod lab-03-deploy-02-dep-7fd95bc774-t255d
    watch kubectl get pods
    kubectl delete deploy lab-03-deploy-02-dep
    kubectl get all | grep lab
```


### Setting up a load balancer
You'll notice that in the `kubectl get all` command, the service has a port mapping defined; however, when you try to hit that port via your web browser, you won't be able to reach the service.

This is because by default, the pod is only accessible by its internal IP address within the cluster. To make the container accessible from outside the Kubernetes virtual network, you have to expose the pod as a Kubernetes service.

To do this, we can expose the pod to the public internet using the kubectl expose command 
`kubectl expose deployment lab-03-deploy-01 --type=NodePort`

The `--type=NodePort` flag exposes the deployment outside of the cluster. If you're using this on a cloud provider, you can use a `--type=LoadBalancer` that will provision an external IP address would be provisioned to access the service.

To view the final user interface, use the minikube service command.

`minikube service lab-03-deploy-01`

This will open your web browser to your application that is running in Kubernetes!


