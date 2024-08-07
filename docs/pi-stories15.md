# PI4 Stories

## Raspberry Pi 4 cluster Series - Troubleshooting pod issues

### PODs are stuck in termating status

If you ever come in the situation that some pods get stuck in "terminating" status [1] for one of another reason (upgrade, delete,...) and then these pods stay in the terminating status then what can you do?

```bash
gdha@n1:~$ kubectl get pods -A
NAMESPACE         NAME                                                  READY   STATUS        RESTARTS       AGE
ingress-nginx     ingress-nginx-admission-patch-w5dhz                   0/1     Completed     0              107d
ingress-nginx     ingress-nginx-admission-create-lgbdc                  0/1     Completed     0              107d
monitoring        grafana-544f695579-g246k                              0/1     Terminating   0              3d20h
graphite          graphite-0                                            0/1     Terminating   1              3d20h
monitoring        node-exporter-dvzxs                                   2/2     Running       54 (17m ago)   105d
celsius           celsius-779654f68d-d56q6                              1/1     Running       24 (17m ago)   92d
ntopng            ntopng-7d5d578657-9wj7g                               1/1     Running       7 (17m ago)    6d23h
```
No panic, just do the following trick - force a termination without waiting on anything:

```bash
gdha@n1:~$ kubectl delete pod graphite-0 --grace-period=0 --force --namespace graphite
Warning: Immediate deletion does not wait for confirmation that the running resource has been terminated. The resource may continue to run on the cluster indefinitely.
pod "graphite-0" force deleted
```

And, there you go - one pod is gone:

