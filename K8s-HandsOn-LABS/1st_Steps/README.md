## What is Kubernetes?

Google developed the Kubernetes project in mid-2014 to act as a container orchestrator for the company. Kubernetes (k8s), whose term in Greek means "helmsman", is an *open-source* project that has *design* and development based on the Borg project, which is also from Google [article](https://kubernetes.io/blog/2015/04/borg-predecessor-to-kubernetes). Some other products available on the market, such as Apache Mesos and Cloud Foundry, also emerged from the Borg project. As Kubernetes is a difficult word to pronounce - and to write - the community dubbed it "K8s".

Kubernetes is open source - in contrast to Borg and Omega which were developed as purely internal Google systems. Kubernetes was developed with a stronger focus on the experience of developers who write applications that run on a cluster: its main goal is to facilitate the deployment and management of distributed systems while benefiting from the better use of memory and processing resources that containers make possible. 

Information was extracted and adapted from this [article](https://static.googleusercontent.com/media/research.google.com/EN//pubs/archive/44843.pdf), which describes lessons learned from the development and operation of these systems.

## K8s Architecture

The K8s also follows a *control plane/workers* model, thus constituting a *cluster*, where at least three nodes are recommended for its operation: the *control-plane* node, responsible (by default) for managing the *cluster*, and the others as *workers*, executors of the applications we want to run on this *cluster*.

It is possible to create a Kubernetes cluster running on just one node, but it is recommended only for study purposes and never run in a production environment.

If you want to use Kubernetes on your local computer, several solutions are available for creating a Kubernetes cluster, using virtual machines or Docker, for example.

With this, you can have a Kubernetes cluster with several nodes running all of them on your local computer.

Some examples are:

* [Kind](https://kind.sigs.k8s.io/docs/user/quick-start): A tool for running Docker containers that simulate the operation of a Kubernetes cluster. It is used for teaching, development and testing purposes. **Kind should not be used for production**;

* [Minikube](https://github.com/kubernetes/minikube): tool to deploy a Kubernetes *cluster* locally with just one node. Widely used for teaching, development and testing purposes. **Minikube should not be used for production**;

* [MicroK8S](https://microk8s.io): Developed by [Canonical](https://canonical.com), the same company that develops [Ubuntu](https://ubuntu.com). It can be used in different distributions and **can be used in production environments**, especially for *Edge Computing* and IoT (*Internet of things*);

* [k3s](https://k3s.io): Developed by [Rancher Labs](https://rancher.com), it is a direct competitor to MicroK8s, and can even be run on a Raspberry Pi;

* [k0s](https://k0sproject.io): Developed by [Mirantis](https://www.mirantis.com), the same company that acquired the enterprise part of [Docker](https://www.docker .with). It is a Kubernetes distribution with all the resources necessary to operate in a single binary, which provides simplicity in installing and maintaining the cluster. The correct pronunciation is kay-zero-ess and its objective is to reduce the technical effort and wear and tear in installing a Kubernetes cluster, which is why its name alludes to *Zero Friction*. **k0s can be used in production environments**

* **API Server**: It is one of the main components of k8s. This component provides an API that uses JSON over HTTP for communication, where the ``kubectl`` utility is mainly used by administrators to communicate with other nodes, as shown in the graph. These communications between components are established through [REST](https://restfulapi.net) requests;

* **ETCD**: etcd is a distributed key-value *datastore* that k8s uses to store *cluster* specifications, status and configurations. All data stored within etcd is manipulated only through the API. For security reasons, etcd is by default only run on nodes classified as *control plane* in the k8s *cluster*, but they can also be run on external *clusters*, specific to etcd, for example;

* **Scheduler**: The *scheduler* is responsible for selecting the node that will host a given *pod* (the smallest unit of a k8s *cluster* - don't worry about that for now, we'll talk more about that later late) to be executed. This selection is made based on the amount of resources available in each node, as well as the state of each of the nodes in the *cluster*, thus ensuring that resources are well distributed. Furthermore, the selection of nodes, on which one or more pods will run, can also take into account user-defined policies, such as affinity, location of data to be read by applications, etc.;

* **Controller Manager**: It is the *controller manager* who guarantees that the *cluster* is in the last state defined in etcd. For example: if in etcd a *deploy* is configured to have ten replicas of a *pod*, it is the *controller manager* who will check if the current state of the *cluster* corresponds to this state and, if not, it will look for reconcile both;

* **Kubelet**: *kubelet* can be seen as the k8s alent that runs on worker nodes. On each worker node, there must be a Kubelet agent running. Kubelet is responsible for actually managing the *pods* that were directed by the *cluster* *controller*, within the nodes, so that Kubelet can start, stop and keep the containers and pods running accordingly as instructed by the cluster controller;

* **Kube-proxy**: Acts as a *proxy* and a *load balancer*. This component is responsible for routing requests to the correct *pods*, as well as taking care of the network part of the node.
&nbsp;
## Ports to be opened

**CONTROL PLANE**
Protocol|Direction|Port Range|Purpose|Used By
--------|---------|----------|-------|-------
TCP|Inbound|6443*|Kubernetes API server|All
TCP|Inbound|2379-2380|etcd server client API|kube-apiserver, etcd
TCP|Inbound|10250|Kubelet API|Self, Control plane
TCP|Inbound|10251|kube-scheduler|Self
TCP|Inbound|10252|kube-controller-manager|Self

* Every port marked by * is customizable, you need to make sure that the changed port is also open.

&nbsp;
**WORKERS**
Protocol|Direction|Port Range|Purpose|Used By
--------|---------|----------|-------|-------
TCP|Inbound|10250|Kubelet API|Self, Control plane
TCP|Inbound|30000-32767|NodePort|Services All

&nbsp;

## K8S Key Concepts 

K8s manages containers slightly differently from other orchestrators, mainly because it does not handle containers directly, but rather through *pods*. 

- **Pod**: It is the smallest object in K8s. They are objects that share the same resources, such as addresses, volumes, CPU and RAM. A pod can have multiple containers;

- **Deployment**: It is one of the main *controllers* used. *Deployment*, in conjunction with *ReplicaSet*, guarantees that a certain number of replicas of a pod are running on the cluster's worker nodes. In addition, Deployment is also responsible for managing the application lifecycle, where characteristics associated with the application, such as image, port, volumes and environment variables, can be specified in *yaml* or *json* files to subsequently be passed as parameter to ``kubectl`` to execute the deployment. This action can be performed both for creation and for updating and removing the deployment;

- **ReplicaSets**: It is an object responsible for guaranteeing the number of pods running on the node;

- **Services**: It is a way for you to expose communication through a *ClusterIP*, *NodePort* or *LoadBalancer* to distribute requests between the different Pods of that Deployment. It works as a load balancer.

## Methods to install Kubernetes

We will focus on installing Kubernetes using `kubeadm`, which is one of the most used ways to create a Kubernetes cluster. But there are other ways to install Kubernetes:

* **`kubeadm`**: It is a tool for creating and managing a Kubernetes cluster across multiple nodes. It automates many of the cluster configuration tasks, including installing the control plane and nodes. It is highly configurable and can be used to create custom clusters.

* **`Kubespray`**: It is a tool that uses Ansible to deploy and manage a Kubernetes cluster across multiple nodes. It offers many options for customizing your cluster installation, including choosing the network provider, number of control plane replicas, storage type, and more. It is a good option for deploying a cluster across multiple environments, including public and private clouds.

* **`Cloud Providers`**: Many cloud providers, such as AWS, Google Cloud Platform, and Microsoft Azure, offer options for deploying a Kubernetes cluster in their infrastructure. They often provide predefined templates that can be used to deploy a cluster in just a few clicks. Some cloud providers also offer managed Kubernetes services that handle all cluster configuration and management.

* **`Managed Kubernetes`**: These are managed services offered by some cloud providers, such as Amazon EKS, Google Cloud's GKE and Azure's AKS. They offer a managed Kubernetes cluster where you only need to worry about deploying and managing your applications. These services handle the configuration, updating, and maintenance of the cluster for you. In this case, you do not have to manage the cluster control plane, as it is managed by the cloud provider.

* **`Kops`**: It is a tool for deploying and managing Kubernetes clusters in the cloud. It is specifically designed for deployment on public clouds like AWS, GCP, and Azure. Kops allows you to create, update and manage Kubernetes clusters in the cloud. Some of the main advantages of using Kops are customization, scalability, and security. However, using Kops can be more complex than other Kubernetes installation options, especially if you are unfamiliar with the cloud you are deploying to.

* **`Minikube` and `kind`**: These are tools that allow you to create a Kubernetes cluster locally, on a single node. They are useful for testing and learning about Kubernetes, as you can create a cluster in a few minutes and start deploying applications immediately. They are also useful for developers who need to test their applications in a Kubernetes environment without having to set up a cluster in a production environment.

## Creating a Kubernetes cluster with kubeadm

Let's move on to creating a Kubernetes cluster using `kubeadm`.

As mentioned, `kubeadm` is a tool for creating and managing a Kubernetes cluster on multiple nodes. It automates many of the cluster configuration tasks, including installing the control plane and nodes.

First thing, before we can move forward, we have to understand what the prerequisites are for installing Kubernetes. To do this, you can consult the official Kubernetes documentation, but below is a list of the main prerequisites:

*Linux

* 2GB or more RAM per machine (less than 2GB is not recommended)

* 2 CPUs or more

* Network connection between all nodes in the cluster (can be via public or private network)

* Some ports need to be open for the cluster to work correctly, the main ones:

     * Port 6443: It is the default port used by the Kubernetes API Server to communicate with the cluster components. It is the main port used to manage the cluster and must always be open.

     * Ports 10250-10255: These ports are used by kubelet to communicate with the Kubernetes control plane. Port 10250 is used for read/write communication and port 10255 is used for read communication only.

     * Port 30000-32767: These ports are used for NodePort services that need to be accessible outside the cluster. Kubernetes allocates a random port within this range to each NodePort service and redirects traffic to the corresponding pod.
     * Port 2379-2380: These ports are used by etcd, the distributed key-value database used by the Kubernetes control plane. Port 2379 is used for read/write communication and port 2380 is used for election communication only.
&nbsp;
## Installing kubeadm

#### Disabling the use of swap on the system

Disable the use of swap in the system. This is necessary because Kubernetes doesn't work well with swap enabled:

```
sudo swapoff -a
```

## Loading kernel modules

#### Load the kernel modules necessary for Kubernetes to work:

```
cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
overlay
br_netfilter
EOF

sudo modprobe overlay
sudo modprobe br_netfilter
```
## Configuring system parameters

#### Configure some system parameters. This will ensure our cluster works correctly:

```
cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-iptables = 1
net.bridge.bridge-nf-call-ip6tables = 1
net.ipv4.ip_forward = 1
EOF

sudo sysctl --system
```
## Installing Kubernetes packages

#### Install the Kubernetes packages! Oh my God, beautiful thing! Here we go:

```
sudo apt-get update && sudo apt-get install -y apt-transport-https curl

curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -

echo "deb https://apt.kubernetes.io/kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list

sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl

sudo apt-mark hold kubelet kubeadm kubectl
```

## Installing containerd

#### Install containerd, which are essential for our Kubernetes environment:

```
sudo apt-get update && sudo apt-get install -y apt-transport-https ca-certificates curl gnupg lsb-release

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

echo "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt-get update && sudo apt-get install -y containerd.io
```

## Configuring containerd

#### Configure containerd so that it works properly with our cluster:

```
sudo containerd config default | sudo tee /etc/containerd/config.toml

sudo sed -i 's/SystemdCgroup = false/SystemdCgroup = true/g' /etc/containerd/config.toml

sudo systemctl restart containerd
sudo systemctl status containerd
```
## Enabling the kubelet service

#### Enabling the kubelet service so that it starts automatically with the system:

```
sudo systemctl enable --now kubelet
```

## Configuring ports

Before starting the cluster, remember the ports that need to be open for the cluster to function correctly(Step Ports to be opened). In this hands-on lab, we will be using only one `control plane` node, we do not need to worry about some when we have more than one `control plane` node, as they need to communicate with each other to maintain the state of the cluster, or even the ports 30000-32767, which are used for NodePort services that need to be accessible outside the cluster. We can open them as needed, as we create our services.

For now, what we need to ensure are TCP ports 6443 only on the `control plane` and 10250-10255 open on all nodes in the cluster.

We will use Weave Net as the CNI, which is a CNI that uses the Kubernetes packet routing protocol to create a network between pods. I will talk more about it later, but as we are talking about the ports that are important for the cluster to work, we need to open TCP port 6783 and UDP ports 6783 and 6784, for Weave Net to work correctly.

Kindly don't forget to open TCP ports 6443, 10250-10255 and 6783 on your firewall.

## Initializing the cluster

### Starting the cluster:

```
sudo kubeadm init --pod-network-cidr=10.10.0.0/16 --apiserver-advertise-address=<CONTROL PLANE IP TO COMMUNICATE TO THE NODES>
```
&nbsp;

If the step above was executed successfully, a message stating that the cluster has been initialized will be displayed:

```
root@ip-172-31-53-44:~# sudo kubeadm init --pod-network-cidr=10.10.0.0/16 --apiserver-advertise-address=172.31.53.44
I0113 16:44:35.367343    4734 version.go:256] remote version is much newer: v1.29.0; falling back to: stable-1.28
[init] Using Kubernetes version: v1.28.5
[preflight] Running pre-flight checks
[preflight] Pulling images required for setting up a Kubernetes cluster
[preflight] This might take a minute or two, depending on the speed of your internet connection
[preflight] You can also perform this action in beforehand using 'kubeadm config images pull'
W0113 16:44:45.175675    4734 checks.go:835] detected that the sandbox image "registry.k8s.io/pause:3.6" of the container runtime is inconsistent with that used by kubeadm. It is recommended that using "registry.k8s.io/pause:3.9" as the CRI sandbox image.
[certs] Using certificateDir folder "/etc/kubernetes/pki"
[certs] Generating "ca" certificate and key
[certs] Generating "apiserver" certificate and key
[certs] apiserver serving cert is signed for DNS names [ip-172-31-53-44 kubernetes kubernetes.default kubernetes.default.svc kubernetes.default.svc.cluster.local] and IPs [10.96.0.1 172.31.53.44]
[certs] Generating "apiserver-kubelet-client" certificate and key
[certs] Generating "front-proxy-ca" certificate and key
[certs] Generating "front-proxy-client" certificate and key
[certs] Generating "etcd/ca" certificate and key
[certs] Generating "etcd/server" certificate and key
[certs] etcd/server serving cert is signed for DNS names [ip-172-31-53-44 localhost] and IPs [172.31.53.44 127.0.0.1 ::1]
[certs] Generating "etcd/peer" certificate and key
[certs] etcd/peer serving cert is signed for DNS names [ip-172-31-53-44 localhost] and IPs [172.31.53.44 127.0.0.1 ::1]
[certs] Generating "etcd/healthcheck-client" certificate and key
[certs] Generating "apiserver-etcd-client" certificate and key
[certs] Generating "sa" key and public key
[kubeconfig] Using kubeconfig folder "/etc/kubernetes"
[kubeconfig] Writing "admin.conf" kubeconfig file
[kubeconfig] Writing "kubelet.conf" kubeconfig file
[kubeconfig] Writing "controller-manager.conf" kubeconfig file
[kubeconfig] Writing "scheduler.conf" kubeconfig file
[etcd] Creating static Pod manifest for local etcd in "/etc/kubernetes/manifests"
[control-plane] Using manifest folder "/etc/kubernetes/manifests"
[control-plane] Creating static Pod manifest for "kube-apiserver"
[control-plane] Creating static Pod manifest for "kube-controller-manager"
[control-plane] Creating static Pod manifest for "kube-scheduler"
[kubelet-start] Writing kubelet environment file with flags to file "/var/lib/kubelet/kubeadm-flags.env"
[kubelet-start] Writing kubelet configuration to file "/var/lib/kubelet/config.yaml"
[kubelet-start] Starting the kubelet
[wait-control-plane] Waiting for the kubelet to boot up the control plane as static Pods from directory "/etc/kubernetes/manifests". This can take up to 4m0s
[apiclient] All control plane components are healthy after 7.002233 seconds
[upload-config] Storing the configuration used in ConfigMap "kubeadm-config" in the "kube-system" Namespace
[kubelet] Creating a ConfigMap "kubelet-config" in namespace kube-system with the configuration for the kubelets in the cluster
[upload-certs] Skipping phase. Please see --upload-certs
[mark-control-plane] Marking the node ip-172-31-53-44 as control-plane by adding the labels: [node-role.kubernetes.io/control-plane node.kubernetes.io/exclude-from-external-load-balancers]
[mark-control-plane] Marking the node ip-172-31-53-44 as control-plane by adding the taints [node-role.kubernetes.io/control-plane:NoSchedule]       
[bootstrap-token] Using token: 3p5qgh.cyki1myi4uw1zmvk
[bootstrap-token] Configuring bootstrap tokens, cluster-info ConfigMap, RBAC Roles
[bootstrap-token] Configured RBAC rules to allow Node Bootstrap tokens to get nodes
[bootstrap-token] Configured RBAC rules to allow Node Bootstrap tokens to post CSRs in order for nodes to get long term certificate credentials
[bootstrap-token] Configured RBAC rules to allow the csrapprover controller automatically approve CSRs from a Node Bootstrap Token
[bootstrap-token] Configured RBAC rules to allow certificate rotation for all node client certificates in the cluster
[bootstrap-token] Creating the "cluster-info" ConfigMap in the "kube-public" namespace
[kubelet-finalize] Updating "/etc/kubernetes/kubelet.conf" to point to a rotatable kubelet client certificate and key
[addons] Applied essential addon: CoreDNS
[addons] Applied essential addon: kube-proxy

Your Kubernetes control-plane has initialized successfully!

To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

Alternatively, if you are the root user, you can run:

  export KUBECONFIG=/etc/kubernetes/admin.conf

You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/

Then you can join any number of worker nodes by running the following on each as root:

kubeadm join 172.31.53.44:6443 --token 3p5qgh.cyki1myi4uw1zmvk \
        --discovery-token-ca-cert-hash sha256:a8a109f84e916bf9caac83de83522bc2df5d7ddb4661dc7a243b70e592142b3a
root@ip-172-31-53-44:~#
```
&nbsp;

You will see a list of commands to configure cluster access with kubectl. Copy and paste this command into your terminal:

```
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```
&nbsp;

This configuration is required so that kubectl can communicate with the cluster, because when we are copying the `admin.conf` file to the user's `.kube` directory, we are copying the file with root permissions, this is the reason We can run the command `sudo chown $(id -u):$(id -g) $HOME/.kube/config` to change the file permissions for the user running the command.

## Understanding the admin.conf file

- It is a configuration file for kubectl, which is the command-line client for Kubernetes. It is used to communicate with the Kubernetes cluster.

- Contains cluster access information, such as the API server address, client certificate, and authentication token.

- I can have more than one context within the `admin.conf` file, where each context is a Kubernetes cluster. For example, I can have one context for the production cluster and another for the development cluster, as simple as flying.

- It contains the cluster access data, so if someone has access to this file, they will have access to the cluster. (As long as you have access to the cluster, of course).

- The `admin.conf` file is created when the cluster boots.

Example of `admin.conf` file:

```
root@ip-172-31-53-44:~# cat /etc/kubernetes/admin.conf
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: <YOUR_KEY>
    server: https://172.31.53.44:6443
  name: kubernetes
contexts:
- context:
    cluster: kubernetes
    user: kubernetes-admin
  name: kubernetes-admin@kubernetes
current-context: kubernetes-admin@kubernetes
kind: Config
preferences: {}
users:
- name: kubernetes-admin
  user:
    client-certificate-data: <YOUR_PUBLIC_KEY>
    client-key-data: <YOUR_PRIVATE_KEY>
root@ip-172-31-53-44:~#
root@ip-172-31-53-44:~#
```
&nbsp;
You can find the files that are used to add these credentials to your cluster in `/etc/kubernetes/pki/`.
There we have the following files that are used to add these credentials to your cluster:

- **client-certificate-data**: The client certificate file is usually found in /etc/kubernetes/pki/apiserver-kubelet-client.crt.

- **client-key-data**: The client private key file is typically found in /etc/kubernetes/pki/apiserver-kubelet-client.key.

- **certificate-authority-data**: The certificate authority (CA) certificate file is typically found in /etc/kubernetes/pki/ca.crt.

It is worth remembering that this file is automatically generated when the cluster is initialized and is added to the `admin.conf` file that is used to access the cluster. These credentials are copied to the `admin.conf` file already converted to base64.
&nbsp;

If you want, you can access the contents of the `admin.conf` file with the following command:

```bash
root@ip-172-31-53-44:~# kubectl config view
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: DATA+OMITTED
    server: https://172.31.53.44:6443
  name: kubernetes
contexts:
- context:
    cluster: kubernetes
    user: kubernetes-admin
  name: kubernetes-admin@kubernetes
current-context: kubernetes-admin@kubernetes
kind: Config
preferences: {}
users:
- name: kubernetes-admin
  user:
    client-certificate-data: DATA+OMITTED
    client-key-data: DATA+OMITTED
root@ip-172-31-53-44:~#
```
&nbsp;
It will only omit certificate and private key data, which are too large to be displayed on the terminal.

## Adding the other nodes to the cluster

To do this, we will again use the `kubeadm` command, but instead of running the command on the control plane node, at this point, we need to run the command directly on the node we want to add to the cluster.

When we initialized our cluster, `kubeadm` showed us the command we need to run on the new nodes, so that they can be added to the cluster as `workers`.

```bash
sudo kubeadm join 172.31.57.89:6443 --token if9hn9.xhxo6s89byj9rsmd \
--discovery-token-ca-cert-hash sha256:ad583497a4171d1fc7d21e2ca2ea7b32bdc8450a1a4ca4cfa2022748a99fa477
```

&nbsp;

The kubeadm join command is used to add a new node to the existing Kubernetes cluster. It runs on worker nodes so they can join the cluster and receive instructions from the control plane. Let's analyze the parts of the given command:

- **kubeadm join**: The base command to add a new node to the cluster.

- **172.31.53.44:6443**: IP address and port of the master node API server (control plane). In this example, the master node is located at IP 172.31.53.44 and the port is 6443.

- **--token 3p5qgh.cyki1myi4uw1zmvk** token: The token is used to authenticate the worker node to the master node during the joining process. Tokens are generated by the master node and have a limited validity (by default, 24 hours). In this example, the token is 3p5qgh.cyki1myi4uw1zmvk

- **--discovery-token-ca-cert-hash sha256:a8a109f84e916bf9caac83de83522bc2df5d7ddb4661dc7a243b70e592142b3a**: This is a cryptographic hash of the control plane's certification authority (CA) certificate. It is used to ensure that the worker node is communicating with the correct and authentic control plane node. The value after sha256: is the hash of the CA certificate.

When you run this command on the worker, it will start the process of joining the cluster. If the token is valid and the CA certificate hash matches the control plane node's CA certificate, the worker node is authenticated and added to the cluster. Upon successful joining, the new node will begin running the Pods and receiving instructions from the control plane as needed.

After executing `join` on each worker node, go to the node we created to be the control plane, and execute:

```bash
kubectl get nodes
```
&nbsp;

```bash
root@ip-172-31-53-44:~# kubectl get no
NAME              STATUS     ROLES           AGE   VERSION
ip-172-31-53-44   NotReady   control-plane   10m   v1.28.2
ip-172-31-56-3    NotReady   <none>          71s   v1.28.2
```
&nbsp;

The two new nodes have been added to the cluster, but they still have the status `Not Ready`,  since we have not yet installed our network plugin to communicate between the pods is possible. Let's do it now. :)

## Installing Weave Net

Install Weave Net:
```
$ kubectl apply -f https://github.com/weaveworks/weave/releases/download/v2.8.1/weave-daemonset-k8s.yaml
```
&nbsp;

Wait a few minutes until all cluster components are up and running. You can check the status of the cluster components with the following command:

```
kubectl get pods -n kube-system
NAMESPACE     NAME                                      READY   STATUS    RESTARTS       AGE
kube-system   coredns-5dd5756b68-8drcg                  1/1     Running   0              18m
kube-system   coredns-5dd5756b68-fcgk7                  1/1     Running   0              18m
kube-system   etcd-ip-172-31-53-44                      1/1     Running   0              18m
kube-system   kube-apiserver-ip-172-31-53-44            1/1     Running   0              18m
kube-system   kube-controller-manager-ip-172-31-53-44   1/1     Running   0              18m
kube-system   kube-proxy-wzjcw                          1/1     Running   0              18m
kube-system   kube-proxy-zwcls                          1/1     Running   0              9m27s
kube-system   kube-scheduler-ip-172-31-53-44            1/1     Running   0              18m
kube-system   weave-net-gjdxk                           2/2     Running   1 (2m5s ago)   2m10s
kube-system   weave-net-zsqhr                           2/2     Running   1 (2m5s ago)   2m10s
root@ip-172-31-53-44:~#

```
&nbsp;

```
kubectl get nodes
NAME              STATUS   ROLES           AGE     VERSION
ip-172-31-53-44   Ready    control-plane   17m     v1.28.2
ip-172-31-56-3    Ready    <none>          8m34s   v1.28.2
```

&nbsp;
## What are Weave Net and CNI? 
Weave Net is a networking plugin that allows pods to communicate with each other. It also allows pods to communicate with the outside world, such as other clusters or the Internet.
When Kubernetes is installed, it solves several problems by itself, but when it comes to communication between pods, it doesn't. Therefore, we need to install a network plugin to solve this problem.

CNI is a specification and set of libraries for configuring network interfaces in containers. CNI allows different network solutions to be integrated with Kubernetes, facilitating communication between Pods (groups of containers) and services.

With this, we have different network plugins, which follow the CNI specification, and which can be used in Kubernetes. Weave Net is one such network plugin.

Among the most used network plugins in Kubernetes, we have:

- **Calico** is one of the most popular and widely used networking plugins in Kubernetes. It provides network security and allows the implementation of network policies. Calico uses BGP (Border Gateway Protocol) to route traffic between cluster nodes, providing efficient and scalable performance.

- **Flannel** is a simple and easy-to-configure networking plugin designed for Kubernetes. It creates an overlay network that allows Pods to communicate with each other, even on different nodes in the cluster. Flannel assigns an IP range to each node and uses a simple protocol to route traffic between nodes.

- **Weave** is another popular networking solution for Kubernetes. It provides an overlay network that allows communication between Pods on different nodes. Additionally, Weave supports network encryption and network policy management. It can also be integrated with other solutions such as Calico to provide additional security and network policy capabilities.

- **Cilium** is a network plugin focused on security and performance. It uses BPF (Berkeley Packet Filter) to provide high-performance network and security policies. Cilium also offers advanced features such as load balancing, monitoring, and network troubleshooting.

- **Kube-router** is a lightweight networking solution for Kubernetes. It uses BGP and IPVS (IP Virtual Server) to route traffic between cluster nodes, providing efficient and scalable performance. Kube-router also supports network policies and allows the implementation of firewalls between Pods.

These are just some of the most popular and widely used networking plugins in Kubernetes. You can find a complete list of networking plugins on the Kubernetes website.

Now, which one should you choose? The answer is simple: whatever best suits your needs. Each network plugin has its advantages and disadvantages, and you should choose the one that best suits your environment.

Try to use always plugins that are already validated and well-accepted by the community, such as Weave Net, Calico, Flannel, etc.

The most used is `Weave Net` for its simplicity of installation and the features it offers.

`Cilium`, is another interesting and very complete that has a very active community, in addition to using BPF, which is a super hot topic in the Kubernetes world!

&nbsp;
Once we already have our cluster initialized and Weave Net installed, let's create a Deployment to test communication between Pods.

```bash
kubectl create deployment nginx --image=nginx --replicas 3
kubectl get pods -o wide
```
&nbsp;
```bash
root@ip-172-31-53-44:~# kubectl create deployment nginx --image=nginx --replicas 3
deployment.apps/nginx created
root@ip-172-31-53-44:~# 
root@ip-172-31-53-44:~# kubectl get pods -o wide
NAME                     READY   STATUS    RESTARTS   AGE   IP          NODE             NOMINATED NODE   READINESS GATES
nginx-7854ff8877-g57gn   1/1     Running   0          7s    10.32.0.2   ip-172-31-56-3   <none>           <none>
nginx-7854ff8877-lfth7   1/1     Running   0          7s    10.32.0.3   ip-172-31-56-3   <none>           <none>
nginx-7854ff8877-vnz7b   1/1     Running   0          7s    10.32.0.4   ip-172-31-56-3   <none>           <none>
root@ip-172-31-53-44:~#
```
&nbsp;
From the worker, we can check if any of the NGINXs PODs are loading the default HTML page.
```bash
root@ip-172-31-56-3:~# curl -k http://10.32.0.3
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
root@ip-172-31-56-3:~#
```
&nbsp;
That's it, our cluster is up and running and the Pods are running on different nodes.
