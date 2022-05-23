# PI4 Stories

## Raspberry Pi 4 cluster Series - Replacing internal traefik with Metallb

The problem with have is that with our home pi4 cluster we don't have a decnt external load-balancer.
Therefore, it is hard to access pods via an external IP address, such as the ones we have on our hosts (in
our case in the range of 192.168.0.200-254).

The steps we need to perform are...

### Re-configure the pi4 cluster with ansible

Our [k3s-ansible](https://github.com/gdha/k3s-ansible) project was updated with:

```bash
$ cat inventory/my-cluster/group_vars/all.yml
---
k3s_version: v1.23.6+k3s1
ansible_user: gdha
systemd_dir: /etc/systemd/system
master_ip: "{{ hostvars[groups['master'][0]]['ansible_host'] | default(groups['master'][0]) }}"
extra_server_args: "--write-kubeconfig-mode 644 --disable traefik --disable servicelb"
extra_agent_args: ""
```

in such way by disabling the default traefik and internal load-balancer delivered with the standard k3s implementation.
While we were busy we also used the latest k3s version available at this given moment.

Then it is just a matter of re-running:

```bash
ansible-playbook site.yml -i inventory/my-cluster/hosts.ini
```

It will remove k3s and re-implement it with the internal traefik, but all pods already installed remain present. Excellent news.

### Install metalllb layer2 load-balancer

The main documentation of metallb can be found at [https://metallb.universe.tf/installation/](https://metallb.universe.tf/installation/) [1].
We used the following steps:

```bash
$ helm repo add metallb https://metallb.github.io/metallb
$ helm repo list
NAME    	URL                              
longhorn	https://charts.longhorn.io       
kiwigrid	https://kiwigrid.github.io       
metallb 	https://metallb.github.io/metallb

$ helm install metallb metallb/metallb --namespace kube-system \
   --set configInline.address-pools[0].name=default \
   --set configInline.address-pools[0].protocol=layer2 \
   --set configInline.address-pools[0].addresses[0]=192.168.0.240-192.168.0.250

W0523 12:38:34.591089   84914 warnings.go:70] policy/v1beta1 PodSecurityPolicy is deprecated in v1.21+, unavailable in v1.25+
W0523 12:38:34.600588   84914 warnings.go:70] policy/v1beta1 PodSecurityPolicy is deprecated in v1.21+, unavailable in v1.25+
W0523 12:38:34.788874   84914 warnings.go:70] policy/v1beta1 PodSecurityPolicy is deprecated in v1.21+, unavailable in v1.25+
W0523 12:38:34.790806   84914 warnings.go:70] policy/v1beta1 PodSecurityPolicy is deprecated in v1.21+, unavailable in v1.25+
NAME: metallb
LAST DEPLOYED: Mon May 23 12:38:29 2022
NAMESPACE: kube-system
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
MetalLB is now running in the cluster.
LoadBalancer Services in your cluster are now available on the IPs you
defined in MetalLB's configuration:

config:
  address-pools:
  - addresses:
    - 192.168.0.240-192.168.0.250
    name: default
    protocol: layer2

To see IP assignments, try `kubectl get services`.
```

To verify the load-balancer is working we could execute:

```bash
$ kubectl get svc -n ingress-nginx
NAME                                 TYPE           CLUSTER-IP      EXTERNAL-IP     PORT(S)                      AGE
ingress-nginx-controller-admission   ClusterIP      10.43.197.96    <none>          443/TCP                      11d
ingress-nginx-controller             LoadBalancer   10.43.159.135   192.168.0.240   80:31719/TCP,443:32502/TCP   11d
```

Okay, so far so good.

### Install traefik2 as replacement for the internal traefik of k3s

Execute the following commands:

```bash
$ helm repo add traefik https://helm.traefik.io/traefik
$ helm repo update
Hang tight while we grab the latest from your chart repositories...
...Successfully got an update from the "metallb" chart repository
...Successfully got an update from the "longhorn" chart repository
...Successfully got an update from the "traefik" chart repository
...Successfully got an update from the "kiwigrid" chart repository
Update Complete. ⎈Happy Helming!⎈
```

We need to define a dummy (internal) name for our treafik2 application, therefore, create a file like the one shown below:

```bash
$ cat traefik-values.yaml 
dashboard:
 enabled: true
 domain: traefik.example.com
rbac:
 enabled: true
```

And, finally use helm to install traefik2 with our hand-crafted values yaml file:

```bash
$ helm install traefik traefik/traefik -n kube-system -f traefik-values.yaml 
NAME: traefik
LAST DEPLOYED: Mon May 23 14:53:46 2022
NAMESPACE: kube-system
STATUS: deployed
REVISION: 1
TEST SUITE: None
```

Check if it is created properly:

```bash
$ kubectl get pods -n kube-system
NAME                                      READY   STATUS    RESTARTS   AGE
coredns-d76bd69b-8z7dh                    1/1     Running   0          4h25m
local-path-provisioner-6c79684f77-jvdf6   1/1     Running   0          4h25m
metrics-server-7cd5fcb6b7-d6wlx           1/1     Running   0          4h25m
metallb-controller-777cbcf64f-vfz5v       1/1     Running   0          136m
metallb-speaker-r7wbg                     1/1     Running   0          136m
metallb-speaker-5lxff                     1/1     Running   0          136m
metallb-speaker-cxskn                     1/1     Running   0          136m
metallb-speaker-24vgg                     1/1     Running   0          136m
metallb-speaker-wmzkg                     1/1     Running   0          136m
traefik-7b9cf77df9-cwp4l                  1/1     Running   0          67s
```

And, also very if the treafik service is present:

```bash
$ kubectl get svc -n kube-system | grep traefik
traefik          LoadBalancer   10.43.21.173   192.168.0.241   80:30339/TCP,443:31587/TCP   2m27s
```

We can also check the logs of traefik:

```bash
$ kubectl -n kube-system logs $(kubectl -n kube-system get pods --selector "app.kubernetes.io/name=traefik" --output=name)
time="2022-05-23T12:54:04Z" level=info msg="Configuration loaded from flags."
```

When we see above listed line the we are sure traefik is properly installed and configured.
Now, we are ready to do some more tests with our new load-balancer and traefik.

## References

[1] [Metallb](https://metallb.universe.tf/installation/)

[2] [Setting up your own k3s home cluster](https://www.fullstaq.com/knowledge-hub/blogs/setting-up-your-own-k3s-home-cluster)

[3] [Configuring Traefik 2 Ingress for Kubernetes](https://techno-tim.github.io/posts/k3s-traefik-rancher/)