```bash
gdha@n1:~$ kubectl get pods -A
NAMESPACE         NAME                                                  READY   STATUS        RESTARTS       AGE
ingress-nginx     ingress-nginx-admission-patch-w5dhz                   0/1     Completed     0              107d
ingress-nginx     ingress-nginx-admission-create-lgbdc                  0/1     Completed     0              107d
monitoring        grafana-544f695579-g246k                              0/1     Terminating   0              3d20h
monitoring        node-exporter-dvzxs                                   2/2     Running       54 (33m ago)   105d
celsius           celsius-779654f68d-d56q6                              1/1     Running       24 (33m ago)   92d
ntopng            ntopng-7d5d578657-9wj7g                               1/1     Running       7 (33m ago)    6d23h
longhorn-system   engine-image-ei-fc06c6fb-hml5v                        1/1     Running       31 (33m ago)   107d
longhorn-system   csi-resizer-847d645569-hncv2                          1/1     Running       1 (33m ago)    3d14h
longhorn-system   csi-snapshotter-59ff78b47b-jk6rm                      1/1     Running       1 (33m ago)    3d14h
longhorn-system   csi-attacher-689fdb475f-ttnzx                         1/1     Running       2 (33m ago)    3d14h
longhorn-system   csi-attacher-689fdb475f-rxblc                         1/1     Running       2 (33m ago)    3d14h
longhorn-system   csi-attacher-689fdb475f-x9zj7                         1/1     Running       2 (33m ago)    3d14h
longhorn-system   csi-provisioner-65dd48db7b-x8hgf                      1/1     Running       2 (33m ago)    3d14h
longhorn-system   csi-snapshotter-59ff78b47b-mpsk7                      1/1     Running       1 (33m ago)    3d14h
celsius           celsius-779654f68d-jbxg8                              1/1     Running       1 (33m ago)    92d
longhorn-system   csi-provisioner-65dd48db7b-r4dwc                      1/1     Running       2 (33m ago)    3d14h
monitoring        node-exporter-6gdxp                                   2/2     Running       2 (33m ago)    3d14h
celsius           celsius-779654f68d-9lmq8                              1/1     Running       1 (33m ago)    92d
longhorn-system   csi-snapshotter-59ff78b47b-8jf5c                      1/1     Running       1 (33m ago)    3d14h
monitoring        node-exporter-2pt9t                                   2/2     Running       2 (33m ago)    105d
longhorn-system   longhorn-conversion-webhook-c6f4bf8b6-jgjph           1/1     Running       1 (33m ago)    107d
longhorn-system   csi-resizer-847d645569-lsk97                          1/1     Running       2 (33m ago)    3d14h
celsius           celsius-779654f68d-j6m6t                              1/1     Running       1 (33m ago)    92d
kube-system       traefik-59497f77c-gxj8z                               1/1     Running       15 (33m ago)   6d23h
monitoring        node-exporter-7m984                                   2/2     Running       2 (33m ago)    105d
celsius           celsius-779654f68d-5vs8r                              1/1     Running       1 (33m ago)    6d23h
cert-manager      cert-manager-cainjector-5fcd49c96-ghvkt               1/1     Running       1 (33m ago)    107d
kube-system       metallb-speaker-wvfzm                                 1/1     Running       1 (33m ago)    3d14h
longhorn-system   csi-resizer-847d645569-cfsng                          1/1     Running       2 (33m ago)    3d14h
kube-system       local-path-provisioner-957fdf8bc-zwhws                1/1     Running       1 (33m ago)    3d16h
monitoring        node-exporter-rbv7f                                   2/2     Running       2 (33m ago)    105d
longhorn-system   engine-image-ei-fc06c6fb-k6ssm                        1/1     Running       1 (33m ago)    3d14h
longhorn-system   longhorn-conversion-webhook-c6f4bf8b6-q6qlc           1/1     Running       1 (33m ago)    6d23h
cert-manager      cert-manager-6ffb79dfdb-gqlnm                         1/1     Running       1 (33m ago)    107d
monitoring        prometheus-operator-754546bd85-pmkcc                  1/1     Running       1 (33m ago)    105d
kube-system       metallb-speaker-6v4gs                                 1/1     Running       1 (33m ago)    107d
longhorn-system   engine-image-ei-fc06c6fb-445sw                        1/1     Running       1 (33m ago)    107d
longhorn-system   engine-image-ei-fc06c6fb-8nwh8                        1/1     Running       1 (33m ago)    107d
kube-system       metallb-speaker-t2b5d                                 1/1     Running       1 (33m ago)    107d
longhorn-system   longhorn-recovery-backend-786b877b6c-tfcg9            1/1     Running       1 (33m ago)    107d
kube-system       coredns-77ccd57875-h9dfk                              1/1     Running       1 (33m ago)    3d16h
longhorn-system   longhorn-recovery-backend-786b877b6c-nr5r5            1/1     Running       28 (33m ago)   107d
logging           loki-stack-promtail-qtvj5                             1/1     Running       1 (33m ago)    97d
kube-system       metallb-speaker-55mxm                                 1/1     Running       1 (33m ago)    107d
kube-system       metallb-controller-5b89f7554c-8rhzv                   1/1     Running       1 (33m ago)    107d
longhorn-system   longhorn-ui-788dd6f547-zwjfl                          1/1     Running       3 (32m ago)    107d
longhorn-system   longhorn-ui-788dd6f547-fw5rq                          1/1     Running       14 (32m ago)   6d23h
ingress-nginx     ingress-nginx-controller-65dc77f88f-kh8sn             1/1     Running       1 (33m ago)    107d
cert-manager      cert-manager-webhook-796ff7697b-ptmhq                 1/1     Running       14 (32m ago)   6d23h
longhorn-system   engine-image-ei-fc06c6fb-jpfwq                        1/1     Running       1 (33m ago)    107d
kube-system       metrics-server-7b67f64457-bk84t                       1/1     Running       60 (32m ago)   107d
longhorn-system   longhorn-admission-webhook-777c6cb845-fb6pl           1/1     Running       1 (33m ago)    107d
kube-system       metallb-speaker-nhzj6                                 1/1     Running       65 (32m ago)   107d
monitoring        kube-state-metrics-6f8578cffd-cmks6                   1/1     Running       37 (32m ago)   105d
longhorn-system   longhorn-admission-webhook-777c6cb845-snjmd           1/1     Running       1              107d
longhorn-system   instance-manager-e-f7ebd11fee14cab3eb5a0c0884af9492   1/1     Running       0              32m
longhorn-system   instance-manager-r-f7ebd11fee14cab3eb5a0c0884af9492   1/1     Running       0              32m
longhorn-system   longhorn-manager-sp98j                                1/1     Running       1 (33m ago)    107d
longhorn-system   longhorn-manager-v6c78                                1/1     Running       1 (33m ago)    107d
longhorn-system   longhorn-manager-25889                                1/1     Running       1 (33m ago)    3d14h
longhorn-system   instance-manager-e-1f203b7579bacda190651c7f40a2cea4   1/1     Running       0              31m
longhorn-system   longhorn-csi-plugin-28jqn                             3/3     Running       5 (32m ago)    3d14h
longhorn-system   instance-manager-r-11dfece26197f3792b18d4fa97734e04   1/1     Running       0              31m
longhorn-system   longhorn-manager-hx647                                1/1     Running       28 (33m ago)   107d
longhorn-system   instance-manager-r-1f203b7579bacda190651c7f40a2cea4   1/1     Running       0              31m
longhorn-system   instance-manager-e-1548f3f3f04de3f9fad47c6ce8941357   1/1     Running       0              31m
longhorn-system   instance-manager-e-11dfece26197f3792b18d4fa97734e04   1/1     Running       0              31m
longhorn-system   instance-manager-r-1548f3f3f04de3f9fad47c6ce8941357   1/1     Running       0              31m
longhorn-system   longhorn-csi-plugin-bmzpb                             3/3     Running       5 (32m ago)    3d14h
longhorn-system   longhorn-driver-deployer-bf975c845-sxtsq              1/1     Running       2 (31m ago)    107d
longhorn-system   longhorn-csi-plugin-fblxn                             3/3     Running       5 (32m ago)    3d14h
longhorn-system   longhorn-csi-plugin-dgtqs                             3/3     Running       5 (32m ago)    3d14h
longhorn-system   longhorn-csi-plugin-6dfv6                             3/3     Running       5 (32m ago)    3d14h
longhorn-system   instance-manager-r-07ae318e5cf6bed1004f251d3c792412   1/1     Running       0              31m
longhorn-system   longhorn-manager-69zkg                                1/1     Running       1 (33m ago)    107d
longhorn-system   instance-manager-e-07ae318e5cf6bed1004f251d3c792412   1/1     Running       0              31m
longhorn-system   csi-provisioner-65dd48db7b-k4lcb                      1/1     Running       2 (31m ago)    3d14h
monitoring        prometheus-prometheus-persistant-0                    2/2     Running       0              29m
nfs-server        nfs-server-f48df7cdc-2zkkq                            1/1     Running       0              30m
monitoring        grafana-544f695579-6jcfz                              1/1     Running       0              30m
logging           loki-stack-promtail-pcrjw                             1/1     Running       25 (33m ago)   97d
logging           loki-stack-0                                          1/1     Running       0              28m
logging           loki-stack-promtail-9hls8                             1/1     Running       1 (33m ago)    97d
logging           loki-stack-promtail-zfpdt                             1/1     Running       1 (33m ago)    3d14h
uptime-kuma       uptime-kuma-0                                         1/1     Running       6 (28m ago)    3d14h
logging           loki-stack-promtail-nh2m7                             1/1     Running       1 (33m ago)    97d
graphite          graphite-0                                            1/1     Running   
```

