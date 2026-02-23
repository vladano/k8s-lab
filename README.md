# k8s-lab

K8S installation using VirtualBox - Flannel, Calico, Cilium, Istio

🧱 Infrastructure Architecture

                      Windows / Linux Host
                              │
                              │
               ┌──────────────┴───────────────┐
               │ VirtualBox Host-Only Network │
               │      192.168.59.0/24         │
               └──────────────┬───────────────┘
                              │
        ┌─────────────────────┼─────────────────────┐
        │                     │                     │

┌──────────────┐ ┌──────────────┐ ┌──────────────┐
│ k8s-master │ │ k8s-worker1 │ │ k8s-worker2 │
│ 192.168.59.10│ │192.168.59.11 │ │192.168.59.12 │
│ ControlPlane │ │ Worker │ │ Worker │
└──────────────┘ └──────────────┘ └──────────────┘

Each VM:

- Ubuntu Server 24.04
- containerd
- kubeadm
- kubelet
- kubectl

# Kubernetes On-Prem Lab (VirtualBox)

This repository provides a complete step-by-step guide for building a Kubernetes cluster on VirtualBox using kubeadm and containerd.

The lab demonstrates multiple CNI implementations and service mesh integrations:

- Flannel
- Calico
- Cilium
- Calico + Istio
- Cilium + Istio

🌐 CNI + Istio Traffic Flow
Standard CNI
Pod A → CNI (Flannel/Calico/Cilium) → Pod B

With Istio
Pod A
│
▼
Envoy Sidecar
│
▼
CNI (Calico/Cilium)
│
▼
Envoy Sidecar
│
▼
Pod B

🌍 NodePort Access Flow
Browser (Host)
│
▼
192.168.59.11:NodePort
│
▼
Istio Ingress Gateway
│
▼
Service
│
▼
Application Pod

---

☸ Kubernetes Internal Architecture

                         +---------------------+
                         |   k8s-master        |
                         |   kube-apiserver    |
                         +----------+----------+
                                    |
                    +---------------+----------------+
                    |                                |
             +------+-------+                +------+-------+
             | k8s-worker1  |                |  k8s-worker2 |
             |  kubelet     |                |  kubelet     |
             +------+-------+                +------+-------+
                    |                                |
                +---+---+                        +---+---+
                | Pods  |                        | Pods  |
                +-------+                        +-------+

# 🧱 Lab Architecture

3-node Kubernetes cluster:

| Node        | IP            | Role          |
| ----------- | ------------- | ------------- |
| k8s-master  | 192.168.59.10 | Control Plane |
| k8s-worker1 | 192.168.59.11 | Worker        |
| k8s-worker2 | 192.168.59.12 | Worker        |

Network: VirtualBox Host-Only Adapter (192.168.59.0/24)

Each VM:

- Ubuntu Server 24.04 LTS
- containerd
- kubeadm
- kubelet
- kubectl

---

# 💻 Host Requirements

Recommended minimum:

- CPU: 8 cores
- RAM: 32GB
- Disk: 100GB+
- VirtualBox installed

---

# 🖥 VM Configuration

Each VM:

- 4 vCPU (8 vCPU for Cilium/Istio testing)
- 4GB RAM minimum (8GB recommended for Cilium/Istio)
- 2 NICs:
  - NAT (internet access)
  - Host-only (cluster communication)

---

# 🔧 Base OS Preparation (All Nodes)

## Update System

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y curl vim git apt-transport-https ca-certificates gpg chrony
```

Disable Swap

```bash
sudo swapoff -a
sudo sed -i '/swap/d' /etc/fstab
```

Enable Required Kernel Modules

```bash
sudo modprobe br_netfilter
```

Create config:

```bash
sudo tee /etc/modules-load.d/k8s.conf <<EOF
br_netfilter
EOF
```

Sysctl:

```bash
sudo tee /etc/sysctl.d/k8s.conf <<EOF
net.bridge.bridge-nf-call-iptables=1
net.bridge.bridge-nf-call-ip6tables=1
net.ipv4.ip_forward=1
EOF

sudo sysctl --system
```

📦 Install containerd

```bash
sudo apt install -y containerd
sudo mkdir -p /etc/containerd
containerd config default | sudo tee /etc/containerd/config.toml
sudo sed -i 's/SystemdCgroup = false/SystemdCgroup = true/' /etc/containerd/config.toml
sudo systemctl restart containerd
sudo systemctl enable containerd
```

☸ Install Kubernetes Components
Add repository:

```bash
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.29/deb/Release.key | \
sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg

echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] \
https://pkgs.k8s.io/core:/stable:/v1.29/deb/ /' | \
sudo tee /etc/apt/sources.list.d/kubernetes.list
```

Install:

```bash
sudo apt update
sudo apt install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
```

🚀 Initialize Cluster
On master:

```bash
sudo kubeadm init --apiserver-advertise-address=192.168.59.10 --pod-network-cidr=<CNI-specific>
```

Configure kubectl:

```bash
mkdir -p $HOME/.kube
sudo cp /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

Join workers using generated join command.

🌐 Choose Your Networking

See individual folders:
Flannel
Calico
Cilium
Calico + Istio
Cilium + Istio

🧪 Testing
Basic test:

```bash
kubectl create deployment nginx --image=nginx
kubectl expose deployment nginx --port=80
kubectl get pods -o wide
```

🧹 Reset Cluster

```bash
sudo kubeadm reset -f
sudo rm -rf /etc/cni/net.d/*
```

📌 Notes

Do not use VirtualBox NAT IP for NodePort access.

Always use Host-only network (192.168.59.x).
Take snapshots before testing different CNIs.

# Kubernetes On-Prem Lab (VirtualBox)

![Kubernetes](https://img.shields.io/badge/Kubernetes-v1.29-blue?logo=kubernetes)
![Container Runtime](https://img.shields.io/badge/Containerd-1.7-blue?logo=docker)
![Ubuntu](https://img.shields.io/badge/Ubuntu-24.04-E95420?logo=ubuntu)
![CNI](https://img.shields.io/badge/CNI-Flannel%20|%20Calico%20|%20Cilium-green)
![Service Mesh](https://img.shields.io/badge/Service%20Mesh-Istio-purple)
![Platform](https://img.shields.io/badge/Platform-VirtualBox-lightgrey)
![License](https://img.shields.io/badge/License-MIT-yellow)

![Last Commit](https://img.shields.io/github/last-commit/vladano/k8s-onpremlab)
![Repo Size](https://img.shields.io/github/repo-size/vladano/k8s-onpremlab)
