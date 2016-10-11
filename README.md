Ansible Role: Bootstrap
=======================

Build status for this role: [![Build Status](https://travis-ci.org/PeterMosmans/ansible-role-bootstrap.svg)](https://travis-ci.org/PeterMosmans/ansible-role-bootstrap)


This role bootstraps a (new) server into existence. It installs and tightens a firewall, hardens SSH and modifies GRUB. The main focus is on **hardening a fresh server installation**.


Requirements
------------

None.

Role Variables
--------------

Available variables are listed below, along with default values


**bootstrap_directories**: A list with directories that will be created along with permissions, owner and groups.
Example:
```
bootstrap_directories:
 - path: /var/git
   mode: 2660
   owner: root
   group: git
```


**bootstrap_git_repositories**: A list with common git repositories that will be cloned.
Example:
```
bootstrap_git_repositories:
 - repo: https://github.com/PeterMosmans/security-scripts
   dest: /var/git/security-scripts
   version: master
```


**bootstrap_groups**: A list with usergroups that will be added by default. The defaults can be found in `defaults/main.yml`:
```
bootstrap_groups:
  - git
  - sudo
```


**bootstrap_users**: A nested lists with users to add, with their SSH key, and optional git repos to install (for e.g. dotfiles), and installers to run (for e.g. setting up symlinks).
Example:
```
bootstrap_users:
 - name: apenut
   comment: "Ape Nut"
   groups:
     - git
     - sudo
   shell: "/bin/bash"
   ssh_key: https://github.com/your-github-username.keys
   repos:
     - src: https://github.com/your-github-username/dotfiles
       dest: /home/apenut/.dotfiles
       version: master
   installers:
     - command: /home/apenut/.dotfiles/installer.sh
```
If you don't want to add any repositories or installer scripts, You can also leave the `repos` and `installers` variables empty:
```
boostrap_users:
 - name: apenut
   comment: "Ape Nut"
   groups:
     - git
     - sudo
   shell: "/bin/bash"
   ssh_key: https://github.com/your-github-username.keys
   repos: []
   installers: []
```


**bootstrap_packages**: A list with packages that will be installed by default. The defaults can be found in `defaults/main.yml`:
```
bootstrap_packages:
  - aptitude
  - ca-certificates
  - curl
  - git
  - locate
  - lsof
  - ntp
  - openssl
  - sudo
  - tmux
  - ufw
  - unzip
  - zsh
```


**bootstrap_packages_remove**: A list with packages that will be removed by default. The defaults can be found in `defaults/main.yml`:
```
bootstrap_packages_remove:
  - bluez
  - crda
  - cups
  - iso-codes
  - iw
  - libiw30
  - task-laptop
  - wireless-regdb
  - wireless-tools
  - wpasupplicant
```


**bootstrap_pip_packages**: A list with pip packages that will be installed globally by default. Example:
```
bootstrap_pip_packages:
  - ansible
```


**bootstrap_sudo_users**: A lists of users to grant passwordless access to sudo using the `/etc/sudoers` file. Example:
```
bootstrap_sudo_users:
  - vagrant
```


**bootstrap_ufw_tcp_allow**: A list of TCP ports that will be opened up in the firewall. It defaults to port 22 only. Example:
```
bootstrap_ufw_tcp_allow:
  - 22
  - 80
  - 443
```



**grub_settings**: A list of name / value pairs that will be applied to the GRUB config file. The defaults can be found in `defaults/main.yml`:
```
grub_settings:
  - name: "GRUB_TIMEOUT"
    value: "0"
  - name: "GRUB_RECORDFAIL_TIMEOUT"
  value: "0"
```


**hostname**: The hostname for the machine. If none is given, it will default to "bootstrapped". Example:
```
hostname: bootstrapped
```


**sshd_config**: A list of name / value pairs that will be applied to the SSH daemon configuration file. The defaults can be found in `defaults/main.yml`:
```
sshd_config:
  - name: Banner
    value: /etc/issue.ssh
  - name: PasswordAuthentication
    value: "yes"
  - name: PermitEmptyPasswords
    value: "no"
  - name: PermitRootLogin
    value: "no"
  - name: PubkeyAuthentication
    value: "yes"
  - name: RSAAuthentication
    value: "yes"
  - name: GSSAPIAuthentication
    value: "no"
  - name: UseDNS
    value: "no"
  - name: KexAlgorithms
    value: "curve25519-sha256@libssh.org,diffie-hellman-group-exchange-sha256"
  - name: HostKeyAlgorithms
    value: "HostKeyAlgorithms ssh-ed25519-cert-v01@openssh.com,ssh-rsa-cert-v01@openssh.com,ssh-ed25519,ssh-rsa"
  - name: Ciphers
    value: "chacha20-poly1305@openssh.com,aes256-gcm@openssh.com,aes128-gcm@openssh.com,aes256-ctr,aes192-ctr,aes128-ctr"
  - name: MACs
    value: "MACs hmac-sha2-512-etm@openssh.com,hmac-sha2-256-etm@openssh.com,hmac-ripemd160-etm@openssh.com,umac-128-etm@openssh.com,hmac-sha2-512,hmac-sha2-256,hmac-ripemd160,umac-128@openssh.com"
```


**sshd_config_remove**: A list of lines (name / value pairs) that will be removed from the SSH daemon configuration file. The defaults can be found in `defaults/main.yml`:
```
sshd_config_remove:
  - "HostKey /etc/ssh/ssh_host_dsa_key"
  - "HostKey /etc/ssh/ssh_host_ecdsa_key"
```


**sshd_moduli_remove**: A list of moduli values that will be removed from the /etc/ssh/moduli list. The defaults can be found in `defaults/main.yml`:
```
sshd_moduli_remove:
  - 1023
  - 1535
```


**timezone**: The timezone for the machine. The default can be found in `defaults/main.yml`:
```
timezone: Etc/UTC
```

Furthermore, the file `files/issue.ssh` will be copied to the host, and applied as SSH banner. Change the text to something that applies to you(r company).



Dependencies
------------

None.



Example Playbook
----------------
```
- hosts: all
  become: yes
  become_method: sudo
  roles:
  - role: PeterMosmans.bootstrap
  vars:
  hostname: "myhostname"
```
This example will harden SSH, configure GRUB, and name the host "myhostname"



License
-------

GPLv3


Author Information
------------------

Created by Peter Mosmans.
