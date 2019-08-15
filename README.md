# Bare metal cluster with Kubernetes, Istio & MetalLB
This github repository is material of a presentation of VietKubers core-team at OpenInfra Days Vietnam 2019

## Requirements
>This lab is carried out on Ubuntu 16.04

#### 1. Install vagrant
```console
sudo apt install vagrant
```

#### 2. Install virtualbox
```console
sudo apt install virtualbox
```

## Setting up K8s cluster 

#### 1. Clone the repository `k8s-istio-metallb-hands-on-lab`
```console
git clone https://github.com/vietkubers/k8s-istio-metallb-hands-on-lab.git
```
#### 2. Create VMs for K8s nodes
```console
cd k8s-istio-metallb-hands-on-lab/k8s-cluster
vagrant up
```
After the vagrant work has done, there are 3 VMs up. Now we will set up K8s cluster with 1 `Master node` and 2 `Worker nodes`. Remote to VMs by command:
```console
vagrant ssh k8s-master (or k8s-worker1 / k8s-worker2)
```

#### 3. Deploying K8s cluster

3.1. Deploying Master node (In case of using flannel overlay network)
```console
sudo kubeadm init --apiserver-advertise-address=<PRIVATE-MASTER-IP> --pod-network-cidr=10.244.0.0/16
```
In this lab, <PRIVATE-MASTER-IP>=192.168.205.10

3.2. Start using cluster
```console
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

3.3. Applying a pod network
```console
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/bc79dd1505b0c8681ece4de4c0d86c5cd2643275/Documentation/kube-flannel.yml
```

3.4. Joining `k8s-worker1` and `k8s-worker2` to the cluster
```console
sudo kubeadm join <PRIVATE-MASTER-IP>:6443 --token <token> --discovery-token-ca-cert-hash sha256:<hash>
```
