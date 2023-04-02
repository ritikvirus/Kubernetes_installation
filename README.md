# Kubernetes_installation
> To set up Kubernetes manually on N nodes, you will need to designate one node as the control-plane (master) node and the other as a worker node. In this guide, we'll use Ubuntu as the operating system. Ensure that both nodes meet the following minimum requirements:

# The following ports should be open:

## Control-plane node(s):
* 6443 (API server)
* 2379-2380 (etcd)
* 10250 (kubelet API)
* 10251 (kube-scheduler)
* 10252 (kube-controller-manager)
## Worker nodes:
* 10250 (kubelet API)
* 30000-32767 (NodePort services)
## System Requirements
* 2 or more CPU cores
* 2 GB or more of RAM
* A unique hostname, MAC address, and product_uuid for every node
* Full network connectivity between all machines in the cluster
* Swap disabled (Recommended)

## Step 1: Set up the environment These Commands Run every Nodes Work and Master Both
```
sudo su
```
```
echo -e '#!/bin/bash\nsudo apt-get update && sudo apt-get upgrade -y\nsudo apt-get install -y docker.io\nsudo systemctl enable docker\nsudo systemctl start docker\nsudo apt-get update && sudo apt-get install -y apt-transport-https curl\ncurl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -\necho "deb https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee -a /etc/apt/sources.list.d/kubernetes.list\nsudo apt-get update -y\nsudo apt-get install -y kubelet kubeadm kubectl\nsudo apt-mark hold kubelet kubeadm kubectl' > install_kubeadm.sh && chmod +x install_kubeadm.sh && ./install_kubeadm.sh
```
## Step 2: Initialize the control-plane node (Master NODE)
1. On the control-plane node, initialize the Kubernetes cluster using kubeadm:
```
sudo kubeadm init --pod-network-cidr=10.244.0.0/16
```
2. After the initialization is complete, copy the kubeadm join command with the associated token and save it for later use.
3. Set up the Kubernetes configuration for the control-plane node:
```
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```
## Step 3: Set up the pod network
1. On the control-plane node, install a pod network. In this example, we'll use Flannel:
```
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
```
## Step 4: Join the worker node to the cluster (Copy Your Generated Token And Paste On Your Work Node)
1. On the worker node, execute the kubeadm join command you saved earlier. It should look like this:
```
sudo kubeadm join <control-plane-ip>:6443 --token <token> --discovery-token-ca-cert-hash sha256:<hash>
```
## Step 5: Verify the cluster
```
kubectl get nodes
```
### You should see both nodes in the "Ready" state. Congratulations, you have successfully set up a N-node Kubernetes cluster manually!
## Error Handling
> **Note**
> The connection to the server 172.31.2.185:6443 was refused - did you specify the right host or port?
> Make sure your ports is open if your ports already open then use this steps

#### Your Facing like this error then run this command 
```
sudo cp /etc/kubernetes/admin.conf $HOME/
sudo chown $(id -u):$(id -g) $HOME/admin.conf
export KUBECONFIG=$HOME/admin.conf
```
