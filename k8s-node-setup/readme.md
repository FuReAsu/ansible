# Kubernetes Node Setup 

This ansible playbook is to setup RedHat and Debian family nodes to run kubernetes with cri-o. The playbook only automates up to the installation of kubernetes components:

- kubeadm
- kubelet
- kubectl
- cri-o

on the nodes. You have to run the `kubeadm init` command and `kubeadm join` commands on the nodes yourself to create the cluster and add nodes to the cluster.

## Distributions

Below are the distributions that I have run it on and has confirmed to work:

- AlmaLinux 10 
- CentOS Stream 10
- Debian Bookworm
- Ubuntu 24.04

## What will the playbook do

- Clear prerequisites
    - configure NTP with chrony
    - disable firewall (because it interferes with overlay network tunnels)
    - disable SELinux on distributions that use it (still haven't figured out how to use)
    - disable swap and remove it from /etc/fstab
    - load required kernel modules and make them persistent
    - configure required sysctl conf and make them persistent
- Add CRI-O and K8S repositories
- Install CRI-O and K8S components
- Exclude CRI-O and K8S components from updates to keep them from being updated unintentionally.

> The playbook is designed for initial installs. It can't handle upgrades since they aren't tested yet. What it means is that changing the ***crio_ver*** and ***k8s_ver*** variables won't upgrade the cluster. There will be separate playbook for upgrades later.

## Variables Definition

| Variable | Description | Value | Type|
|---|---|---|---|
| ntp_server | Address of the ntp server that you intend to use | IP address or hostname | string |
| k8s_ver | Kubernetes major release version | v1.3x | string |
| crio_ver | CRI-O major release version | v1.3x | string |
| kernel_modules_to_load | Defines which kernel modules to load | list of kernel module names | list |
| sysctl_configurations | Defines which sysctl configurations to apply | dictionary of sysctl conf key value pairs | dict |

## Somethings to keep in mind

This playbook works the best with the lastest Kubernetes, CRI-O and nodes' Operating Systems. Older versions of CRI-O have used the pause image with tag `v3.8`  by default which is not aligned with the one kubelet is using so it had to be changed manually in the crio configurations. This issue nolonger exists in v1.33+ so there's no task to add the latest pause image to the crio config in the playbook.

To check the latest stable releases of Kubernetes run:

```
curl https://cdn.dl.k8s.io/release/stable.txt
```
CRI-O version is mostly inline with the Kubernetes version but just to be sure, visit their release page to check out:

[https://github.com/cri-o/cri-o/releases](https://github.com/cri-o/cri-o/releases)