Repeat these steps for the remaining pods which are stuck:

```bash
gdha@n1:~$ kubectl delete pod grafana-544f695579-g246k --grace-period=0 --force --namespace monitoring
Warning: Immediate deletion does not wait for confirmation that the running resource has been terminated. The resource may continue to run on the cluster indefinitely.
pod "grafana-544f695579-g246k" force deleted
```

### PODs are stuck in ContainerCreating status

Sometimes we come into a situation where a pod is stuck in ContainerCreating status after restarting a cluster (e.g. after a reboot) - example:

```bash
$ kubectl get pods -n graphite          graphite-0 -o wide
NAME         READY   STATUS              RESTARTS   AGE   IP       NODE   NOMINATED NODE   READINESS GATES
graphite-0   0/1     ContainerCreating   0          23m   <none>   n5     <none>           <none>
```

The reason is not obvious as the logs show only:

```bash
$ kubectl logs -n graphite  graphite-0
Error from server (BadRequest): container "graphite" in pod "graphite-0" is waiting to start: ContainerCreating
```

We could suspect that it could not download a fresh copy of the image, but it worked before always like a charm?

Another way to dig a bit deeper to find the real issue is to use the command:

```bash
$ kubectl describe pod -n graphite  graphite-0
Name:             graphite-0
Namespace:        graphite
Priority:         0
Service Account:  default
Node:             n5/192.168.0.205
Start Time:       Wed, 12 Jul 2023 08:36:05 +0200
Labels:           app.kubernetes.io/instance=graphite
                  app.kubernetes.io/name=graphite
                  controller-revision-hash=graphite-645d65f6fb
                  statefulset.kubernetes.io/pod-name=graphite-0
                  workload.user.cattle.io/workloadselector=statefulSet-graphite-graphite
Annotations:      <none>
Status:           Pending
IP:               
IPs:              <none>
Controlled By:    StatefulSet/graphite
Containers:
  graphite:
    Container ID:   
    Image:          ghcr.io/gdha/graphite:v1.2
    Image ID:       
    Ports:          2003/TCP, 80/TCP
    Host Ports:     0/TCP, 0/TCP
    State:          Waiting
      Reason:       ContainerCreating
    Ready:          False
    Restart Count:  0
    Limits:
      memory:  512Mi
    Requests:
      memory:   400Mi
    Liveness:   http-get http://:liveness-port/metrics/find%3Fquery=%2A delay=0s timeout=2s period=2s #success=1 #failure=3
    Readiness:  http-get http://:liveness-port/metrics/find%3Fquery=%2A delay=0s timeout=2s period=2s #success=2 #failure=3
    Startup:    http-get http://:liveness-port/metrics/find%3Fquery=%2A delay=0s timeout=1s period=10s #success=1 #failure=30
    Environment Variables from:
      graphite    Secret  Optional: false
    Environment:  <none>
    Mounts:
      /opt/graphite/storage from graphite-storage (rw)
      /var/log from bind-mount--logs (rw)
      /var/run/secrets from graphite-secrets (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-kxvv5 (ro)
Conditions:
  Type              Status
  Initialized       True 
  Ready             False 
  ContainersReady   False 
  PodScheduled      True 
Volumes:
  bind-mount--logs:
    Type:          HostPath (bare host directory volume)
    Path:          /app/util/graphite/logs
    HostPathType:  DirectoryOrCreate
  graphite-secrets:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  graphite
    Optional:    false
  graphite-storage:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  graphite
    ReadOnly:   false
  kube-api-access-kxvv5:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    ConfigMapOptional:       <nil>
    DownwardAPI:             true
QoS Class:                   Burstable
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type     Reason       Age                   From               Message
  ----     ------       ----                  ----               -------
  Normal   Scheduled    30m                   default-scheduler  Successfully assigned graphite/graphite-0 to n5
  Warning  FailedMount  7m17s (x19 over 29m)  kubelet            MountVolume.MountDevice failed for volume "pvc-14e6e723-0fa2-4b8f-8619-ae02f2b74cdc" : rpc error: code = Internal desc = mount failed: exit status 32
Mounting command: mount
Mounting arguments: -t ext4 -o defaults /dev/longhorn/pvc-14e6e723-0fa2-4b8f-8619-ae02f2b74cdc /var/lib/kubelet/plugins/kubernetes.io/csi/driver.longhorn.io/8a5f0348fbb5570ffccf61701ff8b83081168509f5904af61a35649c9f2cc7d9/globalmount
Output: mount: /var/lib/kubelet/plugins/kubernetes.io/csi/driver.longhorn.io/8a5f0348fbb5570ffccf61701ff8b83081168509f5904af61a35649c9f2cc7d9/globalmount: /dev/longhorn/pvc-14e6e723-0fa2-4b8f-8619-ae02f2b74cdc already mounted or mount point busy.
  Warning  FailedMount  3m15s (x12 over 28m)  kubelet  Unable to attach or mount volumes: unmounted volumes=[graphite-storage], unattached volumes=[], failed to process volumes=[]: timed out waiting for the condition
```

