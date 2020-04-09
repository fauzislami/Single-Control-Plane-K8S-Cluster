<h1>Kubernetes Single Cluster</h1>

<img src="https://kubernetes.io/images/kubernetes-horizontal-color.png" width="40%">.


<h2>Labs Prerequisites :</h2>

**1. Virtual Machine ( *3 Nodes : 1 Master and 2 Worker* )**
   - **OS :** Ubuntu 18.04 LTS (*Not Mandatory*)
   - **vCPU :** 1
   - **RAM :** 2GB
   - **Network :** 
      - Host-Only Adapter

**2. Cluster Environment**
   - Docker [as container runtime] \
   https://docs.docker.com/get-docker/
   - Flannel [as overlay network] \
   https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
   - Kubernetes for ubuntu xenial \
   https://packages.cloud.google.com/apt/dists/kubernetes-xenial

*In this lab, i use use docker v18.09 and k8s v1.15 during this lab documentation creation in the present time. You might find the latest version of those in the future.*


------------
<h2>Lab Topology</h2>

<img src="https://github.com/islamifauzi/workshop-kubernetes/blob/master/k8s%20topology.png?raw=true" width="60%">.


Do the setup steps below to 3 nodes ( *1 Master and 2 Workers*  ).  The steps with the prefix ** means for master node only and the steps with prefix * means for worker nodes only. The rest is mandatory to run on all nodes.

------------

<h2>Docker Installation</h2>

`sudo apt-get update`

`sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg-agent \
    software-properties-common`

`curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -`

`sudo apt-key fingerprint 0EBFCD88`

`sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"`

`sudo apt-get update`

`sudo apt-get install docker-ce docker-ce-cli containerd.io`

`sudo apt-mark hold docker-ce docker-ce-cli containerd.io`

`sudo systemctl enable --now docker`

<br/>

<sub><b>*P.S. : If the old-version of docker already exists, you can run this command :*<b></sub>

`sudo apt remove docker*`

------------

<h2>Kubernetes Installation</h2>

`curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -`

`sudo apt-add-repository "deb http://apt.kubernetes.io/ kubernetes-xenial main"`

`sudo apt-get update`

`sudo apt-get install -y kubelet kubeadm kubectl`

`sudo apt-mark hold kubelet kubeadm kubectl`


------------

<h2>Cluster Creation</h2>

`sudo swapoff -a`

**`sudo kubeadm init --pod-network-cidr=10.244.0.0/16 --apiserver-advertise-address=<FILL_WITH_YOUR_IP_KUBE_MASTER>`

*`kubeadm join <IP_KUBE_MASTER>:6443 --token <token> --discovery-token-ca-cert-hash sha256:<hash>`

<sub><b>*P.S. : You can exactly find the join command above after the master node initialization is finished.*<b></sub>

`mkdir -p $HOME/.kube`

`sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config`

`sudo chown $(id -u):$(id -g) $HOME/.kube/config`

`kubectl version`

<br/>

<sub><b>*P.S. : If the version of k8s is displayed, it works.*<b></sub>

<h2>Overlay Network Installation </h2>

** `kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml`

** `echo "net.bridge.bridge-nf-call-iptables=1" | sudo tee -a /etc/sysctl.conf`

** `sudo sysctl -p`

** `kubectl get nodes`
<br/>

<sub><b>*P.S. : Check the status of nodes frequently until all of the nodes are in *ready* state*<b></sub>


------------



<h2> Deploy A Simple Application </h2>

`git clone https://github.com/islamifauzi/workshop-kubernetes.git`

`cd /workshop-kubernetes; ls`

`kubectl apply -f simple-app.yaml`

`kubectl get pods`

`kubectl exec -it (nama_pod) -- curl localhost`


------------

<h2> Deploy A Snake Game</h2>

`kubectl apply -f https://raw.githubusercontent.com/google/metallb/v0.8.3/manifests/metallb.yaml
`

`kubectl get pods,serviceaccounts,daemonsets,deployments,roles,rolebindings -n metallb-system `

`cd /workshop-kubernetes; ls`

`kubectl apply -f snake-games.yaml`

`kubectl apply -f metal-lb.yaml`

`kubectl apply -f snake-service.yaml`


------------

<h2>Additional</h2>

In order to add more worker nodes to join the cluster, you can use this command to get the token :

`kubectl token create --print-join-command`

Then use this command :

`kubeadm join <IP_KUBE_MASTER>:6443 --token <token> --discovery-token-ca-cert-hash sha256:<hash>`





