# Lab 6 - Config Map

### Create Config Map
To create a `configmap` for this literal type `kubectl create configmap lab-06-configmap --from-literal=LAB_SUBJECT=ConfigMap\nLAB_SECTION=Environment-Properties`

Another way to create `configmap` is from external file like below:
```
[iid@iid All-About-Kubernatis]$ echo -e "LAB_SUBJECT=ConfigMap\nLAB_SECTION=Environment-Properties" > Lab-6\ Config\ Map/env.properties
[iid@iid All-About-Kubernatis]$ 

[iid@iid All-About-Kubernatis]$ cat Lab-6\ Config\ Map/env.properties 
LAB_SUBJECT=ConfigMap
LAB_SECTION=Environment-Properties

[iid@iid Lab-6 Config Map]$ kubectl create configmap lab-06-configmap --from-env-file=env.properties
configmap/lab-06-configmap created
```

### List ConfigMap data
To see all your configmaps: `kubectl get configmaps`
```
[iid@iid Lab-6 Config Map]$ kubectl get configmap
NAME               DATA   AGE
lab-06-configmap   2      42s
```
To read the value in the logger configmap: `kubectl get configmap/logger -o yaml`
```
[iid@iid Lab-6 Config Map]$ kubectl get configmap/lab-06-configmap -o yaml
apiVersion: v1
data:
  LAB_SECTION: Environment-Properties
  LAB_SUBJECT: ConfigMap
kind: ConfigMap
metadata:
  creationTimestamp: "2020-06-18T08:26:02Z"
  managedFields:
  - apiVersion: v1
    fieldsType: FieldsV1
    fieldsV1:
      f:data:
        .: {}
        f:LAB_SECTION: {}
        f:LAB_SUBJECT: {}
    manager: kubectl
    operation: Update
    time: "2020-06-18T08:26:02Z"
  name: lab-06-configmap
  namespace: default
  resourceVersion: "123871"
  selfLink: /api/v1/namespaces/default/configmaps/lab-06-configmap
  uid: a906552e-0574-4fbf-9f75-c61c5f8b4696
```

To edit the value, we can run `kubectl edit configmap/lab-06-configmap` 

Now apply the `configmap` with compose file and see if the environment updated:
```
[iid@iid Lab-6 Config Map]$ kubectl create -f lab-06-configmap-dep-01.yaml 
deployment.apps/lab-06-configmap-01 created


[iid@iid Lab-6 Config Map]$ kubectl get pod
NAME                                   READY   STATUS    RESTARTS   AGE
lab-06-configmap-01-5cd7ddfb89-gx52w   1/1     Running   0          37s


[iid@iid Lab-6 Config Map]$ kubectl exec lab-06-configmap-01-5cd7ddfb89-gx52w -it -- bash
root@lab-06-configmap-01-5cd7ddfb89-gx52w:/# env
KUBERNETES_SERVICE_PORT_HTTPS=443
KUBERNETES_SERVICE_PORT=443
HOSTNAME=lab-06-configmap-01-5cd7ddfb89-gx52w
PWD=/
PKG_RELEASE=1~buster
HOME=/root
KUBERNETES_PORT_443_TCP=tcp://10.96.0.1:443
NJS_VERSION=0.4.1
TERM=xterm
LAB_SECTION=Environment-Properties
SHLVL=1
KUBERNETES_PORT_443_TCP_PROTO=tcp
KUBERNETES_PORT_443_TCP_ADDR=10.96.0.1
KUBERNETES_SERVICE_HOST=10.96.0.1
KUBERNETES_PORT=tcp://10.96.0.1:443
KUBERNETES_PORT_443_TCP_PORT=443
LAB_SUBJECT=ConfigMap
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
NGINX_VERSION=1.19.0
_=/usr/bin/env
```

### Volume Mount
Create a config map using enviroment file:
```
[iid@iid Lab-6 Config Map]$ echo -e "Hello Lab-06 ... <>br>This is begining of the end." > index.html

[iid@iid Lab-6 Config Map]$ cat index.html 
Hello Lab-06 ... <>br>This is begining of the end.

[iid@iid Lab-6 Config Map]$ kubectl create configmap lab-06-volume-configmap --from-file=index.html

configmap/lab-06-volume-configmap created

[iid@iid Lab-6 Config Map]$ kubectl get configmap
NAME                      DATA   AGE
lab-06-configmap          2      159m
lab-06-volume-configmap   1      32s
```

apply the compose file using configmap:
```
[iid@iid Lab-6 Config Map]$ kubectl get configmap lab-06-volume-configmap -o=yaml
apiVersion: v1
data:
  index.html: |
    Hello Lab-06 ... <>br>This is begining of the end.
kind: ConfigMap
metadata:
  creationTimestamp: "2020-06-18T11:04:35Z"
  managedFields:
  - apiVersion: v1
    fieldsType: FieldsV1
    fieldsV1:
      f:data:
        .: {}
        f:index.html: {}
    manager: kubectl
    operation: Update
    time: "2020-06-18T11:04:35Z"
  name: lab-06-volume-configmap
  namespace: default
  resourceVersion: "144471"
  selfLink: /api/v1/namespaces/default/configmaps/lab-06-volume-configmap
  uid: 36c097ea-2ba3-4c5a-8cf3-e0d138d23eeb
```

```
[iid@iid Lab-6 Config Map]$ kubectl apply -f lab-06-configmap-dep-03.yaml
deployment.apps/lab-06-configmap-dep-03 created
service/lab-06-configmap-svc-03 created
```

See if the volumes are mapped:
```
[iid@iid Lab-6 Config Map]$ kubectl get pod
NAME                                       READY   STATUS    RESTARTS   AGE
lab-06-configmap-dep-03-7bc557f6b9-gkvh6   1/1     Running   0          4m40s

[iid@iid Lab-6 Config Map]$ kubectl exec lab-06-configmap-dep-03-7bc557f6b9-gkvh6 -it -- bash
root@lab-06-configmap-dep-03-7bc557f6b9-gkvh6:/# cd /usr/share/nginx/html/

root@lab-06-configmap-dep-03-7bc557f6b9-gkvh6:/usr/share/nginx/html# ls                       
index.html
root@lab-06-configmap-dep-03-7bc557f6b9-gkvh6:/usr/share/nginx/html# cd /usr/share/nginx/html/
```
