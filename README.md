K3S HA ETCD Cluster
=========

High Availability k3s cluster with Embedded etcd Debian installer.

Tested on OS Debian GNU/Linux 12 (bookworm) with kernel Linux 6.1.0-23-amd64.

Virtual environment
-------------------
You need 3 master and 2 worker virtual machines.
Additionally, you need to install:
- UFW (you can use the [Debian security role](https://galaxy.ansible.com/ui/standalone/roles/madudka/debian_security/)) 
- load balancer like [Nginx](https://galaxy.ansible.com/ui/standalone/roles/madudka/nginx/), [HAProxy + KeepAlived](https://galaxy.ansible.com/ui/standalone/roles/madudka/haproxy-keepalived/) or [HAProxy + Heartbeat](https://galaxy.ansible.com/ui/standalone/roles/madudka/haproxy-heartbeat/).

Install
-------
```
ansible-galaxy install madudka.k3s_ha_etcd_cluster
```

Requirements
------------
Min ansible version: 2.16.8

The Ansible role should include the `become: true` parameter to ensure that tasks are executed with elevated privileges.
You can use any method to pass the `ansible_become_user` and `ansible_become_password` parameters.
More: https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_privilege_escalation.html

When using early versions of Ansible you need to install `community.general` collection:

```
ansible-galaxy collection install community.general
```

Example hosts file
----------
The presence of master, worker, lb, and k3s groups is mandatory.

    [master]
    vm-master-1 ansible_host=192.168.0.101
    vm-master-2 ansible_host=192.168.0.102
    vm-master-3 ansible_host=192.168.0.103
    
    [worker]
    vm-worker-1 ansible_host=192.168.0.111
    vm-worker-2 ansible_host=192.168.0.112
    
    [lb]
    vm-lb-1 ansible_host=192.168.0.191
    vm-lb-2 ansible_host=192.168.0.192
    
    [k3s:children]
    master
    worker
    
    [all:vars]
    ansible_port=22
    ansible_user=username
    ansible_ssh_private_key_file=./id_rsa_ansible 
    ansible_become_password=password

Role Variables
--------------
Default lower priority variables for this role `/defaults/main.yml`:

| Name                  | Description                  | Value example                          |
| --------------------- | ---------------------------- | -------------------------------------- |
| `hosts_path`          | Path to hosts file.          | /etc/hosts                             |
| `resolv_conf_path`    | Path to resolv.conf file.    | /etc/resolv.conf                       |
| `sysctl_conf_path`    | Path to sysctl.conf file.    | /etc/sysctl.conf                       |
| `dns_servers`         | List of DNS servers.         | - { server: nameserver, ip: 8.8.8.8 }  |
| `fstab_path`          | Path to fstab file.          | /etc/fstab                             |
| `k3s_node_token_path` | Path to k3s node token file. | /var/lib/rancher/k3s/server/node-token |
| `k3s_install_url`     | k3s installation URL.        | https://get.k3s.io                     |


Variables associated with this role `/vars/main.yml`:

| Name       | Description                                        | Value example |
| ---------- | -------------------------------------------------- | ------------- |
| `fixed_ip` | Fixed IP address of load balancer for k3s cluster. | 192.168.0.99  |

Dependencies
------------

No dependencies.

Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

    - hosts: all
      roles:
         - { role: madudka.k3s_ha_etcd_cluster }
      become: true

License
-------

GPL-3.0-only

Author Information
------------------

https://madudka.github.io/