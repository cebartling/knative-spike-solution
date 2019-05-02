# Knative on minikube

Following https://github.com/knative/docs/blob/master/docs/install/Knative-with-Minikube.md

https://kubernetes.io/docs/reference/kubectl/cheatsheet/

https://www.youtube.com/watch?v=tOH14vXQR2I


## Getting up and running

1. Install `kubectl`. We're using the `kubectl` from the Google Cloud SDK. More information on kubectl at https://kubernetes.io/docs/reference/kubectl/overview/.

1. Install the KVM2 hypervisor for Ubuntu Linux 18.10 (following the instructions at https://github.com/kubernetes/minikube/blob/master/docs/drivers.md#kvm2-driver): `sudo apt install libvirt-clients libvirt-daemon-system qemu-kvm`

    https://libvirt.org/

1. Enable and start the KVM2 hypervisor:

    ```bash
    sudo systemctl enable libvirtd.service
    sudo systemctl start libvirtd.service
    sudo systemctl status libvirtd.service
    ```

    The status of the libvirtd.service (KVM2):

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


1. Install minikube (https://github.com/kubernetes/minikube#installation): `curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64 && sudo install minikube-linux-amd64 /usr/local/bin/minikube`

1. Start minikube: `minikube start`

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
1. Stop minikube: `minikube stop`
1. Delete the factory-installed minikube configuration: `minikube delete`


## Create minikube cluster

`minikube start --memory=8192 --cpus=4 --kubernetes-version=v1.14.1 --vm-driver=kvm2 --disk-size=30g --bootstrapper=kubeadm --extra-config=apiserver.enable-admission-plugins="LimitRanger,NamespaceExists,NamespaceLifecycle,ResourceQuota,ServiceAccount,DefaultStorageClass,MutatingAdmissionWebhook"`

```bash
😄  minikube v1.0.1 on linux (amd64)
🤹  Downloading Kubernetes v1.14.1 images in the background ...
🔥  Creating kvm2 VM (CPUs=4, Memory=8192MB, Disk=30000MB) ...
📶  "minikube" IP address is 192.168.39.119
🐳  Configuring Docker as the container runtime ...
🐳  Version of container runtime is 18.06.3-ce
⌛  Waiting for image downloads to complete ...
✨  Preparing Kubernetes environment ...
    ▪ apiserver.enable-admission-plugins=LimitRanger,NamespaceExists,NamespaceLifecycle,ResourceQuota,ServiceAccount,DefaultStorageClass,MutatingAdmissionWebhook
🚜  Pulling images required by Kubernetes v1.14.1 ...
🚀  Launching Kubernetes v1.14.1 using kubeadm ...
⌛  Waiting for pods: apiserver proxy etcd scheduler controller dns
🔑  Configuring cluster permissions ...
🤔  Verifying component health .....
💗  kubectl is now configured to use "minikube"
🏄  Done! Thank you for using minikube!
```

Ensure kubectl context is pointing at this new cluster: `kubectl config current-context`

## Install riff

```bash
curl -Lo riff-linux-amd64.tgz https://github.com/projectriff/riff/releases/download/v0.3.0/riff-linux-amd64.tgz
tar xvzf riff-linux-amd64.tgz
sudo mv riff /usr/local/bin/
```

## Install Knative and Istio via riff CLI

Install Knative, watching the pods until everything is running (this could take a couple of minutes). The `--node-port` option replaces *LoadBalancer* type services with *NodePort*.

`riff system install --node-port`

```bash
Installing Istio components
Applying resources defined in: https://storage.googleapis.com/projectriff/istio/istio-v1.0.7-riff.yaml
Istio components installed

Waiting for the Istio components to start ......... all components are 'Running'

Installing Knative components
Applying resources defined in: https://storage.googleapis.com/knative-releases/build/previous/v0.5.0/build.yaml
Applying resources defined in: https://storage.googleapis.com/knative-releases/serving/previous/v0.5.0/serving.yaml
Applying resources defined in: https://raw.githubusercontent.com/knative/serving/v0.5.0/third_party/config/build/clusterrole.yaml
Applying resources defined in: https://storage.googleapis.com/knative-releases/eventing/previous/v0.4.0/eventing.yaml
Applying resources defined in: https://storage.googleapis.com/knative-releases/eventing/previous/v0.4.0/in-memory-channel.yaml
Applying resources defined in: https://storage.googleapis.com/projectriff/riff-buildtemplate/riff-cnb-clusterbuildtemplate-0.2.0.yaml
Knative components installed


riff system install completed successfully
```

## Get the status of all the pods across namespaces

`kubectl get pods --all-namespaces`

```bash
kubectl get pods --all-namespaces
NAMESPACE          NAME                                            READY   STATUS    RESTARTS   AGE
istio-system       cluster-local-gateway-5bf5488bb-vnb2d           1/1     Running   0          10m
istio-system       istio-citadel-78fd647cf-q79sj                   1/1     Running   0          10m
istio-system       istio-egressgateway-5547468b8d-5k8dt            1/1     Running   0          10m
istio-system       istio-galley-64ddbbff8b-2k6xl                   1/1     Running   0          10m
istio-system       istio-ingressgateway-686d54b4bf-fvf4q           1/1     Running   0          10m
istio-system       istio-pilot-7c86c7cb8b-x5trh                    2/2     Running   0          10m
istio-system       istio-policy-64b5ff6bd8-x5xbf                   2/2     Running   0          10m
istio-system       istio-sidecar-injector-6956c47586-tcncp         1/1     Running   0          10m
istio-system       istio-telemetry-55b97c59c4-cb2xn                2/2     Running   0          10m
knative-build      build-controller-77cbbc9888-h8frg               1/1     Running   0          8m38s
knative-build      build-webhook-64f9bbc7c9-ckbkc                  1/1     Running   0          8m38s
knative-eventing   eventing-controller-8b87fcbb5-h8c4z             1/1     Running   0          8m36s
knative-eventing   in-memory-channel-controller-c48986657-x6mxz    1/1     Running   0          8m35s
knative-eventing   in-memory-channel-dispatcher-5f68fff98d-zcchl   2/2     Running   1          8m35s
knative-eventing   webhook-6dc88dbfb4-v89br                        1/1     Running   0          8m36s
knative-serving    activator-654b696b84-cw4wp                      2/2     Running   0          8m37s
knative-serving    autoscaler-585b674986-cjqvq                     2/2     Running   0          8m37s
knative-serving    controller-54b89c944c-p2tww                     1/1     Running   0          8m37s
knative-serving    webhook-5cc857b9b8-qf4bv                        1/1     Running   0          8m37s
kube-system        coredns-fb8b8dccf-5mh52                         1/1     Running   1          17m
kube-system        coredns-fb8b8dccf-ctqww                         1/1     Running   1          17m
kube-system        etcd-minikube                                   1/1     Running   0          16m
kube-system        kube-addon-manager-minikube                     1/1     Running   0          16m
kube-system        kube-apiserver-minikube                         1/1     Running   0          16m
kube-system        kube-controller-manager-minikube                1/1     Running   0          16m
kube-system        kube-proxy-jprqh                                1/1     Running   0          17m
kube-system        kube-scheduler-minikube                         1/1     Running   0          16m
kube-system        kubernetes-dashboard-79dd6bfc48-z5rzr           1/1     Running   4          17m
kube-system        storage-provisioner                             1/1     Running   0          17m
```

## Initialize namespace on DockerHub

Use the riff CLI to initialize your namespace (if you plan on using a namespace other than default then substitute the name you want to use). This will create a serviceaccount and a secret with the provided credentials and install a buildtemplate. Replace *superduperdev* with your DockerHub username.

```bash
export DOCKER_ID=superduperdev
riff namespace init hp-devdays --docker-hub $DOCKER_ID
```

You will be prompted to provide the password.


```bash
Initializing namespace "hp-devdays"

Creating namespace "hp-devdays"
Enter password for user "superduperdev"
Creating secret "push-credentials" with basic authentication to server "https://index.docker.io/v1/" for user "superduperdev"
Creating serviceaccount "riff-build" using secret "push-credentials" in namespace "hp-devdays"
Setting default image prefix to "docker.io/superduperdev" for namespace "hp-devdays"

riff namespace init completed successfully
```

### Create new service from function

`riff function create square --git-repo https://github.com/projectriff-samples/node-square --artifact square.js --image hp-devdays/square --namespace hp-devdays --verbose`

### Remove existing service from namespace

`riff service delete square --namespace hp-devdays`



## minikube dashboard

```bash
✗ minikube dashboard
🔌  Enabling dashboard ...
🤔  Verifying dashboard health ...
🚀  Launching proxy ...
🤔  Verifying proxy health ...
🎉  Opening http://127.0.0.1:46301/api/v1/namespaces/kube-system/services/http:kubernetes-dashboard:/proxy/ in your default browser...
Opening in existing browser session.
```

## minikube delete

```bash
✗ minikube delete
🔥  Deleting "minikube" from kvm2 ...
💔  The "minikube" cluster has been deleted.
```
