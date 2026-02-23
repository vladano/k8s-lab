# Flannel CNI

Flannel is a simple and lightweight Kubernetes CNI.

Recommended for:
- Small labs
- Minimal resource usage
- Learning basics

Minimum per node: 4GB RAM

---

# Initialize Cluster

On master:
```bash
sudo kubeadm init \
  --apiserver-advertise-address=192.168.59.10 \
  --pod-network-cidr=10.244.0.0/16
```

Install Flannel

```bash
kubectl apply -f https://raw.githubusercontent.com/flannel-io/flannel/master/Documentation/kube-flannel.yml
```

Verify

```bash
kubectl get pods -A
kubectl get nodes
```

Nodes should become Ready.

Snapshot Recommendation
Take snapshot: After-Flannel
