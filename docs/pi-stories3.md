# PI4 Stories

## Raspberry Pi 4 cluster Series - Installing k3s software

### Why k3s?

[K3s](https://rancher.com/docs/k3s/latest/en/) is a fully compliant Kubernetes distribution in a single binary perfectly suitable for smaller edge devices such as the Raspberry PI4. Simply said, you can do almost exactly the same as with its big sister kubernetes (k8s). For these kind of devices it is the perfect match.

To get ks3 installed with ansible clone the playbook [k3s-ansible playbook](https://github.com/gdha/k3s-ansible)[1].

K3s is a product from Rancher Labs and can be installed in different ways, such as with [k3sup](https://github.com/alexellis/k3sup) (read the nice article "[Deploying a highly-available K3s with K3sup](https://ma.ttias.be/deploying-highly-available-k3s-k3sup/)") or with a [fork of k3s-ansible](https://github.com/rancher/k3s-ansible) sources. We choose for the latter and made some customisation to fork of k3s-ansible playbook [1].

Do check the `inventory/my-cluster/hosts.ini` file and add you preferences. Also, do not forget to adjust the attributes yaml file `inventory/my-cluster/group_vars/all.yml`. Especially, check the *k3s_version* you want to install. To find the latest stable release of k3s see the [github release page of ks3s](https://github.com/rancher/k3s/releases).


### Run the ansible playbook site.yml

```bash
gdha@n5:~/projects/k3s-ansible$ ansible-playbook site.yml -i inventory/my-cluster/hosts.ini
[WARNING]: While constructing a mapping from /home/gdha/projects/k3s-ansible/roles/ubuntu/tasks/main.yml, line 4, column 5, found a duplicate dict key (backrefs). Using
last defined value only.

PLAY [k3s_cluster] ********************************************************************************************************************************************************

TASK [Gathering Facts] ****************************************************************************************************************************************************
Wednesday 23 September 2020  16:16:13 +0200 (0:00:00.102)       0:00:00.102 *** 
ok: [192.168.0.202]
ok: [192.168.0.204]
ok: [192.168.0.203]
ok: [192.168.0.201]
ok: [192.168.0.205]

TASK [prereq : Set SELinux to disabled state] *****************************************************************************************************************************
Wednesday 23 September 2020  16:16:24 +0200 (0:00:10.334)       0:00:10.437 *** 
skipping: [192.168.0.201]
skipping: [192.168.0.202]
skipping: [192.168.0.203]
skipping: [192.168.0.204]
skipping: [192.168.0.205]

TASK [prereq : Enable IPv4 forwarding] ************************************************************************************************************************************
Wednesday 23 September 2020  16:16:24 +0200 (0:00:00.913)       0:00:11.350 *** 
ok: [192.168.0.203]
ok: [192.168.0.201]
ok: [192.168.0.202]
ok: [192.168.0.204]
ok: [192.168.0.205]

TASK [prereq : Enable IPv6 forwarding] ************************************************************************************************************************************
Wednesday 23 September 2020  16:16:26 +0200 (0:00:01.658)       0:00:13.009 *** 
ok: [192.168.0.201]
ok: [192.168.0.202]
ok: [192.168.0.203]
ok: [192.168.0.204]
ok: [192.168.0.205]

TASK [prereq : Add br_netfilter to /etc/modules-load.d/] ******************************************************************************************************************
Wednesday 23 September 2020  16:16:28 +0200 (0:00:01.781)       0:00:14.790 *** 
skipping: [192.168.0.201]
skipping: [192.168.0.202]
skipping: [192.168.0.203]
skipping: [192.168.0.204]
skipping: [192.168.0.205]

TASK [prereq : Load br_netfilter] *****************************************************************************************************************************************
Wednesday 23 September 2020  16:16:29 +0200 (0:00:00.905)       0:00:15.696 *** 
skipping: [192.168.0.201]
skipping: [192.168.0.202]
skipping: [192.168.0.203]
skipping: [192.168.0.204]
skipping: [192.168.0.205]

TASK [prereq : Set bridge-nf-call-iptables (just to be sure)] *************************************************************************************************************
Wednesday 23 September 2020  16:16:30 +0200 (0:00:00.909)       0:00:16.605 *** 
skipping: [192.168.0.201] => (item=net.bridge.bridge-nf-call-iptables) 
skipping: [192.168.0.201] => (item=net.bridge.bridge-nf-call-ip6tables) 
skipping: [192.168.0.202] => (item=net.bridge.bridge-nf-call-iptables) 
skipping: [192.168.0.202] => (item=net.bridge.bridge-nf-call-ip6tables) 
skipping: [192.168.0.203] => (item=net.bridge.bridge-nf-call-iptables) 
skipping: [192.168.0.203] => (item=net.bridge.bridge-nf-call-ip6tables) 
skipping: [192.168.0.204] => (item=net.bridge.bridge-nf-call-iptables) 
skipping: [192.168.0.204] => (item=net.bridge.bridge-nf-call-ip6tables) 
skipping: [192.168.0.205] => (item=net.bridge.bridge-nf-call-iptables) 
skipping: [192.168.0.205] => (item=net.bridge.bridge-nf-call-ip6tables) 

TASK [prereq : Add /usr/local/bin to sudo secure_path] ********************************************************************************************************************
Wednesday 23 September 2020  16:16:31 +0200 (0:00:00.939)       0:00:17.544 *** 
skipping: [192.168.0.201]
skipping: [192.168.0.202]
skipping: [192.168.0.203]
skipping: [192.168.0.204]
skipping: [192.168.0.205]

TASK [download : Delete k3s if already present] ***************************************************************************************************************************
Wednesday 23 September 2020  16:16:32 +0200 (0:00:01.169)       0:00:18.714 *** 
changed: [192.168.0.201]
changed: [192.168.0.203]
changed: [192.168.0.202]
changed: [192.168.0.204]
changed: [192.168.0.205]

TASK [download : Download k3s binary x64] *********************************************************************************************************************************
Wednesday 23 September 2020  16:16:34 +0200 (0:00:01.707)       0:00:20.422 *** 
skipping: [192.168.0.201]
skipping: [192.168.0.202]
skipping: [192.168.0.203]
skipping: [192.168.0.204]
skipping: [192.168.0.205]

TASK [download : Download k3s binary arm64] *******************************************************************************************************************************
Wednesday 23 September 2020  16:16:34 +0200 (0:00:00.943)       0:00:21.365 *** 
changed: [192.168.0.202]
changed: [192.168.0.204]
changed: [192.168.0.201]
changed: [192.168.0.205]
changed: [192.168.0.203]

TASK [download : Download k3s binary armhf] *******************************************************************************************************************************
Wednesday 23 September 2020  16:17:34 +0200 (0:00:59.059)       0:01:20.425 *** 
skipping: [192.168.0.201]
skipping: [192.168.0.202]
skipping: [192.168.0.203]
skipping: [192.168.0.204]
skipping: [192.168.0.205]

TASK [raspbian : Test for Raspbian] ***************************************************************************************************************************************
Wednesday 23 September 2020  16:17:34 +0200 (0:00:00.940)       0:01:21.365 *** 
ok: [192.168.0.201]
ok: [192.168.0.202]
ok: [192.168.0.203]
ok: [192.168.0.204]
ok: [192.168.0.205]

TASK [raspbian : Activating cgroup support] *******************************************************************************************************************************
Wednesday 23 September 2020  16:17:36 +0200 (0:00:01.165)       0:01:22.531 *** 
skipping: [192.168.0.201]
skipping: [192.168.0.202]
skipping: [192.168.0.203]
skipping: [192.168.0.204]
skipping: [192.168.0.205]

TASK [raspbian : Flush iptables before changing to iptables-legacy] *******************************************************************************************************
Wednesday 23 September 2020  16:17:36 +0200 (0:00:00.796)       0:01:23.327 *** 
skipping: [192.168.0.201]
skipping: [192.168.0.202]
skipping: [192.168.0.203]
skipping: [192.168.0.204]
skipping: [192.168.0.205]

TASK [raspbian : Changing to iptables-legacy] *****************************************************************************************************************************
Wednesday 23 September 2020  16:17:37 +0200 (0:00:00.795)       0:01:24.123 *** 
skipping: [192.168.0.201]
skipping: [192.168.0.202]
skipping: [192.168.0.203]
skipping: [192.168.0.204]
skipping: [192.168.0.205]

TASK [raspbian : Changing to ip6tables-legacy] ****************************************************************************************************************************
Wednesday 23 September 2020  16:17:38 +0200 (0:00:00.798)       0:01:24.922 *** 
skipping: [192.168.0.201]
skipping: [192.168.0.202]
skipping: [192.168.0.203]
skipping: [192.168.0.204]
skipping: [192.168.0.205]

TASK [raspbian : Rebooting] ***********************************************************************************************************************************************
Wednesday 23 September 2020  16:17:39 +0200 (0:00:00.940)       0:01:25.862 *** 
skipping: [192.168.0.201]
skipping: [192.168.0.202]
skipping: [192.168.0.203]
skipping: [192.168.0.204]
skipping: [192.168.0.205]

TASK [ubuntu : Enable cgroup via boot commandline if not already enabled for Ubuntu on ARM] *******************************************************************************
Wednesday 23 September 2020  16:17:40 +0200 (0:00:00.800)       0:01:26.663 *** 
ok: [192.168.0.203]
ok: [192.168.0.202]
ok: [192.168.0.201]
ok: [192.168.0.204]
ok: [192.168.0.205]

PLAY [master] *************************************************************************************************************************************************************

TASK [Gathering Facts] ****************************************************************************************************************************************************
Wednesday 23 September 2020  16:17:42 +0200 (0:00:01.887)       0:01:28.550 *** 
ok: [192.168.0.201]

TASK [k3s/master : Copy K3s service file] *********************************************************************************************************************************
Wednesday 23 September 2020  16:17:49 +0200 (0:00:07.512)       0:01:36.063 *** 
ok: [192.168.0.201]

TASK [k3s/master : Enable and check K3s service] **************************************************************************************************************************
Wednesday 23 September 2020  16:17:51 +0200 (0:00:02.229)       0:01:38.293 *** 
changed: [192.168.0.201]

TASK [k3s/master : Wait for node-token] ***********************************************************************************************************************************
Wednesday 23 September 2020  16:18:11 +0200 (0:00:19.552)       0:01:57.846 *** 
ok: [192.168.0.201]

TASK [k3s/master : Register node-token file access mode] ******************************************************************************************************************
Wednesday 23 September 2020  16:18:13 +0200 (0:00:01.661)       0:01:59.508 *** 
ok: [192.168.0.201]

TASK [k3s/master : Change file access node-token] *************************************************************************************************************************
Wednesday 23 September 2020  16:18:14 +0200 (0:00:01.250)       0:02:00.758 *** 
changed: [192.168.0.201]

TASK [k3s/master : Read node-token from master] ***************************************************************************************************************************
Wednesday 23 September 2020  16:18:15 +0200 (0:00:01.036)       0:02:01.794 *** 
ok: [192.168.0.201]

TASK [k3s/master : Store Master node-token] *******************************************************************************************************************************
Wednesday 23 September 2020  16:18:16 +0200 (0:00:01.293)       0:02:03.087 *** 
ok: [192.168.0.201]

TASK [k3s/master : Restore node-token file access] ************************************************************************************************************************
Wednesday 23 September 2020  16:18:17 +0200 (0:00:00.277)       0:02:03.365 *** 
changed: [192.168.0.201]

TASK [k3s/master : Create directory .kube] ********************************************************************************************************************************
Wednesday 23 September 2020  16:18:18 +0200 (0:00:01.079)       0:02:04.445 *** 
ok: [192.168.0.201]

TASK [k3s/master : Copy config file to user home directory] ***************************************************************************************************************
Wednesday 23 September 2020  16:18:19 +0200 (0:00:01.083)       0:02:05.528 *** 
changed: [192.168.0.201]

TASK [k3s/master : Replace https://localhost:6443 by https://master-ip:6443] **********************************************************************************************
Wednesday 23 September 2020  16:18:20 +0200 (0:00:01.586)       0:02:07.115 *** 
changed: [192.168.0.201]

TASK [k3s/master : Create kubectl symlink] ********************************************************************************************************************************
Wednesday 23 September 2020  16:18:22 +0200 (0:00:02.239)       0:02:09.354 *** 
ok: [192.168.0.201]

TASK [k3s/master : Create crictl symlink] *********************************************************************************************************************************
Wednesday 23 September 2020  16:18:23 +0200 (0:00:00.973)       0:02:10.328 *** 
ok: [192.168.0.201]

PLAY [node] ***************************************************************************************************************************************************************

TASK [Gathering Facts] ****************************************************************************************************************************************************
Wednesday 23 September 2020  16:18:25 +0200 (0:00:01.351)       0:02:11.680 *** 
ok: [192.168.0.203]
ok: [192.168.0.205]
ok: [192.168.0.202]
ok: [192.168.0.204]

TASK [k3s/node : Copy K3s service file] ***********************************************************************************************************************************
Wednesday 23 September 2020  16:18:35 +0200 (0:00:09.865)       0:02:21.546 *** 
ok: [192.168.0.202]
ok: [192.168.0.203]
ok: [192.168.0.204]
ok: [192.168.0.205]

TASK [k3s/node : Enable and check K3s service] ****************************************************************************************************************************
Wednesday 23 September 2020  16:18:37 +0200 (0:00:02.715)       0:02:24.261 *** 
changed: [192.168.0.202]
changed: [192.168.0.204]
changed: [192.168.0.205]
changed: [192.168.0.203]

TASK [k3s/node : Create directory .kube] **********************************************************************************************************************************
Wednesday 23 September 2020  16:18:46 +0200 (0:00:08.523)       0:02:32.785 *** 
ok: [192.168.0.202]
ok: [192.168.0.203]
ok: [192.168.0.204]
ok: [192.168.0.205]

TASK [k3s/node : Create kubectl/crictl symlinks] **************************************************************************************************************************
Wednesday 23 September 2020  16:18:48 +0200 (0:00:01.660)       0:02:34.446 *** 
ok: [192.168.0.202] => (item=kubectl)
ok: [192.168.0.203] => (item=kubectl)
ok: [192.168.0.204] => (item=kubectl)
ok: [192.168.0.202] => (item=crictl)
ok: [192.168.0.205] => (item=kubectl)
ok: [192.168.0.203] => (item=crictl)
ok: [192.168.0.204] => (item=crictl)
ok: [192.168.0.205] => (item=crictl)

TASK [k3s/node : fetch the ~/.kube/config file] ***************************************************************************************************************************
Wednesday 23 September 2020  16:18:50 +0200 (0:00:02.746)       0:02:37.192 *** 
skipping: [192.168.0.202]
skipping: [192.168.0.203]
skipping: [192.168.0.204]
skipping: [192.168.0.205]

PLAY RECAP ****************************************************************************************************************************************************************
192.168.0.201              : ok=21   changed=7    unreachable=0    failed=0    skipped=12   rescued=0    ignored=0   
192.168.0.202              : ok=12   changed=3    unreachable=0    failed=0    skipped=13   rescued=0    ignored=0   
192.168.0.203              : ok=12   changed=3    unreachable=0    failed=0    skipped=13   rescued=0    ignored=0   
192.168.0.204              : ok=12   changed=3    unreachable=0    failed=0    skipped=13   rescued=0    ignored=0   
192.168.0.205              : ok=12   changed=3    unreachable=0    failed=0    skipped=13   rescued=0    ignored=0   

Wednesday 23 September 2020  16:18:51 +0200 (0:00:00.630)       0:02:37.823 *** 
=============================================================================== 
download : Download k3s binary arm64 ------------------------------------------------------------------------------------------------------------------------------ 59.06s
k3s/master : Enable and check K3s service ------------------------------------------------------------------------------------------------------------------------- 19.55s
Gathering Facts --------------------------------------------------------------------------------------------------------------------------------------------------- 10.33s
Gathering Facts ---------------------------------------------------------------------------------------------------------------------------------------------------- 9.87s
k3s/node : Enable and check K3s service ---------------------------------------------------------------------------------------------------------------------------- 8.52s
Gathering Facts ---------------------------------------------------------------------------------------------------------------------------------------------------- 7.51s
k3s/node : Create kubectl/crictl symlinks -------------------------------------------------------------------------------------------------------------------------- 2.75s
k3s/node : Copy K3s service file ----------------------------------------------------------------------------------------------------------------------------------- 2.72s
k3s/master : Replace https://localhost:6443 by https://master-ip:6443 ---------------------------------------------------------------------------------------------- 2.24s
k3s/master : Copy K3s service file --------------------------------------------------------------------------------------------------------------------------------- 2.23s
ubuntu : Enable cgroup via boot commandline if not already enabled for Ubuntu on ARM ------------------------------------------------------------------------------- 1.89s
prereq : Enable IPv6 forwarding ------------------------------------------------------------------------------------------------------------------------------------ 1.78s
download : Delete k3s if already present --------------------------------------------------------------------------------------------------------------------------- 1.71s
k3s/master : Wait for node-token ----------------------------------------------------------------------------------------------------------------------------------- 1.66s
k3s/node : Create directory .kube ---------------------------------------------------------------------------------------------------------------------------------- 1.66s
prereq : Enable IPv4 forwarding ------------------------------------------------------------------------------------------------------------------------------------ 1.66s
k3s/master : Copy config file to user home directory --------------------------------------------------------------------------------------------------------------- 1.59s
k3s/master : Create crictl symlink --------------------------------------------------------------------------------------------------------------------------------- 1.35s
k3s/master : Read node-token from master --------------------------------------------------------------------------------------------------------------------------- 1.29s
k3s/master : Register node-token file access mode ------------------------------------------------------------------------------------------------------------------ 1.25s
```

### k3s is up and running?

Wow the installation went rather fast - an exciting moment - is k3s working fine?

```bash
gdha@n5:~/projects/k3s-ansible$ k3s --version
k3s version v1.19.2+k3s1 (d38505b1)

gdha@n5:~/projects/k3s-ansible$ k3s kubectl cluster-info

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.
The connection to the server localhost:8080 was refused - did you specify the right host or port?
```

Ok - k3s needs the cluster configuration file via variable `KUBECONFIG` or the file `~/kube/config`. We are on a worker node (n5) and choose as master node n1, therefore, the cluster configuration was created and resides on the master node. Or, we copy this to each node or decide only the work from the master node. It is a much better choice to copy the configuration to each node instead.

To do so first copy the cluster configuration file from the master node (n1) to this node (n5):

```bash
gdha@n5:~/projects/k3s-ansible$ scp n1:.kube/config ~/.kube/
config                                                                                                                                   100% 2793     2.0MB/s   00:00    
```

Now see if we have more luck with a kubernetes command to get all the nodes in this cluster and dumping the cluster-info?

```bash
gdha@n5:~/projects/k3s-ansible$ kubectl get nodes
NAME   STATUS   ROLES    AGE   VERSION
n2     Ready    <none>   49d   v1.19.2+k3s1
n5     Ready    <none>   49d   v1.19.2+k3s1
n4     Ready    <none>   49d   v1.19.2+k3s1
n3     Ready    <none>   49d   v1.19.2+k3s1
n1     Ready    master   49d   v1.19.2+k3s1

gdha@n5:~/projects/k3s-ansible$ k3s kubectl cluster-info
Kubernetes master is running at https://192.168.0.201:6443
CoreDNS is running at https://192.168.0.201:6443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy
Metrics-server is running at https://192.168.0.201:6443/api/v1/namespaces/kube-system/services/https:metrics-server:/proxy

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.
```

Yes, that works fine, so lets copy the configuration file to the other nodes as well:

```bash
gdha@n5:~/projects/k3s-ansible$ scp ~/.kube/config n2:~/.kube/
config                                                                                                                                   100% 2793     1.9MB/s   00:00    
gdha@n5:~/projects/k3s-ansible$ scp ~/.kube/config n3:~/.kube/
config                                                                                                                                   100% 2793     1.7MB/s   00:00    
gdha@n5:~/projects/k3s-ansible$ scp ~/.kube/config n4:~/.kube/
config                                                          
```

And finally, to conclude this story, what pods are running within a basic k3s setup?

```bash
gdha@n5:~/projects/k3s-ansible$ kubectl get pods -A
NAMESPACE     NAME                                     READY   STATUS      RESTARTS   AGE
kube-system   helm-install-traefik-p7jkh               0/1     Completed   0          4d22h
kube-system   svclb-traefik-kxxn4                      2/2     Running     2          4d22h
kube-system   svclb-traefik-r9q6r                      2/2     Running     2          4d22h
kube-system   svclb-traefik-qlgn9                      2/2     Running     2          4d22h
kube-system   traefik-5dd496474-4fwdm                  1/1     Running     1          4d22h
kube-system   local-path-provisioner-7ff9579c6-l6t6s   1/1     Running     1          4d22h
kube-system   svclb-traefik-n6srr                      2/2     Running     2          4d22h
kube-system   coredns-66c464876b-vqrd6                 1/1     Running     1          4d22h
kube-system   metrics-server-7b4f8b595-bldsd           1/1     Running     1          4d22h
kube-system   svclb-traefik-74k9f                      2/2     Running     2          4d22h
```

### References

[1] [Ansible k3s-ansible playbook](https://github.com/gdha/k3s-ansible)

[2] [Deploying a highly-available K3s with K3sup](https://ma.ttias.be/deploying-highly-available-k3s-k3sup/)

### Edit history

- initial post on 28/Sep/2020
