# Synterix Edge Helm Chart

Helm chart for deploying Synterix Edge components to Kubernetes clusters with configurable namespace permissions.

## Features

- Flexible namespace management (cluster-wide or specific namespaces)
- Automatic RBAC configuration
- Configurable deployment parameters
- Supports both single-namespace and multi-namespace deployments

## Prerequisites

- Kubernetes 1.19+
- Helm 3.2.0+
- Cluster-admin privileges for installation

## Installation

### Add Helm Repository

```bash
helm repo add synterix-edge https://<your-repo-url>
helm repo update
```

### Install with Cluster-Wide Access

For cluster-wide access (admin permissions):

```bash
helm install synterix-edge synterix-edge/synterix-edge --set namespaces[0]="*"
```

### Install with Specific Namespaces

For access to specific namespaces:

```bash
helm install synterix-edge synterix-edge/synterix-edge --set namespaces={comblox,comblox-test}
```

### Install with Custom Values

Create a custom values file (`custom-values.yaml`) and install:

```bash
helm install synterix-edge synterix-edge/synterix-edge -f custom-values.yaml
```

## Configuration

| Parameter                    | Description                                               | Default                                                        |
|------------------------------|-----------------------------------------------------------|----------------------------------------------------------------|
| `namespaces`                 | List of namespaces to manage (use `"*"` for cluster-wide) | `["comblox","comblox-test"]`                                   |
| `deployment.replicaCount`    | Number of replicas                                        | `1`                                                            |
| `deployment.image.gateway`   | Gateway image configuration                               | `repository: sebertes/synterix-central-gateway`, `tag: latest` |
| `deployment.image.proxy`     | Proxy image configuration                                 | `repository: sebertes/synterix-kube-proxy`, `tag: latest`      |
| `deployment.resources.proxy` | Resource limits for proxy container                       | See [values.yaml](values.yaml)                                 |
| `gateway.url`                | Gateway connection URL                                    | `"wss://yuntuops.bjttsx.com:443/synterix/gateway"`             |
| `gateway.token`              | Edge authentication token                                 | `"oWSGibL0fLWMlyzTc3ybdi3t1rbQjj"`                             |
| `gateway.edgeId`             | Edge identifier                                           | `"1780350654385859692002162"`                                  |
| `service.type`               | Kubernetes Service type                                   | `ClusterIP`                                                    |
| `service.ports.gateway`      | Gateway service port configuration                        | `port: 7088`, `targetPort: 7088`                               |

## Architecture

The chart deploys:

1. **Namespace** (when using cluster-wide mode)
2. **RBAC Components**:
    - ClusterRole with admin permissions
    - ServiceAccount
    - RoleBindings (for specific namespaces) or ClusterRoleBinding (for cluster-wide)
3. **Application Components**:
    - Deployment with gateway and proxy containers
    - Service for accessing the gateway

## Upgrade

```bash
helm upgrade synterix-edge synterix-edge/synterix-edge -f custom-values.yaml
```

## Uninstall

To completely remove the chart:

```bash
helm uninstall synterix-edge
```

Note: This will not remove any namespaces that were created.

## Security Considerations

1. When using `namespaces: ["*"]`, the service account will have cluster-admin privileges
2. Always secure your edge tokens and IDs
3. Consider using Kubernetes Secrets for sensitive values in production

## Support

For support, please contact:
- Email: synterix@qq.com
- Documentation: [https://synterix.cloud](https://synterix.cloud)