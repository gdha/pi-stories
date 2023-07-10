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

Repeat these steps for the remining pods which are stuck:

```bash
gdha@n1:~$ kubectl delete pod grafana-544f695579-g246k --grace-period=0 --force --namespace monitoring
Warning: Immediate deletion does not wait for confirmation that the running resource has been terminated. The resource may continue to run on the cluster indefinitely.
pod "grafana-544f695579-g246k" force deleted
```

## References

[1] [PODs are stuck in terminating status](https://stackoverflow.com/questions/35453792/pods-stuck-in-terminating-status)
