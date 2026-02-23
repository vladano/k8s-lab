# Cilium CNI

Cilium uses eBPF for advanced networking.

Minimum per node:

- 8GB RAM

---

# Initialize Cluster

```bash
sudo kubeadm init \
  --apiserver-advertise-address=192.168.59.10 \
  --pod-network-cidr=10.0.0.0/16
```

Install Cilium CLI

```bash
curl -LO https://github.com/cilium/cilium-cli/releases/latest/download/cilium-linux-amd64.tar.gz
sudo tar xzvf cilium-linux-amd64.tar.gz -C /usr/local/bin
```

Install Cilium

```bash
cilium install
```

Verify

````bash
cilium status

kubectl get pods -A
kubectl get nodes
```bash

All nodes should be Ready.

Snapshot Recommendation
Take snapshot: After-Cilium
````
