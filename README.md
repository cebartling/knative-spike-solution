# knative-spike-solution

Following https://github.com/knative/docs/blob/master/docs/install/Knative-with-Minikube.md 

https://kubernetes.io/docs/reference/kubectl/cheatsheet/


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

## Installing Knative

*CRD*: Custom resource definition

`kubectl apply --selector knative.dev/crd-install=true --filename https://github.com/knative/serving/releases/download/v0.5.2/serving.yaml`

`kubectl apply --selector knative.dev/crd-install=true --filename https://github.com/knative/build/releases/download/v0.5.0/build.yaml`

`kubectl apply --selector knative.dev/crd-install=true --filename https://github.com/knative/eventing/releases/download/v0.5.0/release.yaml`

`kubectl apply --selector knative.dev/crd-install=true --filename https://github.com/knative/eventing-sources/releases/download/v0.5.0/eventing-sources.yaml`

`kubectl apply --selector knative.dev/crd-install=true --filename https://github.com/knative/serving/releases/download/v0.5.2/monitoring.yaml`

`kubectl apply --selector knative.dev/crd-install=true --filename https://raw.githubusercontent.com/knative/serving/v0.5.2/third_party/config/build/clusterrole.yaml`





```bash
kubectl apply --filename https://github.com/knative/serving/releases/download/v0.5.2/serving.yaml \
--filename https://github.com/knative/build/releases/download/v0.5.0/build.yaml \
--filename https://github.com/knative/eventing/releases/download/v0.5.0/release.yaml \
--filename https://github.com/knative/eventing-sources/releases/download/v0.5.0/eventing-sources.yaml \
--filename https://github.com/knative/serving/releases/download/v0.5.2/monitoring.yaml \
--filename https://raw.githubusercontent.com/knative/serving/v0.5.2/third_party/config/build/clusterrole.yaml
```

