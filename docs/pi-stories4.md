# PI4 Stories

## Raspberry Pi 4 cluster Series - Install awesome kubectl aliases

The article "[Awesome Kubernetes Command-Line Hacks](https://medium.com/better-programming/awesome-kubernetes-command-line-hacks-8bd3604e394f)" points out some interesting items to take in account. In summary activate the autocompletion and generate the kubectl aliases to make your life better.

### Enable kubectl autocompletion

Enable kubectl autocompletion is one of the first things you have to do to make your life a bit easier. There are two ways in which you can do this:

* Source the completion script in your `~/.bashrc` file:

```bash
echo 'source <(kubectl completion bash)' >>~/.bashrc
```

* Add the completion script to the `/etc/bash_completion.d` directory:

```bash
kubectl completion bash >/etc/bash_completion.d/kubectl
```

If you have an alias for kubectl, you can extend shell completion to work with that alias:

```bash
echo 'alias k=kubectl' >>~/.bashrc
echo 'complete -F __start_kubectl k' >>~/.bashrc
```

Above information come from [kubernetes kubectl page](https://kubernetes.io/docs/tasks/tools/install-kubectl/). If you are alone then go for the first option, if more users require these autocompletion then go for the latter option.

### Kubectl aliases

Kubectl commands can be long and hard to type over and over again, therefore, why not apply the [kubectl-aliases github project](https://github.com/ahmetb/kubectl-aliases).

## References

[1] [Awesome Kubernetes Command-Line Hacks](https://medium.com/better-programming/awesome-kubernetes-command-line-hacks-8bd3604e394f)

[2] [kubectl-aliases GitHub Source](https://github.com/ahmetb/kubectl-aliases)

## Edit history

* initial post on 30/Sep/2020
