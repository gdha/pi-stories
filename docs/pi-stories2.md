# PI4 Stories

## Raspberry Pi 4 cluster Series - Prepare for kubernetes installation

### Add account with Secure Shell keys and common software packages
It is important that my account is created on each host with the requires secure shell keys. Also, we install what we think are required software packages on each host:

- vim
- git
- rsync
- acl
- dnsutils
- dphys-swapfile
- python-is-python3
- sshpass
- ca-certificates
- curl
- gnupg-agent
- software-properties-common
- jq

To get going first install `git` on the system where you start ansible playbooks (in our case it is host n5) and afterwards, clone the playbook [pi4-cluster-ansible-roles](https://github.com/gdha/pi4-cluster-ansible-roles). Do check the `inventory.yml` file and add you preferences.

The playbook will also disable swap on each host, which is a requirement for kubernetes.

### Compile ARM side libraries for interfacing to Raspberry Pi GPU
Download the source code from [2](http://github.com/raspberrypi/userland). Compile and install these binaries on the host from where you will use the playbook. In my case its was host n5 (do not forget to install `ansible`).


### Run the ansible playbook provision.yml
```bash
$ ansible-playbook provision.yml -k --vault-password-file .my_password
SSH password: 

PLAY [all] *************************************************************************************************************************************

TASK [Gathering Facts] *************************************************************************************************************************
ok: [n5]
ok: [n3]
ok: [n2]
ok: [n4]
ok: [n1]

TASK [user : debug] ****************************************************************************************************************************
ok: [n1] => {
    "msg": "creating user gdha and OpenSSH public key distribution"
}
ok: [n2] => {
    "msg": "creating user gdha and OpenSSH public key distribution"
}
ok: [n3] => {
    "msg": "creating user gdha and OpenSSH public key distribution"
}
ok: [n4] => {
    "msg": "creating user gdha and OpenSSH public key distribution"
}
ok: [n5] => {
    "msg": "creating user gdha and OpenSSH public key distribution"
}

TASK [user : Creating admin group] *************************************************************************************************************
ok: [n3]
ok: [n1]
ok: [n4]
ok: [n2]
ok: [n5]

TASK [user : Add group gdha (1001)] ************************************************************************************************************
ok: [n1]
ok: [n2]
ok: [n3]
ok: [n4]
ok: [n5]

TASK [user : Add user 'gdha' with specific uid (1001) and group 'gdha' (1001) and secondary group 'admin'] *************************************
ok: [n5]
ok: [n2]
ok: [n3]
ok: [n1]
ok: [n4]

TASK [user : Create the /home/gdha/.ssh directory] *********************************************************************************************
ok: [n2]
ok: [n1]
ok: [n3]
ok: [n4]
ok: [n5]

TASK [user : Copy /home/gdha/.ssh/id_rsa.pub to remote nodes] **********************************************************************************
ok: [n2]
ok: [n3]
ok: [n1]
ok: [n4]
ok: [n5]

TASK [user : Append public ssh key of gdha to authorized_keys] *********************************************************************************
changed: [n1] => (item=/home/gdha/.ssh/id_rsa.pub)
changed: [n4] => (item=/home/gdha/.ssh/id_rsa.pub)
changed: [n2] => (item=/home/gdha/.ssh/id_rsa.pub)
changed: [n3] => (item=/home/gdha/.ssh/id_rsa.pub)
ok: [n5] => (item=/home/gdha/.ssh/id_rsa.pub)

TASK [user : Create /etc/sudoers.d/gdha-sudoers file] ******************************************************************************************
ok: [n1]
ok: [n2]
ok: [n3]
ok: [n4]
ok: [n5]

TASK [base : Set /etc/hosts for other nodes] ***************************************************************************************************
ok: [n1] => (item=n2)
ok: [n2] => (item=n1)
ok: [n3] => (item=n1)
ok: [n4] => (item=n1)
ok: [n5] => (item=n1)
ok: [n1] => (item=n3)
ok: [n2] => (item=n3)
ok: [n3] => (item=n2)
ok: [n4] => (item=n2)
ok: [n5] => (item=n2)
ok: [n1] => (item=n4)
ok: [n2] => (item=n4)
ok: [n4] => (item=n3)
ok: [n3] => (item=n4)
ok: [n5] => (item=n3)
ok: [n1] => (item=n5)
ok: [n2] => (item=n5)
ok: [n4] => (item=n5)
ok: [n3] => (item=n5)
ok: [n5] => (item=n4)

TASK [base : Install common packages] **********************************************************************************************************
ok: [n5]
changed: [n2]
changed: [n3]
changed: [n4]
changed: [n1]

TASK [base : Set default locale] ***************************************************************************************************************
ok: [n1]
ok: [n2]
ok: [n3]
ok: [n4]
ok: [n5]

TASK [base : Enable locale] ********************************************************************************************************************
ok: [n2]
ok: [n1]
ok: [n3]
ok: [n4]
ok: [n5]

TASK [rpi : Create directory /opt/vc/bin] ******************************************************************************************************
ok: [n1]
ok: [n2]
ok: [n3]
ok: [n4]
ok: [n5]

TASK [rpi : copy tvservice to /opt/vc/bin] *****************************************************************************************************
ok: [n1]
ok: [n2]
ok: [n3]
ok: [n4]
ok: [n5]

TASK [rpi : Create the /opt/vc/lib directory] **************************************************************************************************
ok: [n1]
ok: [n2]
ok: [n3]
ok: [n4]
ok: [n5]

TASK [rpi : Copy /opt/vc/lib/libvchiq_arm.so] **************************************************************************************************
ok: [n1]
ok: [n2]
ok: [n3]
ok: [n4]
ok: [n5]

TASK [rpi : Copy /opt/vc/lib/libvcos.so] *******************************************************************************************************
ok: [n1]
ok: [n2]
ok: [n3]
ok: [n4]
ok: [n5]

TASK [rpi : add /opt/vc/lib to /etc/ld.so.conf file] *******************************************************************************************
ok: [n1]
ok: [n2]
ok: [n3]
ok: [n4]
ok: [n5]

TASK [rpi : Run ldconfig] **********************************************************************************************************************
changed: [n2]
changed: [n1]
changed: [n3]
changed: [n4]
changed: [n5]

TASK [rpi : Check if HDMI is on] ***************************************************************************************************************
ok: [n1]
ok: [n2]
ok: [n3]
ok: [n4]
ok: [n5]

TASK [rpi : Switch off HDMI] *******************************************************************************************************************
changed: [n1]
changed: [n2]
changed: [n3]
changed: [n4]
changed: [n5]

TASK [rpi : Ensure rc.local exists] ************************************************************************************************************
changed: [n1]
changed: [n2]
changed: [n3]
changed: [n4]
changed: [n5]

TASK [rpi : Switch off HDMI on boot] ***********************************************************************************************************
ok: [n1]
ok: [n2]
ok: [n3]
ok: [n4]
ok: [n5]

TASK [os : Set hostname] ***********************************************************************************************************************
ok: [n2]
ok: [n3]
ok: [n1]
ok: [n4]
ok: [n5]

TASK [os : Set /etc/hosts hostname] ************************************************************************************************************
ok: [n1]
ok: [n2]
ok: [n3]
ok: [n4]
ok: [n5]

TASK [os : Check if swap is enabled] ***********************************************************************************************************
changed: [n1]
changed: [n2]
changed: [n3]
changed: [n4]
changed: [n5]

TASK [os : Disable swap] ***********************************************************************************************************************
changed: [n1] => (item=dphys-swapfile swapoff)
changed: [n2] => (item=dphys-swapfile swapoff)
changed: [n3] => (item=dphys-swapfile swapoff)
changed: [n4] => (item=dphys-swapfile swapoff)
changed: [n5] => (item=dphys-swapfile swapoff)
changed: [n1] => (item=dphys-swapfile uninstall)
changed: [n2] => (item=dphys-swapfile uninstall)
changed: [n3] => (item=dphys-swapfile uninstall)
changed: [n4] => (item=dphys-swapfile uninstall)
changed: [n5] => (item=dphys-swapfile uninstall)
changed: [n2] => (item=update-rc.d dphys-swapfile disable)
changed: [n3] => (item=update-rc.d dphys-swapfile disable)
changed: [n1] => (item=update-rc.d dphys-swapfile disable)
changed: [n4] => (item=update-rc.d dphys-swapfile disable)
changed: [n5] => (item=update-rc.d dphys-swapfile disable)

TASK [os : Add users to passwordless sudoers.] *************************************************************************************************
ok: [n1] => (item=gdha)
ok: [n2] => (item=gdha)
ok: [n3] => (item=gdha)
ok: [n4] => (item=gdha)
ok: [n5] => (item=gdha)
ok: [n1] => (item=ubuntu)
ok: [n2] => (item=ubuntu)
ok: [n3] => (item=ubuntu)
ok: [n4] => (item=ubuntu)
ok: [n5] => (item=ubuntu)

TASK [ssh : Check if ssh key exists] ***********************************************************************************************************
ok: [n1]
ok: [n2]
ok: [n3]
ok: [n4]
ok: [n5]

TASK [ssh : Create .ssh directory] *************************************************************************************************************
ok: [n1]
ok: [n2]
ok: [n3]
ok: [n4]
ok: [n5]

TASK [ssh : Generate ssh key] ******************************************************************************************************************
skipping: [n5]
changed: [n1]
changed: [n3]
changed: [n2]
changed: [n4]

TASK [ssh : Slurp public keys from all nodes] **************************************************************************************************
ok: [n1]
ok: [n3]
ok: [n2]
ok: [n4]
ok: [n5]

TASK [ssh : Copy public keys of all nodes into authorized_keys] ********************************************************************************
skipping: [n1] => (item=n1) 
skipping: [n1] => (item=n2) 
skipping: [n1] => (item=n3) 
skipping: [n1] => (item=n4) 
skipping: [n1] => (item=n5) 
skipping: [n2] => (item=n1) 
skipping: [n2] => (item=n2) 
skipping: [n2] => (item=n3) 
skipping: [n2] => (item=n4) 
skipping: [n2] => (item=n5) 
skipping: [n3] => (item=n1) 
skipping: [n3] => (item=n2) 
skipping: [n3] => (item=n3) 
skipping: [n3] => (item=n4) 
skipping: [n3] => (item=n5) 
skipping: [n4] => (item=n1) 
skipping: [n4] => (item=n2) 
skipping: [n4] => (item=n3) 
skipping: [n4] => (item=n4) 
skipping: [n4] => (item=n5) 
skipping: [n5] => (item=n1) 
skipping: [n5] => (item=n2) 
skipping: [n5] => (item=n3) 
skipping: [n5] => (item=n4) 
skipping: [n5] => (item=n5) 

TASK [ssh : Copy local public key to authorized_keys] ******************************************************************************************
ok: [n1]
ok: [n2]
ok: [n3]
ok: [n4]
ok: [n5]

TASK [ssh : Slurp host keys from all nodes] ****************************************************************************************************
ok: [n1]
ok: [n2]
ok: [n3]
ok: [n4]
ok: [n5]

TASK [ssh : Insert all nodes into global known_hosts] ******************************************************************************************
ok: [n1] => (item=n1)
ok: [n2] => (item=n1)
ok: [n3] => (item=n1)
ok: [n4] => (item=n1)
ok: [n5] => (item=n1)
ok: [n2] => (item=n2)
ok: [n3] => (item=n2)
ok: [n1] => (item=n2)
ok: [n4] => (item=n2)
ok: [n5] => (item=n2)
ok: [n2] => (item=n3)
ok: [n3] => (item=n3)
ok: [n4] => (item=n3)
ok: [n1] => (item=n3)
ok: [n5] => (item=n3)
ok: [n2] => (item=n4)
ok: [n3] => (item=n4)
ok: [n4] => (item=n4)
ok: [n1] => (item=n4)
ok: [n5] => (item=n4)
ok: [n2] => (item=n5)
ok: [n3] => (item=n5)
ok: [n4] => (item=n5)
ok: [n1] => (item=n5)
ok: [n5] => (item=n5)

TASK [ssh : Secure SSH configuration] **********************************************************************************************************
ok: [n1] => (item={'regexp': '^[# \\t]*Port', 'line': 'Port 22'})
ok: [n2] => (item={'regexp': '^[# \\t]*Port', 'line': 'Port 22'})
ok: [n3] => (item={'regexp': '^[# \\t]*Port', 'line': 'Port 22'})
ok: [n4] => (item={'regexp': '^[# \\t]*Port', 'line': 'Port 22'})
ok: [n5] => (item={'regexp': '^[# \\t]*Port', 'line': 'Port 22'})
ok: [n1] => (item={'regexp': '^[# \\t]*PasswordAuthentication', 'line': 'PasswordAuthentication yes'})
ok: [n2] => (item={'regexp': '^[# \\t]*PasswordAuthentication', 'line': 'PasswordAuthentication yes'})
ok: [n3] => (item={'regexp': '^[# \\t]*PasswordAuthentication', 'line': 'PasswordAuthentication yes'})
ok: [n4] => (item={'regexp': '^[# \\t]*PasswordAuthentication', 'line': 'PasswordAuthentication yes'})
ok: [n5] => (item={'regexp': '^[# \\t]*PasswordAuthentication', 'line': 'PasswordAuthentication yes'})
ok: [n1] => (item={'regexp': '^[# \\t]*PermitRootLogin', 'line': 'PermitRootLogin no'})
ok: [n2] => (item={'regexp': '^[# \\t]*PermitRootLogin', 'line': 'PermitRootLogin no'})
ok: [n3] => (item={'regexp': '^[# \\t]*PermitRootLogin', 'line': 'PermitRootLogin no'})
ok: [n4] => (item={'regexp': '^[# \\t]*PermitRootLogin', 'line': 'PermitRootLogin no'})
ok: [n5] => (item={'regexp': '^[# \\t]*PermitRootLogin', 'line': 'PermitRootLogin no'})
ok: [n1] => (item={'regexp': '^[# \\t]*UseDNS', 'line': 'UseDNS no'})
ok: [n2] => (item={'regexp': '^[# \\t]*UseDNS', 'line': 'UseDNS no'})
ok: [n3] => (item={'regexp': '^[# \\t]*UseDNS', 'line': 'UseDNS no'})
ok: [n4] => (item={'regexp': '^[# \\t]*UseDNS', 'line': 'UseDNS no'})
ok: [n5] => (item={'regexp': '^[# \\t]*UseDNS', 'line': 'UseDNS no'})
ok: [n1] => (item={'regexp': '^[# \\t]*PermitEmptyPasswords', 'line': 'PermitEmptyPasswords no'})
ok: [n2] => (item={'regexp': '^[# \\t]*PermitEmptyPasswords', 'line': 'PermitEmptyPasswords no'})
ok: [n3] => (item={'regexp': '^[# \\t]*PermitEmptyPasswords', 'line': 'PermitEmptyPasswords no'})
ok: [n4] => (item={'regexp': '^[# \\t]*PermitEmptyPasswords', 'line': 'PermitEmptyPasswords no'})
ok: [n1] => (item={'regexp': '^[# \\t]*ChallengeResponseAuthentication', 'line': 'ChallengeResponseAuthentication no'})
ok: [n5] => (item={'regexp': '^[# \\t]*PermitEmptyPasswords', 'line': 'PermitEmptyPasswords no'})
ok: [n2] => (item={'regexp': '^[# \\t]*ChallengeResponseAuthentication', 'line': 'ChallengeResponseAuthentication no'})
ok: [n3] => (item={'regexp': '^[# \\t]*ChallengeResponseAuthentication', 'line': 'ChallengeResponseAuthentication no'})
ok: [n4] => (item={'regexp': '^[# \\t]*ChallengeResponseAuthentication', 'line': 'ChallengeResponseAuthentication no'})
ok: [n1] => (item={'regexp': '^[# \\t]*GSSAPIAuthentication', 'line': 'GSSAPIAuthentication no'})
ok: [n2] => (item={'regexp': '^[# \\t]*GSSAPIAuthentication', 'line': 'GSSAPIAuthentication no'})
ok: [n5] => (item={'regexp': '^[# \\t]*ChallengeResponseAuthentication', 'line': 'ChallengeResponseAuthentication no'})
ok: [n3] => (item={'regexp': '^[# \\t]*GSSAPIAuthentication', 'line': 'GSSAPIAuthentication no'})
ok: [n4] => (item={'regexp': '^[# \\t]*GSSAPIAuthentication', 'line': 'GSSAPIAuthentication no'})
ok: [n1] => (item={'regexp': '^[# \\t]*X11Forwarding', 'line': 'X11Forwarding no'})
ok: [n2] => (item={'regexp': '^[# \\t]*X11Forwarding', 'line': 'X11Forwarding no'})
ok: [n5] => (item={'regexp': '^[# \\t]*GSSAPIAuthentication', 'line': 'GSSAPIAuthentication no'})
ok: [n3] => (item={'regexp': '^[# \\t]*X11Forwarding', 'line': 'X11Forwarding no'})
ok: [n4] => (item={'regexp': '^[# \\t]*X11Forwarding', 'line': 'X11Forwarding no'})
ok: [n5] => (item={'regexp': '^[# \\t]*X11Forwarding', 'line': 'X11Forwarding no'})

TASK [ssh : Secure SSH hosts configuration] ****************************************************************************************************
ok: [n1] => (item={'regexp': '^[# \\t]*HashKnownHosts', 'line': 'HashKnownHosts no'})
ok: [n2] => (item={'regexp': '^[# \\t]*HashKnownHosts', 'line': 'HashKnownHosts no'})
ok: [n3] => (item={'regexp': '^[# \\t]*HashKnownHosts', 'line': 'HashKnownHosts no'})
ok: [n4] => (item={'regexp': '^[# \\t]*HashKnownHosts', 'line': 'HashKnownHosts no'})
ok: [n5] => (item={'regexp': '^[# \\t]*HashKnownHosts', 'line': 'HashKnownHosts no'})

PLAY RECAP *************************************************************************************************************************************
n1                         : ok=38   changed=8    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0   
n2                         : ok=38   changed=8    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0   
n3                         : ok=38   changed=8    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0   
n4                         : ok=38   changed=8    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0   
n5                         : ok=37   changed=5    unreachable=0    failed=0    skipped=2    rescued=0    ignored=0   

```

### References

[1] [Ansible pi4-cluster-ansible-roles playbook](https://github.com/gdha/pi4-cluster-ansible-roles)

[2] [Source code for ARM side libraries for interfacing to Raspberry Pi GPU](http://github.com/raspberrypi/userland)

### Edit history

- initial post on 09/Sep/2020
