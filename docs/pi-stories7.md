# PI4 Stories

## Raspberry Pi 4 cluster Series - YAML everywhere - what about correctness?

When you dive deep into Kubernetes you will notice you cannot go around [YAML](https://blog.stackpath.com/yaml/) [1] language. You hate it or love it, however, you better get used to it as it is part of the core of kubernetes.

Writing YAML code from scratch is not a real pleasure, therefore, having a linter would be nice to avoid the low hanging fruit errors. We found a kubernetes linter tool called "[KubeLinter](https://github.com/stackrox/kube-linter)" written in the Go language, however, there is no binary available for the Raspberry Pi4 architecture aarch64 on the [release page of kube-linter](https://github.com/stackrox/kube-linter/releases).

Therefore, we decided to build it ourselves from the sources.

### Installling the Go Language binaries

On our node *n1* we installed the Go Language with the commands:

```bash
$ sudo apt  install golang-go
$ sudo apt install make 
```

### Compiling the KubeLinter code

Download the code from the [Kube-Linter Github page](https://github.com/gdha/kube-linter) and according the "[Building from source](https://docs.kubelinter.io/#/?id=building-from-source)" documentation it would be as easy as `make build` to generate the binary:

```bash
$ git clone git@github.com:gdha/kube-linter.git
$ cd kube-linter
$ make build
...
all modules verified
+ /home/gdha/projects/kube-linter/.gobin/packr
go install github.com/gobuffalo/packr/packr
packr
Compiling Go source in ./cmd/kube-linter to bin/darwin/kube-linter
# golang.stackrox.io/kube-linter/cmd/kube-linter
/usr/lib/go-1.13/pkg/tool/linux_arm64/link: running gcc failed: exit status 1
/usr/bin/ld: unrecognized -a option `gezero_size'
collect2: error: ld returned 1 exit status

make: *** [Makefile:100: build] Error 2
```

As usual it didn't work as expected. Mind the `bin/darwin/kube-linter` compile line mentions **darwin** and that is not our architecture for RPI4.

We figured out that by tweaking the [Makefile](https://github.com/gdha/kube-linter/blob/main/Makefile#L100) [2] we could build the kube-linter to an executable.

### Installing kube-linter as /usr/local/bin/kube-linter

We update the [Makefile](https://github.com/gdha/kube-linter/blob/main/Makefile#L134) with an install rule for our binary so that we do not have to add the PATH to out `.bashrc` file.

Just run `make install` to copy the compiled binary to `/usr/local/bin/kube-linter`. To verify it works try the command:

```bash
$ kube-linter version
0.1.4-10-g5c30a676d3-dirty
```
### Testing it out on a real example

In a previous post we discussed and explained the rolling upgrade of the k3s kubernetes software on our pods. [3]

Therefore, what kind of information will the `kube-linter` produce on these YAML files?

```bash
$ kube-linter lint ../k3s-upgrade-controller/
../k3s-upgrade-controller/system-upgrade-controller.yaml: (object: system-upgrade/system-upgrade-controller apps/v1, Kind=Deployment) container "system-upgrade-controller" does not have a read-only root file system (check: no-read-only-root-fs, remediation: Set readOnlyRootFilesystem to true in your container's securityContext.)

../k3s-upgrade-controller/system-upgrade-controller.yaml: (object: system-upgrade/system-upgrade-controller apps/v1, Kind=Deployment) container "system-upgrade-controller" is not set to runAsNonRoot (check: run-as-non-root, remediation: Set runAsUser to a non-zero number, and runAsNonRoot to true, in your pod or container securityContext. See https://kubernetes.io/docs/tasks/configure-pod-container/security-context/ for more details.)

../k3s-upgrade-controller/system-upgrade-controller.yaml: (object: system-upgrade/system-upgrade-controller apps/v1, Kind=Deployment) container "system-upgrade-controller" has cpu request 0 (check: unset-cpu-requirements, remediation: Set your container's CPU requests and limits depending on its requirements. See https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/#requests-and-limits for more details.)

../k3s-upgrade-controller/system-upgrade-controller.yaml: (object: system-upgrade/system-upgrade-controller apps/v1, Kind=Deployment) container "system-upgrade-controller" has cpu limit 0 (check: unset-cpu-requirements, remediation: Set your container's CPU requests and limits depending on its requirements. See https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/#requests-and-limits for more details.)

../k3s-upgrade-controller/system-upgrade-controller.yaml: (object: system-upgrade/system-upgrade-controller apps/v1, Kind=Deployment) container "system-upgrade-controller" has memory request 0 (check: unset-memory-requirements, remediation: Set your container's memory requests and limits depending on its requirements. See https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/#requests-and-limits for more details.)

../k3s-upgrade-controller/system-upgrade-controller.yaml: (object: system-upgrade/system-upgrade-controller apps/v1, Kind=Deployment) container "system-upgrade-controller" has memory limit 0 (check: unset-memory-requirements, remediation: Set your container's memory requests and limits depending on its requirements. See https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/#requests-and-limits for more details.)

Error: found 6 lint errors
```

The recommendations are perhaps not really perfect for this example as we do need a writable file system to be able to perform an update and `root` permissions will be required as well. However, the test itself was successful as it produces a meaningfull output.

For a more profound usage of `kube-linter` see the "[KubeLinter documentation](https://docs.kubelinter.io/#/)" [4].

## References

[1] [YAML Ain't Markup Language](https://blog.stackpath.com/yaml/)

[2] [Kube-Linter GitHub fork](https://github.com/gdha/kube-linter)

[3] [CRD system-upgrade-controller](https://github.com/gdha/k3s-upgrade-controller/blob/main/system-upgrade-controller.yaml)

[4] [KubeLinter Documenation](https://docs.kubelinter.io/#/)
