# PI4 Stories

## Raspberry Pi 4 cluster Series - Installing cert-manager on the k3s cluster

As certificates are crucial in a kuberbetes cluster one of the first pods that one should install is [cert-manager](https://cert-manager.io/docs/installation/).

### Installling cert-manager

Installaion is extremelt easy with the following command:
```bash
kubectl apply --validate=false -f https://github.com/jetstack/cert-manager/releases/download/v1.11.0/cert-manager.yaml
```
In the time of writing this article the current version was v1.0.4 - you can change that to the [latest release](https://github.com/jetstack/cert-manager/releases) available of course. Here follows an example of the instalaltion of cert-manager:

```bash
gdha@n1:~$ kubectl apply --validate=false -f https://github.com/jetstack/cert-manager/releases/download/v1.11.0/cert-manager.yaml
namespace/cert-manager created
customresourcedefinition.apiextensions.k8s.io/clusterissuers.cert-manager.io created
customresourcedefinition.apiextensions.k8s.io/challenges.acme.cert-manager.io created
customresourcedefinition.apiextensions.k8s.io/certificaterequests.cert-manager.io created
customresourcedefinition.apiextensions.k8s.io/issuers.cert-manager.io created
customresourcedefinition.apiextensions.k8s.io/certificates.cert-manager.io created
customresourcedefinition.apiextensions.k8s.io/orders.acme.cert-manager.io created
serviceaccount/cert-manager-cainjector created
serviceaccount/cert-manager created
serviceaccount/cert-manager-webhook created
configmap/cert-manager-webhook created
clusterrole.rbac.authorization.k8s.io/cert-manager-cainjector created
clusterrole.rbac.authorization.k8s.io/cert-manager-controller-issuers created
clusterrole.rbac.authorization.k8s.io/cert-manager-controller-clusterissuers created
clusterrole.rbac.authorization.k8s.io/cert-manager-controller-certificates created
clusterrole.rbac.authorization.k8s.io/cert-manager-controller-orders created
clusterrole.rbac.authorization.k8s.io/cert-manager-controller-challenges created
clusterrole.rbac.authorization.k8s.io/cert-manager-controller-ingress-shim created
clusterrole.rbac.authorization.k8s.io/cert-manager-view created
clusterrole.rbac.authorization.k8s.io/cert-manager-edit created
clusterrole.rbac.authorization.k8s.io/cert-manager-controller-approve:cert-manager-io created
clusterrole.rbac.authorization.k8s.io/cert-manager-controller-certificatesigningrequests created
clusterrole.rbac.authorization.k8s.io/cert-manager-webhook:subjectaccessreviews created
clusterrolebinding.rbac.authorization.k8s.io/cert-manager-cainjector created
clusterrolebinding.rbac.authorization.k8s.io/cert-manager-controller-issuers created
clusterrolebinding.rbac.authorization.k8s.io/cert-manager-controller-clusterissuers created
clusterrolebinding.rbac.authorization.k8s.io/cert-manager-controller-certificates created
clusterrolebinding.rbac.authorization.k8s.io/cert-manager-controller-orders created
clusterrolebinding.rbac.authorization.k8s.io/cert-manager-controller-challenges created
clusterrolebinding.rbac.authorization.k8s.io/cert-manager-controller-ingress-shim created
clusterrolebinding.rbac.authorization.k8s.io/cert-manager-controller-approve:cert-manager-io created
clusterrolebinding.rbac.authorization.k8s.io/cert-manager-controller-certificatesigningrequests created
clusterrolebinding.rbac.authorization.k8s.io/cert-manager-webhook:subjectaccessreviews created
role.rbac.authorization.k8s.io/cert-manager-cainjector:leaderelection created
role.rbac.authorization.k8s.io/cert-manager:leaderelection created
role.rbac.authorization.k8s.io/cert-manager-webhook:dynamic-serving created
rolebinding.rbac.authorization.k8s.io/cert-manager-cainjector:leaderelection created
rolebinding.rbac.authorization.k8s.io/cert-manager:leaderelection created
rolebinding.rbac.authorization.k8s.io/cert-manager-webhook:dynamic-serving created
service/cert-manager created
service/cert-manager-webhook created
deployment.apps/cert-manager-cainjector created
deployment.apps/cert-manager created
deployment.apps/cert-manager-webhook created
mutatingwebhookconfiguration.admissionregistration.k8s.io/cert-manager-webhook created
validatingwebhookconfiguration.admissionregistration.k8s.io/cert-manager-webhook created
```

Just after previous command check if the cert-manager pods are created:

```bash
$ kubectl get pods -A
NAMESPACE      NAME                                       READY   STATUS              RESTARTS   AGE
kube-system    helm-install-traefik-p7jkh                 0/1     Completed           0          49d
kube-system    metrics-server-7b4f8b595-bldsd             1/1     Running             3          49d
kube-system    local-path-provisioner-7ff9579c6-l6t6s     1/1     Running             3          49d
kube-system    svclb-traefik-r9q6r                        2/2     Running             6          49d
kube-system    svclb-traefik-n6srr                        2/2     Running             6          49d
kube-system    svclb-traefik-kxxn4                        2/2     Running             6          49d
kube-system    coredns-66c464876b-vqrd6                   1/1     Running             3          49d
kube-system    svclb-traefik-74k9f                        2/2     Running             6          49d
kube-system    svclb-traefik-qlgn9                        2/2     Running             6          49d
kube-system    traefik-5dd496474-4fwdm                    1/1     Running             3          49d
cert-manager   cert-manager-86548b886-4xrbj               0/1     ContainerCreating   0          9s
cert-manager   cert-manager-cainjector-6d59c8d4f7-b2vdc   0/1     ContainerCreating   0          9s
cert-manager   cert-manager-webhook-578954cdd-lg5m4       0/1     ContainerCreating   0          9s
```
After a minute or so check again with the wide option to see on which worker nodes the cert-managers pods are running:

```bash
$ kubectl get pods -n cert-manager -o wide
NAME                                       READY   STATUS    RESTARTS   AGE   IP           NODE   NOMINATED NODE   READINESS GATES
cert-manager-cainjector-6d59c8d4f7-b2vdc   1/1     Running   0          21m   10.42.1.17   n5     <none>           <none>
cert-manager-webhook-578954cdd-lg5m4       1/1     Running   0          21m   10.42.0.19   n1     <none>           <none>
cert-manager-86548b886-4xrbj               1/1     Running   0          21m   10.42.5.10   n4     <none>           <none>
```
 
## References

 - [cert-manager documentation](https://cert-manager.io/docs/)
 - [cert-manager sources](https://github.com/jetstack/cert-manager)

### Last updates

- 24/Jan/2023: update with the installation of version v1.11.0
