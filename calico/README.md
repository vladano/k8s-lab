# Calico CNI

Calico provides:

- Advanced Network Policy
- eBPF dataplane support
- Production-ready networking

Minimum per node: 4GB RAM

---

# Initialize Cluster

```bash
sudo kubeadm init \
  --apiserver-advertise-address=192.168.59.10 \
  --pod-network-cidr=192.168.0.0/16
```

Install Calico

```bash
kubectl apply -f https://raw.githubusercontent.com/projectcalico/calico/v3.27.2/manifests/calico.yaml
```

Verify

```bash
kubectl get pods -A
kubectl get nodes
```

Ensure:

calico-node Running
calico-kube-controllers Running

Snapshot Recommendation
Take snapshot: After-Calico
