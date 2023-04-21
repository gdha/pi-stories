 PI4 Stories

## Raspberry Pi 4 cluster Series - Setup logging with Loki

This story is mostly based on [Logging Logs](https://rpi4cluster.com/monitoring/k3s-logging/) article.

We have our Prometheus and Grafana on k3s cluster if you followed my guides. But Prometheus is not able to get logs from k3s nodes, containers, Kubernetes API, and it's not made of that kind of monitoring. We need Loki + Promtail, you can find all info on their Grafana Loki and Promtail.

* Loki - or also known as Grafana Loki - log aggregation system, it does not index the contents of the logs, but rather a set of labels for each log stream. In simple terms, it's the app that accepts logs from everything and store it.

* Promtail - is a tool that reads the logs from various sources like OS, Containers, Kubernetes API server etc... and push them to Loki.

We will be using Arkade to install loki, therefore, we first need to install it.

### Install Arkade

This is basically Helm, just made even simpler.

More info about Arkade: [https://github.com/alexellis/arkade](https://github.com/alexellis/arkade)

```bash
$ curl -SLsf https://dl.get-arkade.dev/ | sudo sh
aarch64
Downloading package https://github.com/alexellis/arkade/releases/download/0.8.62/arkade-arm64 as /tmp/arkade-arm64
Download complete.

Running with sufficient permissions to attempt to move arkade to /usr/local/bin
New version of arkade installed to /usr/local/bin
Creating alias 'ark' for 'arkade'.
            _             _
  __ _ _ __| | ____ _  __| | ___
 / _` | '__| |/ / _` |/ _` |/ _ \
| (_| | |  |   < (_| | (_| |  __/
 \__,_|_|  |_|\_\__,_|\__,_|\___|

Open Source Marketplace For Developer Tools

Version: 0.8.62
Git Commit: 003e8716bbc987c412643af3a90cf87ef778bd2e

 🐳 arkade needs your support: https://github.com/sponsors/alexellis
gdha@n1:~/projects/pi4-logging$ ls /usr/local/bin
ark  arkade  crictl  golangci-lint  helm  k3s  k3s-killall.sh  k3s-uninstall.sh  kubectl  kube-linter
```

To upgrade arkade just re-run above command.

### Installation of loki and promtail


