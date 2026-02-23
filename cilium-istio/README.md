# Cilium + Istio

This setup combines:

- Cilium (eBPF CNI)
- Istio (Service Mesh)

---

# Prerequisite

Restore snapshot: `After-Cilium`

Ensure 8GB RAM per node.

---

# Install Istio

```bash
curl -L https://istio.io/downloadIstio | sh -
sudo mv istio-*/bin/istioctl /usr/local/bin/
```

Install:

```bash
istioctl install --set profile=default -y
```

Enable Injection

```bash
kubectl label namespace default istio-injection=enabled
```

Verify

```bash
kubectl get pods -n istio-system
istioctl proxy-status
```

Architecture Flow

Pod → Envoy Sidecar → Cilium (eBPF) → Network

Cilium handles:

- Networking
- Network Policy
- eBPF dataplane

Istio handles:

- mTLS
- Traffic management
- Observability
