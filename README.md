# knative-spike-solution

Following https://github.com/knative/docs/blob/master/docs/install/Knative-with-Minikube.md 

## Getting up and running

1. Install `kubectl`. We're using the `kubectl` from the Google Cloud SDK. More information on kubectl at https://kubernetes.io/docs/reference/kubectl/overview/.

1. Install the KVM2 hypervisor for Ubuntu Linux 18.10 (following the instructions at https://github.com/kubernetes/minikube/blob/master/docs/drivers.md#kvm2-driver): `sudo apt install libvirt-clients libvirt-daemon-system qemu-kvm`

    https://libvirt.org/

1. Enable and start the JVM2 hypervisor:    

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


## Create Kubernetes cluster

`minikube start --memory=8192 --cpus=4 --kubernetes-version=v1.14.1 --vm-driver=kvm2 --disk-size=30g --extra-config=apiserver.enable-admission-plugins="LimitRanger,NamespaceExists,NamespaceLifecycle,ResourceQuota,ServiceAccount,DefaultStorageClass,MutatingAdmissionWebhook"`

```bash
😄  minikube v1.0.1 on linux (amd64)
💥  Kubernetes downgrade is not supported, will continue to use v1.14.1
🤹  Downloading Kubernetes v1.14.1 images in the background ...
💡  Tip: Use 'minikube start -p <name>' to create a new cluster, or 'minikube delete' to delete this one.
🔄  Restarting existing kvm2 VM for "minikube" ...
⌛  Waiting for SSH access ...
📶  "minikube" IP address is 192.168.39.3
🐳  Configuring Docker as the container runtime ...
🐳  Version of container runtime is 18.06.3-ce
⌛  Waiting for image downloads to complete ...
✨  Preparing Kubernetes environment ...
    ▪ apiserver.enable-admission-plugins=LimitRanger,NamespaceExists,NamespaceLifecycle,ResourceQuota,ServiceAccount,DefaultStorageClass,MutatingAdmissionWebhook
🚜  Pulling images required by Kubernetes v1.14.1 ...
🔄  Relaunching Kubernetes v1.14.1 using kubeadm ... 
⌛  Waiting for pods: apiserver proxy etcd scheduler controller dns
📯  Updating kube-proxy configuration ...
🤔  Verifying component health .....
💗  kubectl is now configured to use "minikube"
🏄  Done! Thank you for using minikube!
```

## Install Istio

1. `kubectl apply --filename https://raw.githubusercontent.com/knative/serving/v0.5.2/third_party/istio-1.0.7/istio-crds.yaml && curl -L https://raw.githubusercontent.com/knative/serving/v0.5.2/third_party/istio-1.0.7/istio.yaml | sed 's/LoadBalancer/NodePort/' | kubectl apply --filename -`

```bash
customresourcedefinition.apiextensions.k8s.io/virtualservices.networking.istio.io created
customresourcedefinition.apiextensions.k8s.io/destinationrules.networking.istio.io created
customresourcedefinition.apiextensions.k8s.io/serviceentries.networking.istio.io created
customresourcedefinition.apiextensions.k8s.io/gateways.networking.istio.io created
customresourcedefinition.apiextensions.k8s.io/envoyfilters.networking.istio.io created
customresourcedefinition.apiextensions.k8s.io/policies.authentication.istio.io created
customresourcedefinition.apiextensions.k8s.io/meshpolicies.authentication.istio.io created
customresourcedefinition.apiextensions.k8s.io/httpapispecbindings.config.istio.io created
customresourcedefinition.apiextensions.k8s.io/httpapispecs.config.istio.io created
customresourcedefinition.apiextensions.k8s.io/quotaspecbindings.config.istio.io created
customresourcedefinition.apiextensions.k8s.io/quotaspecs.config.istio.io created
customresourcedefinition.apiextensions.k8s.io/rules.config.istio.io created
customresourcedefinition.apiextensions.k8s.io/attributemanifests.config.istio.io created
customresourcedefinition.apiextensions.k8s.io/bypasses.config.istio.io created
customresourcedefinition.apiextensions.k8s.io/circonuses.config.istio.io created
customresourcedefinition.apiextensions.k8s.io/deniers.config.istio.io created
customresourcedefinition.apiextensions.k8s.io/fluentds.config.istio.io created
customresourcedefinition.apiextensions.k8s.io/kubernetesenvs.config.istio.io created
customresourcedefinition.apiextensions.k8s.io/listcheckers.config.istio.io created
customresourcedefinition.apiextensions.k8s.io/memquotas.config.istio.io created
customresourcedefinition.apiextensions.k8s.io/noops.config.istio.io created
customresourcedefinition.apiextensions.k8s.io/opas.config.istio.io created
customresourcedefinition.apiextensions.k8s.io/prometheuses.config.istio.io created
customresourcedefinition.apiextensions.k8s.io/rbacs.config.istio.io created
customresourcedefinition.apiextensions.k8s.io/redisquotas.config.istio.io created
customresourcedefinition.apiextensions.k8s.io/servicecontrols.config.istio.io created
customresourcedefinition.apiextensions.k8s.io/signalfxs.config.istio.io created
customresourcedefinition.apiextensions.k8s.io/solarwindses.config.istio.io created
customresourcedefinition.apiextensions.k8s.io/stackdrivers.config.istio.io created
customresourcedefinition.apiextensions.k8s.io/statsds.config.istio.io created
customresourcedefinition.apiextensions.k8s.io/stdios.config.istio.io created
customresourcedefinition.apiextensions.k8s.io/apikeys.config.istio.io created
customresourcedefinition.apiextensions.k8s.io/authorizations.config.istio.io created
customresourcedefinition.apiextensions.k8s.io/checknothings.config.istio.io created
customresourcedefinition.apiextensions.k8s.io/kuberneteses.config.istio.io created
customresourcedefinition.apiextensions.k8s.io/listentries.config.istio.io created
customresourcedefinition.apiextensions.k8s.io/logentries.config.istio.io created
customresourcedefinition.apiextensions.k8s.io/edges.config.istio.io created
customresourcedefinition.apiextensions.k8s.io/metrics.config.istio.io created
customresourcedefinition.apiextensions.k8s.io/quotas.config.istio.io created
customresourcedefinition.apiextensions.k8s.io/reportnothings.config.istio.io created
customresourcedefinition.apiextensions.k8s.io/servicecontrolreports.config.istio.io created
customresourcedefinition.apiextensions.k8s.io/tracespans.config.istio.io created
customresourcedefinition.apiextensions.k8s.io/rbacconfigs.rbac.istio.io created
customresourcedefinition.apiextensions.k8s.io/serviceroles.rbac.istio.io created
customresourcedefinition.apiextensions.k8s.io/servicerolebindings.rbac.istio.io created
customresourcedefinition.apiextensions.k8s.io/adapters.config.istio.io created
customresourcedefinition.apiextensions.k8s.io/instances.config.istio.io created
customresourcedefinition.apiextensions.k8s.io/templates.config.istio.io created
customresourcedefinition.apiextensions.k8s.io/handlers.config.istio.io created
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0namespace/istio-system created
configmap/istio-galley-configuration created
configmap/istio-statsd-prom-bridge created
configmap/istio-security-custom-resources created
configmap/istio created
configmap/istio-sidecar-injector created
serviceaccount/istio-galley-service-account created
serviceaccount/istio-egressgateway-service-account created
100  116k  100  116k    0     0   166k      0 --:--:-- --:--:-- --:--:--  166k
serviceaccount/istio-ingressgateway-service-account created
serviceaccount/istio-mixer-service-account created
serviceaccount/istio-pilot-service-account created
serviceaccount/istio-cleanup-secrets-service-account created
clusterrole.rbac.authorization.k8s.io/istio-cleanup-secrets-istio-system created
clusterrolebinding.rbac.authorization.k8s.io/istio-cleanup-secrets-istio-system created
job.batch/istio-cleanup-secrets created
serviceaccount/istio-security-post-install-account created
clusterrole.rbac.authorization.k8s.io/istio-security-post-install-istio-system created
clusterrolebinding.rbac.authorization.k8s.io/istio-security-post-install-role-binding-istio-system created
job.batch/istio-security-post-install created
serviceaccount/istio-citadel-service-account created
serviceaccount/istio-sidecar-injector-service-account created
customresourcedefinition.apiextensions.k8s.io/virtualservices.networking.istio.io unchanged
customresourcedefinition.apiextensions.k8s.io/destinationrules.networking.istio.io unchanged
customresourcedefinition.apiextensions.k8s.io/serviceentries.networking.istio.io unchanged
customresourcedefinition.apiextensions.k8s.io/gateways.networking.istio.io unchanged
customresourcedefinition.apiextensions.k8s.io/envoyfilters.networking.istio.io unchanged
customresourcedefinition.apiextensions.k8s.io/httpapispecbindings.config.istio.io unchanged
customresourcedefinition.apiextensions.k8s.io/httpapispecs.config.istio.io unchanged
customresourcedefinition.apiextensions.k8s.io/quotaspecbindings.config.istio.io unchanged
customresourcedefinition.apiextensions.k8s.io/quotaspecs.config.istio.io unchanged
customresourcedefinition.apiextensions.k8s.io/rules.config.istio.io unchanged
customresourcedefinition.apiextensions.k8s.io/attributemanifests.config.istio.io unchanged
customresourcedefinition.apiextensions.k8s.io/bypasses.config.istio.io unchanged
customresourcedefinition.apiextensions.k8s.io/circonuses.config.istio.io unchanged
customresourcedefinition.apiextensions.k8s.io/deniers.config.istio.io unchanged
customresourcedefinition.apiextensions.k8s.io/fluentds.config.istio.io unchanged
customresourcedefinition.apiextensions.k8s.io/kubernetesenvs.config.istio.io unchanged
customresourcedefinition.apiextensions.k8s.io/listcheckers.config.istio.io unchanged
customresourcedefinition.apiextensions.k8s.io/memquotas.config.istio.io unchanged
customresourcedefinition.apiextensions.k8s.io/noops.config.istio.io unchanged
customresourcedefinition.apiextensions.k8s.io/opas.config.istio.io unchanged
customresourcedefinition.apiextensions.k8s.io/prometheuses.config.istio.io unchanged
customresourcedefinition.apiextensions.k8s.io/rbacs.config.istio.io unchanged
customresourcedefinition.apiextensions.k8s.io/redisquotas.config.istio.io unchanged
customresourcedefinition.apiextensions.k8s.io/servicecontrols.config.istio.io unchanged
customresourcedefinition.apiextensions.k8s.io/signalfxs.config.istio.io unchanged
customresourcedefinition.apiextensions.k8s.io/solarwindses.config.istio.io unchanged
customresourcedefinition.apiextensions.k8s.io/stackdrivers.config.istio.io unchanged
customresourcedefinition.apiextensions.k8s.io/statsds.config.istio.io unchanged
customresourcedefinition.apiextensions.k8s.io/stdios.config.istio.io unchanged
customresourcedefinition.apiextensions.k8s.io/apikeys.config.istio.io unchanged
customresourcedefinition.apiextensions.k8s.io/authorizations.config.istio.io unchanged
customresourcedefinition.apiextensions.k8s.io/checknothings.config.istio.io unchanged
customresourcedefinition.apiextensions.k8s.io/kuberneteses.config.istio.io unchanged
customresourcedefinition.apiextensions.k8s.io/listentries.config.istio.io unchanged
customresourcedefinition.apiextensions.k8s.io/logentries.config.istio.io unchanged
customresourcedefinition.apiextensions.k8s.io/edges.config.istio.io unchanged
customresourcedefinition.apiextensions.k8s.io/metrics.config.istio.io unchanged
customresourcedefinition.apiextensions.k8s.io/quotas.config.istio.io unchanged
customresourcedefinition.apiextensions.k8s.io/reportnothings.config.istio.io unchanged
customresourcedefinition.apiextensions.k8s.io/servicecontrolreports.config.istio.io unchanged
customresourcedefinition.apiextensions.k8s.io/tracespans.config.istio.io unchanged
customresourcedefinition.apiextensions.k8s.io/rbacconfigs.rbac.istio.io unchanged
customresourcedefinition.apiextensions.k8s.io/serviceroles.rbac.istio.io unchanged
customresourcedefinition.apiextensions.k8s.io/servicerolebindings.rbac.istio.io unchanged
customresourcedefinition.apiextensions.k8s.io/adapters.config.istio.io unchanged
customresourcedefinition.apiextensions.k8s.io/instances.config.istio.io unchanged
customresourcedefinition.apiextensions.k8s.io/templates.config.istio.io unchanged
customresourcedefinition.apiextensions.k8s.io/handlers.config.istio.io unchanged
clusterrole.rbac.authorization.k8s.io/istio-galley-istio-system created
clusterrole.rbac.authorization.k8s.io/istio-egressgateway-istio-system created
clusterrole.rbac.authorization.k8s.io/istio-ingressgateway-istio-system created
clusterrole.rbac.authorization.k8s.io/istio-mixer-istio-system created
clusterrole.rbac.authorization.k8s.io/istio-pilot-istio-system created
clusterrole.rbac.authorization.k8s.io/istio-citadel-istio-system created
clusterrole.rbac.authorization.k8s.io/istio-sidecar-injector-istio-system created
clusterrolebinding.rbac.authorization.k8s.io/istio-galley-admin-role-binding-istio-system created
clusterrolebinding.rbac.authorization.k8s.io/istio-egressgateway-istio-system created
clusterrolebinding.rbac.authorization.k8s.io/istio-ingressgateway-istio-system created
clusterrolebinding.rbac.authorization.k8s.io/istio-mixer-admin-role-binding-istio-system created
clusterrolebinding.rbac.authorization.k8s.io/istio-pilot-istio-system created
clusterrolebinding.rbac.authorization.k8s.io/istio-citadel-istio-system created
clusterrolebinding.rbac.authorization.k8s.io/istio-sidecar-injector-admin-role-binding-istio-system created
service/istio-galley created
service/istio-egressgateway created
service/istio-ingressgateway created
service/istio-policy created
service/istio-telemetry created
service/istio-pilot created
service/istio-citadel created
service/istio-sidecar-injector created
deployment.extensions/istio-galley created
deployment.extensions/istio-egressgateway created
deployment.extensions/istio-ingressgateway created
deployment.extensions/istio-policy created
deployment.extensions/istio-telemetry created
deployment.extensions/istio-pilot created
deployment.extensions/istio-citadel created
deployment.extensions/istio-sidecar-injector created
gateway.networking.istio.io/istio-autogenerated-k8s-ingress created
horizontalpodautoscaler.autoscaling/istio-egressgateway created
horizontalpodautoscaler.autoscaling/istio-ingressgateway created
horizontalpodautoscaler.autoscaling/istio-policy created
horizontalpodautoscaler.autoscaling/istio-telemetry created
horizontalpodautoscaler.autoscaling/istio-pilot created
mutatingwebhookconfiguration.admissionregistration.k8s.io/istio-sidecar-injector created
attributemanifest.config.istio.io/istioproxy created
attributemanifest.config.istio.io/kubernetes created
stdio.config.istio.io/handler created
logentry.config.istio.io/accesslog created
logentry.config.istio.io/tcpaccesslog created
rule.config.istio.io/stdio created
rule.config.istio.io/stdiotcp created
metric.config.istio.io/requestcount created
metric.config.istio.io/requestduration created
metric.config.istio.io/requestsize created
metric.config.istio.io/responsesize created
metric.config.istio.io/tcpbytesent created
metric.config.istio.io/tcpbytereceived created
prometheus.config.istio.io/handler created
rule.config.istio.io/promhttp created
rule.config.istio.io/promtcp created
kubernetesenv.config.istio.io/handler created
rule.config.istio.io/kubeattrgenrulerule created
rule.config.istio.io/tcpkubeattrgenrulerule created
kubernetes.config.istio.io/attributes created
destinationrule.networking.istio.io/istio-policy created
destinationrule.networking.istio.io/istio-telemetry created
serviceaccount/cluster-local-gateway-service-account created
clusterrole.rbac.authorization.k8s.io/cluster-local-gateway-istio-system created
clusterrolebinding.rbac.authorization.k8s.io/cluster-local-gateway-istio-system created
service/cluster-local-gateway created
deployment.extensions/cluster-local-gateway created
horizontalpodautoscaler.autoscaling/cluster-local-gateway created
```

`kubectl label namespace default istio-injection=enabled`

## Get the status of the pods in the istio-system namespace 

`kubectl get pods --namespace istio-system`

```bash
✗ kubectl get pods --namespace istio-system
NAME                                      READY   STATUS      RESTARTS   AGE
cluster-local-gateway-5bf5488bb-xfljk     1/1     Running     0          4m45s
istio-citadel-78fd647cf-nm7rl             1/1     Running     0          4m46s
istio-cleanup-secrets-k9q47               0/1     Completed   0          4m47s
istio-egressgateway-5547468b8d-d9jbr      1/1     Running     0          4m46s
istio-galley-64ddbbff8b-p72sx             1/1     Running     0          4m46s
istio-ingressgateway-686d54b4bf-5z749     1/1     Running     0          4m46s
istio-pilot-7bd454bf5c-fxbgq              2/2     Running     0          4m46s
istio-pilot-7bd454bf5c-wdcls              2/2     Running     0          4m30s
istio-pilot-7bd454bf5c-z5srh              2/2     Running     0          4m30s
istio-policy-64b5ff6bd8-dtvst             2/2     Running     0          4m46s
istio-security-post-install-jrr4t         0/1     Completed   0          4m47s
istio-sidecar-injector-6956c47586-p4brc   1/1     Running     0          4m46s
istio-telemetry-55b97c59c4-wr4k6          2/2     Running     0          4m46s
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