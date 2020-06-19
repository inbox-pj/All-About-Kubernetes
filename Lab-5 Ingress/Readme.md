# Lab 5 - Ingress

An API object that manages external access to the services in a cluster, typically HTTP.

Ingress may provide load balancing, SSL termination and name-based virtual hosting.

Typically works as `API Gateway`.

### Enabling Ingress
Ingress need to enable as part of `minikube addons`

```
[iid@iid Lab-5 Ingress]$ minikube addons list
|-----------------------------|----------|--------------|
|         ADDON NAME          | PROFILE  |    STATUS    |
|-----------------------------|----------|--------------|
| ambassador                  | minikube | disabled     |
| dashboard                   | minikube | enabled ✅   |
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
| metrics-server              | minikube | enabled ✅   |
| nvidia-driver-installer     | minikube | disabled     |
| nvidia-gpu-device-plugin    | minikube | disabled     |
| olm                         | minikube | disabled     |
| registry                    | minikube | disabled     |
| registry-aliases            | minikube | disabled     |
| registry-creds              | minikube | disabled     |
| storage-provisioner         | minikube | enabled ✅   |
| storage-provisioner-gluster | minikube | disabled     |
|-----------------------------|----------|--------------|

[iid@iid Lab-5 Ingress]$ minikube addons enable ingress
  The 'ingress' addon is enabled
[iid@iid Lab-5 Ingress]$ minikube addons enable ingress-dns
  The 'ingress-dns' addon is enabled
```

### Additional Ingress command

* to get all the ingress: `kubectl get ingress <ingress_name>` applicable to `default` namespace

* to get all the ingress: `kubectl get ingress <ingress_name> --all-namespace` applicable to all namespace

* describe ingress `kubectl describe ingress <ingress_name>`

* to edit Ingress `kubectl edit ingress test`



--------------------------
### Create Ingress Routing

***Step 1 - Create Deployment***

To start, deploy an example HTTP server that will be the target of our requests. 
The deployment contains three deployments, one called webapp1 and a second called webapp2, and a third called webapp3 with a service for each.

Deploy the definitions with `kubectl create -f lab-05-ingress-05-01.yaml`

The status can be viewed with `kubectl get deployment`

***Step 2 - Deploy Ingress***

The YAML file `lab-05-ingress-05-02.yaml` defines a Nginx-based Ingress controller together with a service making it available on Port 80 to external connections using ExternalIPs. If the Kubernetes cluster was running on a cloud provider then it would use a LoadBalancer service type.
    
The `ServiceAccount` defines the account with a set of permissions on how to access the cluster to access the defined Ingress Rules. 

The default server secret is a self-signed certificate for other Nginx example SSL connections and is required by the Nginx Default Example.

The Ingress controllers are deployed in a familiar fashion to other Kubernetes objects with `kubectl create -f kubectl create -f lab-05-ingress-05-02.yaml`
    
The status can be identified using `kubectl get deployment -n nginx-ingress`
    
***Step 3 - Deploy Ingress Rules***

Ingress rules are an object type with Kubernetes. The rules can be based on a request host (domain), or the path of the request, 
or a combination of both. The rules apply to requests for the host host.clear2pay.com.
 
Two rules are defined based on the path request with a single catch all definition. Requests to the path /webapp1 are forwarded onto the service lab-05-ingress-05-web1-svc. Likewise, the requests to /webapp2 are forwarded to lab-05-ingress-05-web2-svc. If no rules apply, lab-05-ingress-05-web3-svc will be used.
   
As with all Kubernetes objects, they can be deployed via `kubectl create -f lab-05-ingress-05-03.yaml`
   
Once deployed, the status of all the Ingress rules can be discovered via `kubectl get ing`

***Step 4 - Test***

With the Ingress rules applied, the traffic will be routed to the defined place.
   
- `curl -H "Host: my.kubernetes.example" 172.17.0.12/webapp1`
- `curl -H "Host: my.kubernetes.example" 172.17.0.12/webapp2`
- `curl -H "Host: my.kubernetes.example" 172.17.0.12`
