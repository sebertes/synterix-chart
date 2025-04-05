[Synterix website](https://synterix.cloud)

In SvHelm chart for deploying the Synterix platform on Kubernetes.

## Chart Details

- Deploys:
    - Synterix Central Gateway
    - Synterix Kube Proxy
    - Synterix Central Admin (StatefulSet with persistent storage)
    - Required RBAC, ServiceAccounts, and Secrets
    - Configurable Ingress

## Prerequisites

- Kubernetes 1.19+
- Helm 3.2.0+
- Ingress Controller (if using Ingress)
- PV provisioner support in the underlying infrastructure (for StatefulSet persistence)

## Installation

### Add Helm Repository

```bash
helm repo add synterix https://chart.synterix.cloud
helm repo update
```

### Install Chart

```bash
helm install synterix synterix/synterix \
  --namespace synterix \
  --create-namespace \
  --version 0.1.0
```

### Customized Installation

```bash
helm install synterix synterix/synterix \
  --namespace synterix \
  --set ingress.host="custom.example.com" \
  --set centralAdmin.storage.size="5Gi" \
  --set global.secret.data="$(echo -n 'newsecret' | base64)"
```

## Configuration

| Parameter | Description | Default                             |
|-----------|-------------|-------------------------------------|
| `global.namespace` | Namespace for all resources | `synterix`                          |
| `global.secret.name` | Secret name | `synterix-secret`                   |
| `global.secret.data` | Base64 encoded secret value | `c2VjcmV0` ("secret")               |
| `serviceAccount.name` | ServiceAccount name | `synterix-central-account`          |
| `rbac.bindingName` | ClusterRoleBinding name | `synterix-central-account-binding`  |
| `centralGateway.image` | Central Gateway image | `sebertes/synterix-central-gateway` |
| `centralGateway.tag` | Image tag | `master`                            |
| `centralGateway.port` | Service port | `7089`                              |
| `kubeProxy.image` | Kube Proxy image | `sebertes/synterix-kube-proxy`      |
| `kubeProxy.tag` | Image tag | `master`                            |
| `kubeProxy.port` | Service port | `7014`                              |
| `centralAdmin.image` | Central Admin image | `sebertes/synterix-central-admin`   |
| `centralAdmin.tag` | Image tag | `master`                            |
| `centralAdmin.port` | Service port | `7088`                              |
| `centralAdmin.dbPath` | Database path | `"/synterix/synterix.db"`           |
| `centralAdmin.storage.size` | PVC storage size | `1Gi`                               |
| `ingress.enabled` | Enable Ingress | `true`                              |
| `ingress.className` | Ingress class | `nginx`                             |
| `ingress.host` | Ingress host | `xxxx.com`                          |
| `ingress.path` | Ingress path | `/synterix/(.*)`                    |

## Resources

By default, the chart configures resource requests and limits:

- **Central Gateway**:
    - Requests: 200m CPU, 256Mi Memory
    - Limits: 500m CPU, 512Mi Memory

- **Kube Proxy**:
    - Requests: 200m CPU, 256Mi Memory
    - Limits: 500m CPU, 512Mi Memory

- **Central Admin**:
    - Requests: 200m CPU, 256Mi Memory
    - Limits: 500m CPU, 512Mi Memory

## Persistence

The Central Admin component uses a PersistentVolumeClaim with:

- Access mode: ReadWriteOnce
- Default size: 1Gi

## Upgrading

```bash
helm upgrade synterix synterix/synterix \
  --namespace synterix \
  --version <new-version>
```

## Uninstalling

To uninstall/delete the deployment:

```bash
helm uninstall synterix --namespace synterix
```

Note: This will not remove the PersistentVolumeClaim. To completely remove all resources:

```bash
helm uninstall synterix --namespace synterix
kubectl delete pvc -n synterix -l app.kubernetes.io/instance=synterix
kubectl delete namespace synterix
```

## Troubleshooting

1. **Check Pod Status**:
   ```bash
   kubectl get pods -n synterix
   ```

2. **View Logs**:
   ```bash
   kubectl logs -n synterix -l app=synterix-central
   kubectl logs -n synterix -l app=synterix-central-admin
   ```

3. **Check Persistent Volume**:
   ```bash
   kubectl get pvc -n synterix
   ```