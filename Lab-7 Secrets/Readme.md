# LAB-7 Secrets

```

[iid@iid Lab-7 Secrets]$ kubectl create secret --help
Create a secret using specified subcommand.

Available Commands:
  docker-registry Create a secret for use with a Docker registry
  generic         Create a secret from a local file, directory or literal value
  tls             Create a TLS secret

Usage:
  kubectl create secret [flags] [options]

```

### Declare a secret
Just like configuration data, applications might also require other data that might be of more sensitive in nature- for example database passwords, or API tokens. 
Passing these in the yaml for a deployment or pod would make them visible to everyone.

To create a secret: 

```
[iid@iid Lab-7 Secrets]$ kubectl create secret generic apikey --from-literal=api_key=12345
secret/apikey created

[iid@iid Lab-7 Secrets]$ kubectl get secret
NAME                  TYPE                                  DATA   AGE
apikey                Opaque                                1      9s
default-token-llrxl   kubernetes.io/service-account-token   3      3d1h

[iid@iid Lab-7 Secrets]$ kubectl get secret apikey -o=yaml
apiVersion: v1
data:
  api_key: MTIzNDU=
kind: Secret
metadata:
  creationTimestamp: "2020-06-18T11:56:00Z"
  managedFields:
  - apiVersion: v1
    fieldsType: FieldsV1
    fieldsV1:
      f:data:
        .: {}
        f:api_key: {}
      f:type: {}
    manager: kubectl
    operation: Update
    time: "2020-06-18T11:56:00Z"
  name: apikey
  namespace: default
  resourceVersion: "151241"
  selfLink: /api/v1/namespaces/default/secrets/apikey
  uid: b4a52a71-ca73-4ae7-bcb3-c5fc31b512cd
type: Opaque
```
As shown in above example, the api_key value is being encrypted in Base64 encoding.

### add a secret to a deployment

Adding a secret to a deployment is similar to what we did for ```configmap```. You can add a secret to the env portion, and start up the deployment with:
```
[iid@iid Lab-7 Secrets]$ kubectl create -f lab-07-secret-01.yaml 
deployment.apps/lab-07-secret-01 created

[iid@iid Lab-7 Secrets]$ kubectl exec lab-07-secret-01-6cb4757645-h44gj -it -- bash

root@lab-07-secret-01-6cb4757645-h44gj:/# env
KUBERNETES_SERVICE_PORT_HTTPS=443
KUBERNETES_SERVICE_PORT=443
HOSTNAME=lab-07-secret-01-6cb4757645-h44gj
api_key=12345
PWD=/
PKG_RELEASE=1~buster
HOME=/root
KUBERNETES_PORT_443_TCP=tcp://10.96.0.1:443
NJS_VERSION=0.4.1
TERM=xterm
SHLVL=1
KUBERNETES_PORT_443_TCP_PROTO=tcp
KUBERNETES_PORT_443_TCP_ADDR=10.96.0.1
KUBERNETES_SERVICE_HOST=10.96.0.1
KUBERNETES_PORT=tcp://10.96.0.1:443
KUBERNETES_PORT_443_TCP_PORT=443
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
NGINX_VERSION=1.19.0
_=/usr/bin/env

```