```bash
namespace/knative-serving created
clusterrole.rbac.authorization.k8s.io/knative-serving-admin created
clusterrole.rbac.authorization.k8s.io/knative-serving-core created
clusterrole.rbac.authorization.k8s.io/knative-serving-istio created
serviceaccount/controller created
clusterrolebinding.rbac.authorization.k8s.io/knative-serving-controller-admin created
gateway.networking.istio.io/knative-ingress-gateway created
gateway.networking.istio.io/cluster-local-gateway created
customresourcedefinition.apiextensions.k8s.io/certificates.networking.internal.knative.dev unchanged
customresourcedefinition.apiextensions.k8s.io/clusteringresses.networking.internal.knative.dev unchanged
customresourcedefinition.apiextensions.k8s.io/configurations.serving.knative.dev unchanged
customresourcedefinition.apiextensions.k8s.io/images.caching.internal.knative.dev unchanged
customresourcedefinition.apiextensions.k8s.io/podautoscalers.autoscaling.internal.knative.dev unchanged
customresourcedefinition.apiextensions.k8s.io/revisions.serving.knative.dev unchanged
customresourcedefinition.apiextensions.k8s.io/routes.serving.knative.dev unchanged
customresourcedefinition.apiextensions.k8s.io/services.serving.knative.dev unchanged
customresourcedefinition.apiextensions.k8s.io/serverlessservices.networking.internal.knative.dev unchanged
service/activator-service created
service/controller created
service/webhook created
image.caching.internal.knative.dev/queue-proxy created
deployment.apps/activator created
service/autoscaler created
deployment.apps/autoscaler created
configmap/config-autoscaler created
configmap/config-controller created
configmap/config-defaults created
configmap/config-domain created
configmap/config-gc created
configmap/config-istio created
configmap/config-logging created
configmap/config-network created
configmap/config-observability created
deployment.apps/controller created
deployment.apps/webhook created
namespace/knative-build created
podsecuritypolicy.policy/knative-build created
clusterrole.rbac.authorization.k8s.io/knative-build-admin created
serviceaccount/build-controller created
clusterrolebinding.rbac.authorization.k8s.io/build-controller-admin created
customresourcedefinition.apiextensions.k8s.io/builds.build.knative.dev unchanged
customresourcedefinition.apiextensions.k8s.io/buildtemplates.build.knative.dev unchanged
customresourcedefinition.apiextensions.k8s.io/clusterbuildtemplates.build.knative.dev unchanged
customresourcedefinition.apiextensions.k8s.io/images.caching.internal.knative.dev configured
service/build-controller created
service/build-webhook created
image.caching.internal.knative.dev/creds-init created
image.caching.internal.knative.dev/git-init created
image.caching.internal.knative.dev/gcs-fetcher created
image.caching.internal.knative.dev/nop created
configmap/config-logging created
deployment.apps/build-controller created
deployment.apps/build-webhook created
namespace/knative-eventing created
clusterrole.rbac.authorization.k8s.io/addressable-resolver created
clusterrole.rbac.authorization.k8s.io/serving-addressable-resolver created
clusterrole.rbac.authorization.k8s.io/channel-addressable-resolver created
clusterrole.rbac.authorization.k8s.io/broker-addressable-resolver created
clusterrole.rbac.authorization.k8s.io/eventing-broker-filter created
clusterrole.rbac.authorization.k8s.io/knative-eventing-controller created
serviceaccount/eventing-controller created
serviceaccount/eventing-webhook created
clusterrole.rbac.authorization.k8s.io/knative-eventing-webhook created
clusterrolebinding.rbac.authorization.k8s.io/eventing-controller created
clusterrolebinding.rbac.authorization.k8s.io/eventing-controller-resolver created
clusterrolebinding.rbac.authorization.k8s.io/eventing-webhook created
customresourcedefinition.apiextensions.k8s.io/brokers.eventing.knative.dev unchanged
customresourcedefinition.apiextensions.k8s.io/channels.eventing.knative.dev unchanged
customresourcedefinition.apiextensions.k8s.io/clusterchannelprovisioners.eventing.knative.dev unchanged
customresourcedefinition.apiextensions.k8s.io/subscriptions.eventing.knative.dev unchanged
customresourcedefinition.apiextensions.k8s.io/triggers.eventing.knative.dev unchanged
configmap/default-channel-webhook created
service/webhook created
deployment.apps/eventing-controller created
deployment.apps/webhook created
configmap/config-logging created
clusterchannelprovisioner.eventing.knative.dev/in-memory created
clusterchannelprovisioner.eventing.knative.dev/in-memory-channel created
serviceaccount/in-memory-channel-controller created
clusterrole.rbac.authorization.k8s.io/in-memory-channel-controller created
clusterrolebinding.rbac.authorization.k8s.io/in-memory-channel-controller created
deployment.apps/in-memory-channel-controller created
serviceaccount/in-memory-channel-dispatcher created
clusterrole.rbac.authorization.k8s.io/in-memory-channel-dispatcher created
clusterrolebinding.rbac.authorization.k8s.io/in-memory-channel-dispatcher created
deployment.apps/in-memory-channel-dispatcher created
configmap/in-memory-channel-dispatcher-config-map created
namespace/knative-sources created
serviceaccount/controller-manager created
clusterrole.rbac.authorization.k8s.io/eventing-sources-controller created
clusterrolebinding.rbac.authorization.k8s.io/eventing-sources-controller created
customresourcedefinition.apiextensions.k8s.io/awssqssources.sources.eventing.knative.dev unchanged
customresourcedefinition.apiextensions.k8s.io/containersources.sources.eventing.knative.dev unchanged
customresourcedefinition.apiextensions.k8s.io/cronjobsources.sources.eventing.knative.dev unchanged
customresourcedefinition.apiextensions.k8s.io/githubsources.sources.eventing.knative.dev unchanged
customresourcedefinition.apiextensions.k8s.io/kuberneteseventsources.sources.eventing.knative.dev unchanged
service/controller created
statefulset.apps/controller-manager created
namespace/knative-monitoring created
service/elasticsearch-logging created
serviceaccount/elasticsearch-logging created
clusterrole.rbac.authorization.k8s.io/elasticsearch-logging created
clusterrolebinding.rbac.authorization.k8s.io/elasticsearch-logging created
statefulset.apps/elasticsearch-logging created
service/kibana-logging created
deployment.apps/kibana-logging created
configmap/fluentd-ds-config created
logentry.config.istio.io/requestlog created
fluentd.config.istio.io/requestloghandler created
rule.config.istio.io/requestlogtofluentd created
serviceaccount/fluentd-ds created
clusterrole.rbac.authorization.k8s.io/fluentd-ds created
clusterrolebinding.rbac.authorization.k8s.io/fluentd-ds created
service/fluentd-ds created
daemonset.apps/fluentd-ds created
configmap/grafana-dashboard-definition-istio created
configmap/grafana-dashboard-definition-mixer created
configmap/grafana-dashboard-definition-pilot created
serviceaccount/kube-state-metrics created
role.rbac.authorization.k8s.io/kube-state-metrics-resizer created
rolebinding.rbac.authorization.k8s.io/kube-state-metrics created
clusterrole.rbac.authorization.k8s.io/kube-state-metrics created
clusterrolebinding.rbac.authorization.k8s.io/kube-state-metrics created
deployment.extensions/kube-state-metrics created
service/kube-state-metrics created
configmap/grafana-dashboard-definition-kubernetes-deployment created
configmap/grafana-dashboard-definition-kubernetes-capacity-planning created
configmap/grafana-dashboard-definition-kubernetes-cluster-health created
configmap/grafana-dashboard-definition-kubernetes-cluster-status created
configmap/grafana-dashboard-definition-kubernetes-control-plane-status created
configmap/grafana-dashboard-definition-kubernetes-resource-requests created
configmap/grafana-dashboard-definition-kubernetes-nodes created
configmap/grafana-dashboard-definition-kubernetes-pods created
configmap/grafana-dashboard-definition-kubernetes-statefulset created
serviceaccount/node-exporter created
clusterrole.rbac.authorization.k8s.io/node-exporter created
clusterrolebinding.rbac.authorization.k8s.io/node-exporter created
daemonset.extensions/node-exporter created
service/node-exporter created
configmap/grafana-dashboard-definition-knative-efficiency created
configmap/grafana-dashboard-definition-knative-reconciler created
configmap/scaling-config created
configmap/grafana-dashboard-definition-knative created
configmap/grafana-datasources created
configmap/grafana-dashboards created
service/grafana created
deployment.apps/grafana created
metric.config.istio.io/revisionrequestcount created
metric.config.istio.io/revisionrequestduration created
metric.config.istio.io/revisionrequestsize created
metric.config.istio.io/revisionresponsesize created
prometheus.config.istio.io/revisionpromhandler created
rule.config.istio.io/revisionpromhttp created
configmap/prometheus-scrape-config created
service/kube-controller-manager created
service/prometheus-system-discovery created
serviceaccount/prometheus-system created
role.rbac.authorization.k8s.io/prometheus-system created
role.rbac.authorization.k8s.io/prometheus-system created
role.rbac.authorization.k8s.io/prometheus-system created
role.rbac.authorization.k8s.io/prometheus-system created
clusterrole.rbac.authorization.k8s.io/prometheus-system created
rolebinding.rbac.authorization.k8s.io/prometheus-system created
rolebinding.rbac.authorization.k8s.io/prometheus-system created
rolebinding.rbac.authorization.k8s.io/prometheus-system created
rolebinding.rbac.authorization.k8s.io/prometheus-system created
clusterrolebinding.rbac.authorization.k8s.io/prometheus-system created
service/prometheus-system-np created
statefulset.apps/prometheus-system created
service/zipkin created
deployment.apps/zipkin created
clusterrole.rbac.authorization.k8s.io/knative-serving-build created
```

### Monitor Knative components

```bash
kubectl get pods --namespace knative-serving
kubectl get pods --namespace knative-build
kubectl get pods --namespace knative-eventing
kubectl get pods --namespace knative-sources
kubectl get pods --namespace knative-monitoring
```