# knative-spike-solution

## Getting up and running

1. Install `kubectl`. We're using the `kubectl` from the Google Cloud SDK. More information on kubectl at https://kubernetes.io/docs/reference/kubectl/overview/.
1. Install `minikube`. We're on Ubuntu Linux 18.10, so we executed the following to install the KVM2 hypervisor: 

`sudo apt install libvirt-clients libvirt-daemon-system qemu-kvm`

https://libvirt.org/

`sudo systemctl enable libvirtd.service`
`sudo systemctl start libvirtd.service`
`sudo systemctl status libvirtd.service`

```bash
sudo systemctl status libvirtd.service
● libvirtd.service - Virtualization daemon
   Loaded: loaded (/lib/systemd/system/libvirtd.service; enabled; vendor preset: enabled)
   Active: active (running) since Wed 2019-05-01 09:19:28 CDT; 4min 25s ago
     Docs: man:libvirtd(8)
           https://libvirt.org
 Main PID: 12024 (libvirtd)
    Tasks: 19 (limit: 32768)
   CGroup: /system.slice/libvirtd.service
           ├─12024 /usr/sbin/libvirtd
           ├─12506 /usr/sbin/dnsmasq --conf-file=/var/lib/libvirt/dnsmasq/default.conf --leasefile-ro --dhcp-script=/usr/lib/libvirt/libvirt_leaseshelper
           └─12507 /usr/sbin/dnsmasq --conf-file=/var/lib/libvirt/dnsmasq/default.conf --leasefile-ro --dhcp-script=/usr/lib/libvirt/libvirt_leaseshelper

May 01 09:19:28 chris-ubuntu-linux systemd[1]: Started Virtualization daemon.
May 01 09:19:30 chris-ubuntu-linux dnsmasq[12506]: started, version 2.79 cachesize 150
May 01 09:19:30 chris-ubuntu-linux dnsmasq[12506]: compile time options: IPv6 GNU-getopt DBus i18n IDN DHCP DHCPv6 no-Lua TFTP conntrack ipset auth DNSSEC loop-detect inotify
May 01 09:19:30 chris-ubuntu-linux dnsmasq-dhcp[12506]: DHCP, IP range 192.168.122.2 -- 192.168.122.254, lease time 1h
May 01 09:19:30 chris-ubuntu-linux dnsmasq-dhcp[12506]: DHCP, sockets bound exclusively to interface virbr0
May 01 09:19:30 chris-ubuntu-linux dnsmasq[12506]: reading /etc/resolv.conf
May 01 09:19:30 chris-ubuntu-linux dnsmasq[12506]: using nameserver 127.0.0.53#53
May 01 09:19:30 chris-ubuntu-linux dnsmasq[12506]: read /etc/hosts - 7 addresses
May 01 09:19:30 chris-ubuntu-linux dnsmasq[12506]: read /var/lib/libvirt/dnsmasq/default.addnhosts - 0 addresses
May 01 09:19:30 chris-ubuntu-linux dnsmasq-dhcp[12506]: read /var/lib/libvirt/dnsmasq/default.hostsfile
```

`sudo usermod -a -G libvirt $(whoami)`

`newgrp libvirt`

`curl -LO https://storage.googleapis.com/minikube/releases/latest/docker-machine-driver-kvm2 && sudo install docker-machine-driver-kvm2 /usr/local/bin/`

`curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64 && sudo install minikube-linux-amd64 /usr/local/bin/minikube`


```bash
✗ minikube start
😄  minikube v1.0.1 on linux (amd64)
💿  Downloading Minikube ISO ...
 142.88 MB / 142.88 MB [============================================] 100.00% 0s
🤹  Downloading Kubernetes v1.14.1 images in the background ...
🔥  Creating kvm2 VM (CPUs=2, Memory=2048MB, Disk=20000MB) ...
📶  "minikube" IP address is 192.168.39.3
🐳  Configuring Docker as the container runtime ...
🐳  Version of container runtime is 18.06.3-ce
⌛  Waiting for image downloads to complete ...
✨  Preparing Kubernetes environment ...
💾  Downloading kubeadm v1.14.1
💾  Downloading kubelet v1.14.1
🚜  Pulling images required by Kubernetes v1.14.1 ...
🚀  Launching Kubernetes v1.14.1 using kubeadm ... 
⌛  Waiting for pods: apiserver proxy etcd scheduler controller dns
🔑  Configuring cluster permissions ...
🤔  Verifying component health .....
💗  kubectl is now configured to use "minikube"
🏄  Done! Thank you for using minikube!
```