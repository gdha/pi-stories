# PI4 Stories

## Raspberry Pi 4 cluster Series - Install helm3

Installing `helm` can best be done as follow:

```bash
# Download & install Helm
$ curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
$ chmod +x get_helm.sh 
$ ./get_helm.sh
Error: could not find tiller
Helm v3.16.4 is available. Changing from version .
Downloading https://get.helm.sh/helm-v3.16.4-linux-arm64.tar.gz
Verifying checksum... Done.
Preparing to install helm into /usr/local/bin
helm installed into /usr/local/bin/helm
```

To see all the options that come with `helm` command use the `--help` option.
For example:

```bash
$ helm repo list
NAME    	URL                                  
longhorn	https://charts.longhorn.io           
kiwigrid	https://kiwigrid.github.io           
metallb 	https://metallb.github.io/metallb    
traefik 	https://helm.traefik.io/traefik      
grafana 	https://grafana.github.io/helm-charts
```

If you want `helm` command to be available on all your nodes you can use `ansible`:

```bash
$ ansible pi -m copy -b -a "src=/usr/local/bin/helm dest=/usr/local/bin/helm mode=0755"
```

## References

[1] [Helm documentation](https://docs.k3s.io/helm)

[2] [Installing Helm](https://helm.sh/docs/intro/install/)

## Edit history

* initial post on 13/Jan/2025

