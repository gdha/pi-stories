# PI4 Stories

## Raspberry Pi 4 cluster Series - Issues after upgrading k3s

We upgrade our k3s version v1.20.4+k3s1 to v1.22.4+k3s1 according the procedure ligned out in [1].

However, we noticed that after the k3s upgrade their where some issues with our cluster as seen with command `kubectl get pods -A`

* cert-manager      cert-manager-cainjector-6d59c8d4f7-hjszd                          0/1     CrashLoopBackOff    39 (102s ago)   270d
* longhorn-system   longhorn-driver-deployer-666c84fbb7-2lqqm                         0/1     CrashLoopBackOff    43 (3m13s ago)   3h28m
* graphite          graphite-0                                                        0/1     ContainerCreating   0                167m

### Fixing the cert-manager issue

The first thing you need to do when you have issues with apod is looking at the logs, therefore, we execute the following:

```bash
$ k logs -n cert-manager cert-manager-cainjector-6d59c8d4f7-hjszd
I1207 13:08:09.495826       1 start.go:89] "starting" version="v1.0.4" revision="4d870e49b43960fad974487a262395e65da1373e"
I1207 13:08:11.316015       1 request.go:645] Throttling request took 1.036581941s, request: GET:https://10.43.0.1:443/apis/admissionregistration.k8s.io/v1?timeout=32s
I1207 13:08:12.316195       1 request.go:645] Throttling request took 2.035257813s, request: GET:https://10.43.0.1:443/apis/node.k8s.io/v1beta1?timeout=32s
E1207 13:08:12.325362       1 start.go:158] cert-manager/ca-injector "msg"="error registering core-only controllers" "error"="no matches for kind \"MutatingWebhookConfiguration\" in version \"admissionregistration.k8s.io/v1beta1\""
```

From above output we can see that the cert-manager version is v1.0.4. The best thing to do is going to see the documentation [2] of cert-manager whether there are known issues or other items of interest. At the page [2] we read the following sentence "Following their deprecation in version 1.4, the cert-manager API versions v1alpha2, v1alpha3, and v1beta1 are no longer served." - okay - we need to upgrade cert-manager to fix this.

We did found an excellent article [3] that serverd our purposes. We simply have to look at the GitHub page of cert-manager releases [4] to find the latest stable version of cert-manager (today 07 December 2021 it was v1.6.1).

Then we just downloaded the yaml file of cert-manager for arm (as this is a pi cluster) with a simple trick described oon [3]:

```bash
curl -sL \
https://github.com/jetstack/cert-manager/releases/download/v1.6.1/cert-manager.yaml |\
sed -r 's/(image:.*):(v.*)$/\1-arm:\2/g' > cert-manager-arm.yaml
```

Finally, we can upgrade our cert-manager pods with the command:

```bash
$ kubectl replace -f cert-manager-arm.yaml
customresourcedefinition.apiextensions.k8s.io/certificaterequests.cert-manager.io replaced
customresourcedefinition.apiextensions.k8s.io/certificates.cert-manager.io replaced
customresourcedefinition.apiextensions.k8s.io/challenges.acme.cert-manager.io replaced
customresourcedefinition.apiextensions.k8s.io/clusterissuers.cert-manager.io replaced
customresourcedefinition.apiextensions.k8s.io/issuers.cert-manager.io replaced
customresourcedefinition.apiextensions.k8s.io/orders.acme.cert-manager.io replaced
namespace/cert-manager replaced
serviceaccount/cert-manager-cainjector replaced
serviceaccount/cert-manager replaced
serviceaccount/cert-manager-webhook replaced
clusterrole.rbac.authorization.k8s.io/cert-manager-cainjector replaced
clusterrole.rbac.authorization.k8s.io/cert-manager-controller-issuers replaced
clusterrole.rbac.authorization.k8s.io/cert-manager-controller-clusterissuers replaced
clusterrole.rbac.authorization.k8s.io/cert-manager-controller-certificates replaced
clusterrole.rbac.authorization.k8s.io/cert-manager-controller-orders replaced
clusterrole.rbac.authorization.k8s.io/cert-manager-controller-challenges replaced
clusterrole.rbac.authorization.k8s.io/cert-manager-controller-ingress-shim replaced
clusterrole.rbac.authorization.k8s.io/cert-manager-view replaced
clusterrole.rbac.authorization.k8s.io/cert-manager-edit replaced
clusterrole.rbac.authorization.k8s.io/cert-manager-controller-approve:cert-manager-io replaced
clusterrole.rbac.authorization.k8s.io/cert-manager-controller-certificatesigningrequests replaced
clusterrole.rbac.authorization.k8s.io/cert-manager-webhook:subjectaccessreviews replaced
clusterrolebinding.rbac.authorization.k8s.io/cert-manager-cainjector replaced
clusterrolebinding.rbac.authorization.k8s.io/cert-manager-controller-issuers replaced
clusterrolebinding.rbac.authorization.k8s.io/cert-manager-controller-clusterissuers replaced
clusterrolebinding.rbac.authorization.k8s.io/cert-manager-controller-certificates replaced
clusterrolebinding.rbac.authorization.k8s.io/cert-manager-controller-orders replaced
clusterrolebinding.rbac.authorization.k8s.io/cert-manager-controller-challenges replaced
clusterrolebinding.rbac.authorization.k8s.io/cert-manager-controller-ingress-shim replaced
clusterrolebinding.rbac.authorization.k8s.io/cert-manager-controller-approve:cert-manager-io replaced
clusterrolebinding.rbac.authorization.k8s.io/cert-manager-controller-certificatesigningrequests replaced
clusterrolebinding.rbac.authorization.k8s.io/cert-manager-webhook:subjectaccessreviews replaced
role.rbac.authorization.k8s.io/cert-manager-cainjector:leaderelection replaced
role.rbac.authorization.k8s.io/cert-manager:leaderelection replaced
role.rbac.authorization.k8s.io/cert-manager-webhook:dynamic-serving replaced
rolebinding.rbac.authorization.k8s.io/cert-manager-cainjector:leaderelection replaced
rolebinding.rbac.authorization.k8s.io/cert-manager:leaderelection replaced
rolebinding.rbac.authorization.k8s.io/cert-manager-webhook:dynamic-serving replaced
service/cert-manager replaced
service/cert-manager-webhook replaced
deployment.apps/cert-manager-cainjector replaced
deployment.apps/cert-manager replaced
deployment.apps/cert-manager-webhook replaced
mutatingwebhookconfiguration.admissionregistration.k8s.io/cert-manager-webhook replaced
validatingwebhookconfiguration.admissionregistration.k8s.io/cert-manager-webhook replaced

$ kubectl get pods -n cert-manager
NAME                                       READY   STATUS    RESTARTS   AGE
cert-manager-5d4dc99cfc-mfmrp              1/1     Running   0          161m
cert-manager-cainjector-598fcd9fdd-sn9cs   1/1     Running   0          161m
cert-manager-webhook-746ff5ffb9-wmnvp      1/1     Running   0          161m
```

### References

[1] [Raspberry Pi 4 cluster Series - Upgrading k3s software on your cluster](https://gdha.github.io/pi-stories/pi-stories6/)

[2] [Removing Deprecated API Resources in cert-manager](https://cert-manager.io/docs/installation/upgrading/remove-deprecated-apis/)

[3] [K3s on Raspberry Pi - cert-manager](https://bryanbende.com/development/2021/07/01/k3s-raspberry-pi-cert-manager)

[4] [GitHub Cert-manager release page](https://github.com/jetstack/cert-manager/releases)
