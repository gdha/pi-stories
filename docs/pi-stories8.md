# PI4 Stories

## Raspberry Pi 4 cluster Series - Keep your Operating System updated

When you login on one of your nodes you often see a message like the following:

```bash
Welcome to Ubuntu 20.04.1 LTS (GNU/Linux 5.4.0-1026-raspi aarch64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Thu 28 Jan 2021 12:05:56 PM CET

  System load:              0.55
  Usage of /:               18.3% of 28.40GB
  Memory usage:             14%
  Swap usage:               0%
  Temperature:              34.6 C
  Processes:                157
  Users logged in:          1
  IPv4 address for cni0:    10.42.1.1
  IPv4 address for docker0: 172.17.0.1
  IPv4 address for eth0:    192.168.0.205

 * Introducing self-healing high availability clusters in MicroK8s.
   Simple, hardened, Kubernetes for production, from RaspberryPi to DC.

     https://microk8s.io/high-availability

62 updates can be installed immediately.
0 of these updates are security updates.
To see these additional updates run: apt list --upgradable


Last login: Thu Jan 28 12:01:54 2021 from 192.168.0.41
```

Pay attention to the amount of packages ready to update:

```bash
See line "62 updates can be installed immediately"
```

Doing that for a bunch of systems is tiring/boring so let get this done via ansible.
First we need to download package information from all configured sources.

```bash
$ ansible pi -m shell -b -a "apt update"
n1 | SUCCESS | rc=0 >>
Hit:1 http://ports.ubuntu.com/ubuntu-ports focal InRelease
Get:2 http://ports.ubuntu.com/ubuntu-ports focal-updates InRelease [114 kB]
Get:3 http://ports.ubuntu.com/ubuntu-ports focal-backports InRelease [101 kB]
Get:4 http://ports.ubuntu.com/ubuntu-ports focal-security InRelease [109 kB]
Fetched 324 kB in 2s (187 kB/s)
Reading package lists...
Building dependency tree...
Reading state information...
49 packages can be upgraded. Run 'apt list --upgradable' to see them.
WARNING: apt does not have a stable CLI interface. Use with caution in scripts.

...

n5 | SUCCESS | rc=0 >>
Hit:1 http://ports.ubuntu.com/ubuntu-ports focal InRelease
Get:2 http://ports.ubuntu.com/ubuntu-ports focal-updates InRelease [114 kB]
Get:3 http://ports.ubuntu.com/ubuntu-ports focal-backports InRelease [101 kB]
Get:4 http://ports.ubuntu.com/ubuntu-ports focal-security InRelease [109 kB]
Fetched 324 kB in 2s (196 kB/s)
Reading package lists...
Building dependency tree...
Reading state information...
58 packages can be upgraded. Run 'apt list --upgradable' to see them.
WARNING: apt does not have a stable CLI interface. Use with caution in scripts
```

And, finally, to automate the installation of the packages:

```bash
$ ansible pi -m shell -b -a "apt --yes upgrade"
```
