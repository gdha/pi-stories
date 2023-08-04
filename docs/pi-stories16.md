# PI4 Stories

## Raspberry Pi 4 cluster Series - Deploying uptime kuma with helm

### Download the GitHub sources of Uptime Kuma

We forked the original GitHub Sources of [https://github.com/k3rnelpan1c-dev/uptime-kuma-helm](https://github.com/k3rnelpan1c-dev/uptime-kuma-helm) to make our adjustments [1].

If you wonder what you can do with Uptime Kuma, then the best description found is "[Uptime Kuma is an easy-to-use self-hosted monitoring tool.](https://github.com/louislam/uptime-kuma)" - a simple example is to watch a NAS system with a simple `ping`:

![](img/uptime-kuma-nas.png)

### The container image content

The [directory](https://github.com/gdha/pi4-uptime-kuma-helm/tree/main/container) contains the Dockerfile to build the container image.

Within Uptime Kuma we can use [apprise](https://github.com/caronc/apprise) which is an interesting tool to send out notifications that we will be using outside this project as well.

### Build the Uptime Kuma for ARM64 architecture

We have set-up GitHub Actions to build a container image for [Uptime Kuma for x86 based images](https://github.com/gdha/pi4-uptime-kuma-helm/pkgs/container/uptime-kuma).

However, as we want to have an image for ARM64 we need to build it ourselves. We made a script `container/build.sh` to build the ARM64 based image:

```bash
REL=${1:-v1.0}
cat ~/.ghcr-token | docker login ghcr.io -u gdha --password-stdin
echo "Building pi4-uptime-kuma:$REL"
docker build --tag ghcr.io/gdha/pi4-uptime-kuma:$REL .
docker tag ghcr.io/gdha/pi4-uptime-kuma:$REL ghcr.io/gdha/pi4-uptime-kuma:latest
echo "Pushing pi4-uptime-kuma:$REL to GitHub Docker Container registry"
docker push ghcr.io/gdha/pi4-uptime-kuma:$REL
```

You can find the latest [v1.0.5 image for ARM64](https://github.com/users/gdha/packages/container/package/pi4-uptime-kuma)



### Introducing the Helm chart of the Uptime Kuma application

Well this will be the first introduction with Helm charts [2]. [Helm](https://helm.sh/) is a package manager for Kubernetes [3] and we were interesting to have some more knowledge about it.

The helm chart of uptime kuma [4]:

```bash
apiVersion: v2
name: uptime-kuma
description: A Helm chart for Uptime Kuma. A fancy self-hosted monitoring tool.

# A chart can be either an 'application' or a 'library' chart.
#
# Application charts are a collection of templates that can be packaged into versioned archives
# to be deployed.
#
# Library charts provide useful utilities or functions for the chart developer. They're included as
# a dependency of application charts to inject those utilities and functions into the rendering
# pipeline. Library charts do not define any templates and therefore cannot be deployed.
type: application

# This is the chart version. This version number should be incremented each time you make changes
# to the chart and its templates, including the app version.
# Versions are expected to follow Semantic Versioning (https://semver.org/)
version: 1.0.5

# This is the version number of the (container) application being deployed. This version number should be
# incremented each time you make changes to the application. Versions are not expected to
# follow Semantic Versioning. They should reflect the version the application is using.
# It is recommended to use it with quotes.
# Use: docker image ls  (to find the latest one)
appVersion: "v1.0.5"

icon: https://github.com/louislam/uptime-kuma/blob/master/public/apple-touch-icon-precomposed.png

sources:
- https://github.com/k3rnelpan1c-dev/uptime-kuma-helm
- https://github.com/louislam/uptime-kuma
- https://helm.sh/docs/topics/charts/
```



### References

[1] [https://github.com/gdha/pi4-uptime-kuma-helm/](https://github.com/gdha/pi4-uptime-kuma-helm/tree/main)

[2] [How to Create Helm Chart (Comprehensive Beginners Guide)](https://devopscube.com/create-helm-chart/)

[3] [Introduction to Helm and Creating your first Helm Chart](https://devopslearning.medium.com/introduction-to-helm-and-creating-your-first-helm-chart-53c5df736bf2)

[4] [Chart.yaml of Uptime Kuma](https://github.com/gdha/pi4-uptime-kuma-helm/blob/main/uptime-kuma/Chart.yaml)
