---
created: 2024-02-19T21:27:15 (UTC +06:00)
tags: []
source: https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/
author: 
---

# Creating a cluster with kubeadm | Kubernetes

> ## Excerpt
> Using kubeadm, you can create a minimum viable Kubernetes cluster that conforms to best practices. In fact, you can use kubeadm to set up a cluster that will pass the Kubernetes Conformance tests. kubeadm also supports other cluster lifecycle functions, such as bootstrap tokens and cluster upgrades.
The kubeadm tool is good if you need:
A simple way for you to try out Kubernetes, possibly for the first time. A way for existing users to automate setting up a cluster and test their application.

---
![](Creating%20a%20cluster%20with%20kubeadm%20%20Kubernetes/kubeadm-stacked-color.png) Using `kubeadm`, you can create a minimum viable Kubernetes cluster that conforms to best practices. In fact, you can use `kubeadm` to set up a cluster that will pass the [Kubernetes Conformance tests](https://kubernetes.io/blog/2017/10/software-conformance-certification/). `kubeadm` also supports other cluster lifecycle functions, such as [bootstrap tokens](https://kubernetes.io/docs/reference/access-authn-authz/bootstrap-tokens/) and cluster upgrades.

The `kubeadm` tool is good if you need:

-   A simple way for you to try out Kubernetes, possibly for the first time.
-   A way for existing users to automate setting up a cluster and test their application.
-   A building block in other ecosystem and/or installer tools with a larger scope.

You can install and use `kubeadm` on various machines: your laptop, a set of cloud servers, a Raspberry Pi, and more. Whether you're deploying into the cloud or on-premises, you can integrate `kubeadm` into provisioning systems such as Ansible or Terraform.

## Before you begin

To follow this guide, you need:

-   One or more machines running a deb/rpm-compatible Linux OS; for example: Ubuntu or CentOS.
-   2 GiB or more of RAM per machine--any less leaves little room for your apps.
-   At least 2 CPUs on the machine that you use as a control-plane node.
-   Full network connectivity among all machines in the cluster. You can use either a public or a private network.

You also need to use a version of `kubeadm` that can deploy the version of Kubernetes that you want to use in your new cluster.

[Kubernetes' version and version skew support policy](https://kubernetes.io/docs/setup/release/version-skew-policy/#supported-versions) applies to `kubeadm` as well as to Kubernetes overall. Check that policy to learn about what versions of Kubernetes and `kubeadm` are supported. This page is written for Kubernetes v1.29.

The `kubeadm` tool's overall feature state is General Availability (GA). Some sub-features are still under active development. The implementation of creating the cluster may change slightly as the tool evolves, but the overall implementation should be pretty stable.

## Objectives

-   Install a single control-plane Kubernetes cluster
-   Install a Pod network on the cluster so that your Pods can talk to each other

## Instructions

### Preparing the hosts

#### Component installation

Install a [container runtime](https://kubernetes.io/docs/setup/production-environment/container-runtimes) and kubeadm on all the hosts. For detailed instructions and other prerequisites, see [Installing kubeadm](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/).

#### Network setup

kubeadm similarly to other Kubernetes components tries to find a usable IP on the network interfaces associated with a default gateway on a host. Such an IP is then used for the advertising and/or listening performed by a component.

To find out what this IP is on a Linux host you can use:

```shell
ip route show # Look for a line starting with "default via"
```

Kubernetes components do not accept custom network interface as an option, therefore a custom IP address must be passed as a flag to all components instances that need such a custom configuration.

To configure the API server advertise address for control plane nodes created with both `init` and `join`, the flag `--apiserver-advertise-address` can be used. Preferably, this option can be set in the [kubeadm API](https://kubernetes.io/docs/reference/config-api/kubeadm-config.v1beta3) as `InitConfiguration.localAPIEndpoint` and `JoinConfiguration.controlPlane.localAPIEndpoint`.

For kubelets on all nodes, the `--node-ip` option can be passed in `.nodeRegistration.kubeletExtraArgs` inside a kubeadm configuration file (`InitConfiguration` or `JoinConfiguration`).

For dual-stack see [Dual-stack support with kubeadm](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/dual-stack-support).

The IP addresses that you assign to control plane components become part of their X.509 certificates' subject alternative name fields. Changing these IP addresses would require signing new certificates and restarting the affected components, so that the change in certificate files is reflected. See [Manual certificate renewal](https://kubernetes.io/docs/tasks/administer-cluster/kubeadm/kubeadm-certs/#manual-certificate-renewal) for more details on this topic.

### Preparing the required container images

This step is optional and only applies in case you wish `kubeadm init` and `kubeadm join` to not download the default container images which are hosted at `registry.k8s.io`.

Kubeadm has commands that can help you pre-pull the required images when creating a cluster without an internet connection on its nodes. See [Running kubeadm without an internet connection](https://kubernetes.io/docs/reference/setup-tools/kubeadm/kubeadm-init#without-internet-connection) for more details.

Kubeadm allows you to use a custom image repository for the required images. See [Using custom images](https://kubernetes.io/docs/reference/setup-tools/kubeadm/kubeadm-init#custom-images) for more details.

### Initializing your control-plane node

The control-plane node is the machine where the control plane components run, including [etcd](https://kubernetes.io/docs/tasks/administer-cluster/configure-upgrade-etcd/) (the cluster database) and the [API Server](https://kubernetes.io/docs/concepts/overview/components/#kube-apiserver) (which the [kubectl](https://kubernetes.io/docs/reference/kubectl/) command line tool communicates with).

1.  (Recommended) If you have plans to upgrade this single control-plane `kubeadm` cluster to high availability you should specify the `--control-plane-endpoint` to set the shared endpoint for all control-plane nodes. Such an endpoint can be either a DNS name or an IP address of a load-balancer.
2.  Choose a Pod network add-on, and verify whether it requires any arguments to be passed to `kubeadm init`. Depending on which third-party provider you choose, you might need to set the `--pod-network-cidr` to a provider-specific value. See [Installing a Pod network add-on](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/#pod-network).
3.  (Optional) `kubeadm` tries to detect the container runtime by using a list of well known endpoints. To use different container runtime or if there are more than one installed on the provisioned node, specify the `--cri-socket` argument to `kubeadm`. See [Installing a runtime](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/#installing-runtime).

To initialize the control-plane node run:

### Considerations about apiserver-advertise-address and ControlPlaneEndpoint

While `--apiserver-advertise-address` can be used to set the advertise address for this particular control-plane node's API server, `--control-plane-endpoint` can be used to set the shared endpoint for all control-plane nodes.

`--control-plane-endpoint` allows both IP addresses and DNS names that can map to IP addresses. Please contact your network administrator to evaluate possible solutions with respect to such mapping.

Here is an example mapping:

```
192.168.0.102 cluster-endpoint
```

Where `192.168.0.102` is the IP address of this node and `cluster-endpoint` is a custom DNS name that maps to this IP. This will allow you to pass `--control-plane-endpoint=cluster-endpoint` to `kubeadm init` and pass the same DNS name to `kubeadm join`. Later you can modify `cluster-endpoint` to point to the address of your load-balancer in an high availability scenario.

Turning a single control plane cluster created without `--control-plane-endpoint` into a highly available cluster is not supported by kubeadm.

### More information

For more information about `kubeadm init` arguments, see the [kubeadm reference guide](https://kubernetes.io/docs/reference/setup-tools/kubeadm/).

To configure `kubeadm init` with a configuration file see [Using kubeadm init with a configuration file](https://kubernetes.io/docs/reference/setup-tools/kubeadm/kubeadm-init/#config-file).

To customize control plane components, including optional IPv6 assignment to liveness probe for control plane components and etcd server, provide extra arguments to each component as documented in [custom arguments](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/control-plane-flags/).

To reconfigure a cluster that has already been created see [Reconfiguring a kubeadm cluster](https://kubernetes.io/docs/tasks/administer-cluster/kubeadm/kubeadm-reconfigure).

To run `kubeadm init` again, you must first [tear down the cluster](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/#tear-down).

If you join a node with a different architecture to your cluster, make sure that your deployed DaemonSets have container image support for this architecture.

`kubeadm init` first runs a series of prechecks to ensure that the machine is ready to run Kubernetes. These prechecks expose warnings and exit on errors. `kubeadm init` then downloads and installs the cluster control plane components. This may take several minutes. After it finishes you should see:

```none
Your Kubernetes control-plane has initialized successfully! To start using your cluster, you need to run the following as a regular user: mkdir -p $HOME/.kube sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config sudo chown $(id -u):$(id -g) $HOME/.kube/config You should now deploy a Pod network to the cluster. Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at: /docs/concepts/cluster-administration/addons/ You can now join any number of machines by running the following on each node as root: kubeadm join <control-plane-host>:<control-plane-port> --token <token> --discovery-token-ca-cert-hash sha256:<hash>
```

To make kubectl work for your non-root user, run these commands, which are also part of the `kubeadm init` output:

```bash
mkdir -p $HOME/.kube sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

Alternatively, if you are the `root` user, you can run:

```bash
export KUBECONFIG=/etc/kubernetes/admin.conf
```

Make a record of the `kubeadm join` command that `kubeadm init` outputs. You need this command to [join nodes to your cluster](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/#join-nodes).

The token is used for mutual authentication between the control-plane node and the joining nodes. The token included here is secret. Keep it safe, because anyone with this token can add authenticated nodes to your cluster. These tokens can be listed, created, and deleted with the `kubeadm token` command. See the [kubeadm reference guide](https://kubernetes.io/docs/reference/setup-tools/kubeadm/kubeadm-token/).

### Installing a Pod network add-on

Several external projects provide Kubernetes Pod networks using CNI, some of which also support [Network Policy](https://kubernetes.io/docs/concepts/services-networking/network-policies/).

See a list of add-ons that implement the [Kubernetes networking model](https://kubernetes.io/docs/concepts/cluster-administration/networking/#how-to-implement-the-kubernetes-network-model).

Please refer to the [Installing Addons](https://kubernetes.io/docs/concepts/cluster-administration/addons/#networking-and-network-policy) page for a non-exhaustive list of networking addons supported by Kubernetes. You can install a Pod network add-on with the following command on the control-plane node or a node that has the kubeconfig credentials:

```bash
kubectl apply -f <add-on.yaml>
```

You can install only one Pod network per cluster.

Once a Pod network has been installed, you can confirm that it is working by checking that the CoreDNS Pod is `Running` in the output of `kubectl get pods --all-namespaces`. And once the CoreDNS Pod is up and running, you can continue by joining your nodes.

If your network is not working or CoreDNS is not in the `Running` state, check out the [troubleshooting guide](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/troubleshooting-kubeadm/) for `kubeadm`.

### Managed node labels

By default, kubeadm enables the [NodeRestriction](https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/#noderestriction) admission controller that restricts what labels can be self-applied by kubelets on node registration. The admission controller documentation covers what labels are permitted to be used with the kubelet `--node-labels` option. The `node-role.kubernetes.io/control-plane` label is such a restricted label and kubeadm manually applies it using a privileged client after a node has been created. To do that manually you can do the same by using `kubectl label` and ensure it is using a privileged kubeconfig such as the kubeadm managed `/etc/kubernetes/admin.conf`.

### Control plane node isolation

By default, your cluster will not schedule Pods on the control plane nodes for security reasons. If you want to be able to schedule Pods on the control plane nodes, for example for a single machine Kubernetes cluster, run:

```bash
kubectl taint nodes --all node-role.kubernetes.io/control-plane-
```

The output will look something like:

```
node "test-01" untainted
...
```

This will remove the `node-role.kubernetes.io/control-plane:NoSchedule` taint from any nodes that have it, including the control plane nodes, meaning that the scheduler will then be able to schedule Pods everywhere.

Additionally, you can execute the following command to remove the [`node.kubernetes.io/exclude-from-external-load-balancers`](https://kubernetes.io/docs/reference/labels-annotations-taints/#node-kubernetes-io-exclude-from-external-load-balancers) label from the control plane node, which excludes it from the list of backend servers:

```bash
kubectl label nodes --all node.kubernetes.io/exclude-from-external-load-balancers-
```

### Joining your nodes

The nodes are where your workloads (containers and Pods, etc) run. To add new nodes to your cluster do the following for each machine:

-   SSH to the machine
    
-   Become root (e.g. `sudo su -`)
    
-   [Install a runtime](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/#installing-runtime) if needed
    
-   Run the command that was output by `kubeadm init`. For example:
    
    ```bash
    kubeadm join --token <token> <control-plane-host>:<control-plane-port> --discovery-token-ca-cert-hash sha256:<hash>
    ```
    

If you do not have the token, you can get it by running the following command on the control-plane node:

The output is similar to this:

```console
TOKEN TTL EXPIRES USAGES DESCRIPTION EXTRA GROUPS 8ewj1p.9r9hcjoqgajrj4gi 23h 2018-06-12T02:51:28Z authentication, The default bootstrap system: signing token generated by bootstrappers: 'kubeadm init'. kubeadm: default-node-token
```

By default, tokens expire after 24 hours. If you are joining a node to the cluster after the current token has expired, you can create a new token by running the following command on the control-plane node:

The output is similar to this:

If you don't have the value of `--discovery-token-ca-cert-hash`, you can get it by running the following command chain on the control-plane node:

```bash
openssl x509 -pubkey -in /etc/kubernetes/pki/ca.crt | openssl rsa -pubin -outform der 2>/dev/null | \ openssl dgst -sha256 -hex | sed 's/^.* //'
```

The output is similar to:

```console
8cb2de97839780a412b93877f8507ad6c94f73add17d5d7058e91741c9d5ec78
```

The output should look something like:

```
[preflight] Running pre-flight checks

... (log output of join workflow) ...

Node join complete:
* Certificate signing request sent to control-plane and response
  received.
* Kubelet informed of new secure connection details.

Run 'kubectl get nodes' on control-plane to see this machine join.
```

A few seconds later, you should notice this node in the output from `kubectl get nodes` when run on the control-plane node.

### (Optional) Controlling your cluster from machines other than the control-plane node

In order to get a kubectl on some other computer (e.g. laptop) to talk to your cluster, you need to copy the administrator kubeconfig file from your control-plane node to your workstation like this:

```bash
scp root@<control-plane-host>:/etc/kubernetes/admin.conf . kubectl --kubeconfig ./admin.conf get nodes
```

### (Optional) Proxying API Server to localhost

If you want to connect to the API Server from outside the cluster you can use `kubectl proxy`:

```bash
scp root@<control-plane-host>:/etc/kubernetes/admin.conf . kubectl --kubeconfig ./admin.conf proxy
```

You can now access the API Server locally at `http://localhost:8001/api/v1`

## Clean up

If you used disposable servers for your cluster, for testing, you can switch those off and do no further clean up. You can use `kubectl config delete-cluster` to delete your local references to the cluster.

However, if you want to deprovision your cluster more cleanly, you should first [drain the node](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#drain) and make sure that the node is empty, then deconfigure the node.

### Remove the node

Talking to the control-plane node with the appropriate credentials, run:

```bash
kubectl drain <node name> --delete-emptydir-data --force --ignore-daemonsets
```

Before removing the node, reset the state installed by `kubeadm`:

The reset process does not reset or clean up iptables rules or IPVS tables. If you wish to reset iptables, you must do so manually:

```bash
iptables -F && iptables -t nat -F && iptables -t mangle -F && iptables -X
```

If you want to reset the IPVS tables, you must run the following command:

Now remove the node:

```bash
kubectl delete node <node name>
```

If you wish to start over, run `kubeadm init` or `kubeadm join` with the appropriate arguments.

### Clean up the control plane

You can use `kubeadm reset` on the control plane host to trigger a best-effort clean up.

See the [`kubeadm reset`](https://kubernetes.io/docs/reference/setup-tools/kubeadm/kubeadm-reset/) reference documentation for more information about this subcommand and its options.

## Version skew policy

While kubeadm allows version skew against some components that it manages, it is recommended that you match the kubeadm version with the versions of the control plane components, kube-proxy and kubelet.

### kubeadm's skew against the Kubernetes version

kubeadm can be used with Kubernetes components that are the same version as kubeadm or one version older. The Kubernetes version can be specified to kubeadm by using the `--kubernetes-version` flag of `kubeadm init` or the [`ClusterConfiguration.kubernetesVersion`](https://kubernetes.io/docs/reference/config-api/kubeadm-config.v1beta3/) field when using `--config`. This option will control the versions of kube-apiserver, kube-controller-manager, kube-scheduler and kube-proxy.

Example:

-   kubeadm is at 1.29
-   `kubernetesVersion` must be at 1.29 or 1.28

### kubeadm's skew against the kubelet

Similarly to the Kubernetes version, kubeadm can be used with a kubelet version that is the same version as kubeadm or three versions older.

Example:

-   kubeadm is at 1.29
-   kubelet on the host must be at 1.29, 1.28, 1.27 or 1.26

### kubeadm's skew against kubeadm

There are certain limitations on how kubeadm commands can operate on existing nodes or whole clusters managed by kubeadm.

If new nodes are joined to the cluster, the kubeadm binary used for `kubeadm join` must match the last version of kubeadm used to either create the cluster with `kubeadm init` or to upgrade the same node with `kubeadm upgrade`. Similar rules apply to the rest of the kubeadm commands with the exception of `kubeadm upgrade`.

Example for `kubeadm join`:

-   kubeadm version 1.29 was used to create a cluster with `kubeadm init`
-   Joining nodes must use a kubeadm binary that is at version 1.29

Nodes that are being upgraded must use a version of kubeadm that is the same MINOR version or one MINOR version newer than the version of kubeadm used for managing the node.

Example for `kubeadm upgrade`:

-   kubeadm version 1.28 was used to create or upgrade the node
-   The version of kubeadm used for upgrading the node must be at 1.28 or 1.29

To learn more about the version skew between the different Kubernetes component see the [Version Skew Policy](https://kubernetes.io/releases/version-skew-policy/).

## Limitations

### Cluster resilience

The cluster created here has a single control-plane node, with a single etcd database running on it. This means that if the control-plane node fails, your cluster may lose data and may need to be recreated from scratch.

Workarounds:

-   Regularly [back up etcd](https://etcd.io/docs/v3.5/op-guide/recovery/). The etcd data directory configured by kubeadm is at `/var/lib/etcd` on the control-plane node.
    
-   Use multiple control-plane nodes. You can read [Options for Highly Available topology](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/ha-topology/) to pick a cluster topology that provides [high-availability](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/high-availability/).
    

### Platform compatibility

kubeadm deb/rpm packages and binaries are built for amd64, arm (32-bit), arm64, ppc64le, and s390x following the [multi-platform proposal](https://git.k8s.io/design-proposals-archive/multi-platform.md).

Multiplatform container images for the control plane and addons are also supported since v1.12.

Only some of the network providers offer solutions for all platforms. Please consult the list of network providers above or the documentation from each provider to figure out whether the provider supports your chosen platform.

## Troubleshooting

If you are running into difficulties with kubeadm, please consult our [troubleshooting docs](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/troubleshooting-kubeadm/).

## What's next

-   Verify that your cluster is running properly with [Sonobuoy](https://github.com/heptio/sonobuoy)
-   See [Upgrading kubeadm clusters](https://kubernetes.io/docs/tasks/administer-cluster/kubeadm/kubeadm-upgrade/) for details about upgrading your cluster using `kubeadm`.
-   Learn about advanced `kubeadm` usage in the [kubeadm reference documentation](https://kubernetes.io/docs/reference/setup-tools/kubeadm/)
-   Learn more about Kubernetes [concepts](https://kubernetes.io/docs/concepts/) and [`kubectl`](https://kubernetes.io/docs/reference/kubectl/).
-   See the [Cluster Networking](https://kubernetes.io/docs/concepts/cluster-administration/networking/) page for a bigger list of Pod network add-ons.
-   See the [list of add-ons](https://kubernetes.io/docs/concepts/cluster-administration/addons/) to explore other add-ons, including tools for logging, monitoring, network policy, visualization & control of your Kubernetes cluster.
-   Configure how your cluster handles logs for cluster events and from applications running in Pods. See [Logging Architecture](https://kubernetes.io/docs/concepts/cluster-administration/logging/) for an overview of what is involved.

### Feedback

-   For bugs, visit the [kubeadm GitHub issue tracker](https://github.com/kubernetes/kubeadm/issues)
-   For support, visit the [#kubeadm](https://kubernetes.slack.com/messages/kubeadm/) Slack channel
-   General SIG Cluster Lifecycle development Slack channel: [#sig-cluster-lifecycle](https://kubernetes.slack.com/messages/sig-cluster-lifecycle/)
-   SIG Cluster Lifecycle [SIG information](https://github.com/kubernetes/community/tree/master/sig-cluster-lifecycle#readme)
-   SIG Cluster Lifecycle mailing list: [kubernetes-sig-cluster-lifecycle](https://groups.google.com/forum/#!forum/kubernetes-sig-cluster-lifecycle)
