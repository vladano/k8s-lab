````markdown
# Calico + Istio

This setup combines:

- Calico (CNI)
- Istio (Service Mesh)

---

# Prerequisite

Restore snapshot: `After-Calico`

Increase RAM to 8GB per node.

---

# Install Istio

```bash
curl -L https://istio.io/downloadIstio | sh -
sudo mv istio-*/bin/istioctl /usr/local/bin/
```
````

Install:

```bash
istioctl install --set profile=default -y
```

Enable Injection

```bash
kubectl label namespace default istio-injection=enabled
```

Deploy Sample App

```bash
kubectl apply -f samples/bookinfo/platform/kube/bookinfo.yaml
```

Verify

```bash
kubectl get pods -n istio-system
istioctl proxy-status
```

Pods should show sidecar containers.