Okay, here we see clearly the error "/dev/longhorn/pvc-14e6e723-0fa2-4b8f-8619-ae02f2b74cdc already mounted or mount point busy". 

So, let us try the following:

```bash
$ kubectl delete pod -n graphite  graphite-0
pod "graphite-0" deleted
$ kubectl get pods -n graphite          graphite-0 -o wide
NAME         READY   STATUS              RESTARTS   AGE   IP       NODE   NOMINATED NODE   READINESS GATES
graphite-0   0/1     ContainerCreating   0          12m   <none>   n5     <none>           <none>
```

Unfortunately, that did not fix it. So, we remove the PVC and recreated it, but no luck. To see the last events use the command:

```bash
$ kubectl get events --all-namespaces  --sort-by='.metadata.creationTimestamp'
NAMESPACE    LAST SEEN   TYPE      REASON        OBJECT                         MESSAGE
graphite     14m         Warning   FailedMount   pod/graphite-0                 MountVolume.MountDevice failed for volume "pvc-90ed5ccf-3152-4e0b-b795-cb985a7d83be" : rpc error: code = Internal desc = format of disk "/dev/longhorn/pvc-90ed5ccf-3152-4e0b-b795-cb985a7d83be" failed: type:("ext4") target:("/var/lib/kubelet/plugins/kubernetes.io/csi/driver.longhorn.io/d3ebefb7681c1a1f9472812339e5a4342cca0dee55d743334be457f6a9cf1fb3/globalmount") options:("defaults") errcode:(exit status 1) output:(mke2fs 1.46.4 (18-Aug-2021)...
graphite     5m8s        Warning   FailedMount   pod/graphite-0                 Unable to attach or mount volumes: unmounted volumes=[graphite-storage], unattached volumes=[], failed to process volumes=[]: timed out waiting for the condition
monitoring   36m         Warning   Unhealthy     pod/grafana-544f695579-st45c   Readiness probe failed: Get "http://10.42.4.117:3000/api/health": context deadline exceeded (Client.Timeout exceeded while awaiting headers)
```
Via some research we came across page [Troubleshooting: `MountVolume.SetUp failed for volume` due to multipathd on the node](https://longhorn.io/kb/troubleshooting-volume-with-multipath/) [2] which might be the solution for our problem here as after rebooting the complete cluster the graphite PVC was mounted correctly, but then the loki-stack PVC was not able to mount with the exact same error. Therefore, we can conclude there is nothing wrong with the PVC, but it is an interaction clash with the multipath daemon.

We update our [k3s ansible playbook](https://github.com/gdha/k3s-ansible) to also update the `/etc/multipath.conf` file to blacklist `/dev/sd*` devices.

### POD stuck in ImagePullBackOff status

For example when we tried to upgrade out ntopng image with helm we got an error like:

```bash
$ kubectl get pods -n ntopng
NAME                      READY   STATUS             RESTARTS   AGE
ntopng-6586866d8b-w668c   0/1     ImagePullBackOff   0          69m
```

The first thing we think of is a mismatch of our GitHub Container Registry (ghrc) Personal Access Token (PAT). How can we verify that the PAT our kubernetes cluster knows is still the same as the one listed in our `~/.ghcr-token` file?

As we know in which namespace to look (in our case ntopng) we can start digging as follow:

```bash
$ kubectl get secret -n ntopng
NAME                           TYPE                             DATA   AGE
ntopng-ghrc                    kubernetes.io/dockerconfigjson   1      67m
ntopng                         Opaque                           0      67m
sh.helm.release.v1.ntopng.v1   helm.sh/release.v1               1      67m
```

Right, as we suspect the ghrc secret lets extract this from out cluster:

```bash
$ kubectl get secret -n ntopng ntopng-ghrc --output="jsonpath={.data.\.dockerconfigjson}" | base64 --decode
{"auths":{"ghcr.io":{"username":"gdha","password":"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","auth":"Z2RoYTpnaHBfdXlPNElwUnJ6TmpFc25JTnN3NEQ1dVZRVWdkYVpSNDR2NGM2"}}}
```

There is our PAT key: `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` and indeed it was an old one which we revoked a time ago.

To fix this we must update our `ghcr-secret.yaml` file and especially the .dockerconfigjson data section. 

How? Well, as example I take the revoked old PAT key of ghrc:

```bash
$ kubectl create secret docker-registry dockerconfigjson-github-com --docker-server=ghcr.io  --docker-username=gdha --docker-password=$(echo -n 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) --dry-run=client -oyaml
apiVersion: v1
data:
  .dockerconfigjson: eyJhdXRocyI6eyJnaGNyLmlvIjp7InVzZXJuYW1lIjoiZ2RoYSIsInBhc3N3b3JkIjoiZ2hwX3V5TzRJcFJyek5qRXNuSU5zdzRENXVWUVVnZGFaUjQ0djRjNiIsImF1dGgiOiJaMlJvWVRwbmFIQmZkWGxQTkVsd1VuSjZUbXBGYzI1SlRuTjNORVExZFZaUlZXZGtZVnBTTkRSMk5HTTIifX19
kind: Secret
metadata:
  creationTimestamp: null
  name: dockerconfigjson-github-com
type: kubernetes.io/dockerconfigjson
```
### How to debug a POD

Interesting topic as we stumbled over the pi4-ntopng POD which refused to start with an error:

```bash
Starting redis-server: 
*** FATAL CONFIG FILE ERROR ***
Reading the configuration file, at line 171
>>> 'logfile /var/log/redis/redis-server.log'
Can't open the log file: Permission denied
failed
29/Sep/2023 15:30:47 [Redis.cpp:98] ERROR: Connection error [Connection refused]
29/Sep/2023 15:30:48 [Redis.cpp:80] Redis has disconnected, reconnecting [remaining attempts: 14]
```

Of course, the POD exits immediately. So, how can we debug our error? It might just be that there is more wrong the only the permission denied error...
We found a good article [3] which gives us some more insight on how to dig deeper.

We need to override the "entrypoint" as it behaves as init 1, and therefore, we cannot just pass a bash command to it. Execute the following:

```bash
$ docker run -it --entrypoint /bin/bash ghcr.io/gdha/pi4-ntopng:5.7.0 
```

This will drop us into a shell command from where we can peek around in the container to see what might be wrong. Indeed as the error above mentioned the ownership of `/var/log/redis` was wrong:

```bash
root@131f7e6411c5:/var/log# ls -l
total 692
-rw-r--r-- 1 root root   4901 Sep 29 15:13 alternatives.log
drwxr-xr-x 1 root root   4096 Sep 29 15:12 apt
-rw-r--r-- 1 root root  58584 Jan 26  2023 bootstrap.log
-rw-rw---- 1 root utmp      0 Jan 26  2023 btmp
-rw-r--r-- 1 root root 282997 Sep 29 15:14 dpkg.log
-rw-r--r-- 1 root root  32000 Sep 29 15:14 faillog
-rw-r--r-- 1 root root    484 Sep 29 15:12 fontconfig.log
drwxr-sr-x 2 root root   4096 Sep 29 15:11 journal
-rw-rw-r-- 1 root root 296000 Sep 29 15:14 lastlog
drwx------ 2 root root   4096 Sep 29 15:11 private
drwxr-s--- 1 root root   4096 Sep 29 15:12 redis
-rw-rw-r-- 1 root utmp      0 Jan 26  2023 wtmp
root@131f7e6411c5:/var/log# chown -R redis:redis /var/log/redis
```

However, as we are inside the container lets try to start redis:

```bash
root@131f7e6411c5:~# /etc/init.d/redis-server  start
Starting redis-server: failed
```

Hum, not 100% okay it seems. However, as the log file of redis should be available now we can check it out:

```bash
root@131f7e6411c5:~# cat /var/log/redis/redis-server.log 
19:C 02 Oct 2023 07:11:40.148 # Can't chdir to '/var/lib/redis': Permission denied
33:C 02 Oct 2023 07:13:22.884 # Can't chdir to '/var/lib/redis': Permission denied
69:C 02 Oct 2023 07:17:39.861 # Can't chdir to '/var/lib/redis': Permission denied
```

Indeed still another directory to be tackled:

```bash
root@131f7e6411c5:~# chown -R redis:redis /var/lib/redis
```

We can start redis again and see what it says:

```bash
root@131f7e6411c5:~# /usr/bin/redis-server
37:C 02 Oct 2023 07:15:29.235 # oO0OoO0OoO0Oo Redis is starting oO0OoO0OoO0Oo
37:C 02 Oct 2023 07:15:29.236 # Redis version=5.0.7, bits=64, commit=00000000, modified=0, pid=37, just started
37:C 02 Oct 2023 07:15:29.236 # Warning: no config file specified, using the default config. In order to specify a config file use /usr/bin/redis-server /path/to/redis.conf
                _._                                                  
           _.-``__ ''-._                                             
      _.-``    `.  `_.  ''-._           Redis 5.0.7 (00000000/0) 64 bit
  .-`` .-```.  ```\/    _.,_ ''-._                                   
 (    '      ,       .-`  | `,    )     Running in standalone mode
 |`-._`-...-` __...-.``-._|'` _.-'|     Port: 6379
 |    `-._   `._    /     _.-'    |     PID: 37
  `-._    `-._  `-./  _.-'    _.-'                                   
 |`-._`-._    `-.__.-'    _.-'_.-'|                                  
 |    `-._`-._        _.-'_.-'    |           http://redis.io        
  `-._    `-._`-.__.-'_.-'    _.-'                                   
 |`-._`-._    `-.__.-'    _.-'_.-'|                                  
 |    `-._`-._        _.-'_.-'    |                                  
  `-._    `-._`-.__.-'_.-'    _.-'                                   
      `-._    `-.__.-'    _.-'                                       
          `-._        _.-'                                           
              `-.__.-'                                               

37:M 02 Oct 2023 07:15:29.258 # Server initialized
37:M 02 Oct 2023 07:15:29.258 # WARNING you have Transparent Huge Pages (THP) support enabled in your kernel. This will create latency and memory usage issues with Redis. To fix this issue run the command 'echo never > /sys/kernel/mm/transparent_hugepage/enabled' as root, and add it to your /etc/rc.local in order to retain the setting after a reboot. Redis must be restarted after THP is disabled.
37:M 02 Oct 2023 07:15:29.259 * Ready to accept connections
```

Yep, it seems to start, but the message around transparent_hugepage is quite interesting which we can take care of in our Dockerfile.

## References

[1] [PODs are stuck in terminating status](https://stackoverflow.com/questions/35453792/pods-stuck-in-terminating-status)

[2] [Troubleshooting: `MountVolume.SetUp failed for volume` due to multipathd on the node](https://longhorn.io/kb/troubleshooting-volume-with-multipath/)

[3] [Ten tips for debugging Docker containers](https://medium.com/@betz.mark/ten-tips-for-debugging-docker-containers-cde4da841a1d)
