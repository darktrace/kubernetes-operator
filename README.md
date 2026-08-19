# Darktrace Kubernetes Operator

The Darktrace Kubernetes Operator provides comprehensive security monitoring and threat detection for Kubernetes environments through automated deployment and management of Darktrace sensors.

## Key Features

- **API Security Monitoring**: Detect malicious Kubernetes API events using DtK8sSensorAuditAgent
- **Network Traffic Analysis**: Identify suspicious network activity with DtK8sSensorServer
- **Container Security**: Manage environment taxonomy aligned with Kubernetes API through DtK8sSensorClusterAnalyzer
- **Health Monitoring**: Track deployment status and sensor health
- **Automated Updates**: Streamlined management of Darktrace component updates

## Installation

### Prerequisites

- Kubernetes cluster with admin privileges
- Valid Darktrace Active AI Security Portal client credentials
- Network connectivity to Darktrace cloud services

The operator requires authenticated access to Darktrace's /CLOUD and /NETWORK endpoints for automated deployment and core functionality.

### Deployment

The installation process creates:

- Dedicated operator namespace
- Operator deployment with appropriate RBAC permissions
- [Custom Resource Definitions (CRDs)](https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/) for `DtK8sSensor`, `DtK8sSensorServer`, `DtK8sSensorAuditAgent`, and `DtK8sSensorClusterAnalyzer`

**Installation Steps:**

1. Download the latest `dt-k8ssensor-operator.yaml` from the [releases page](releases)
2. Deploy the operator:

   ```bash
   NAMESPACE=<target-namespace> IMAGE=dt-k8ssensor:latest envsubst < dt-k8ssensor-operator.yaml | kubectl apply -f -
   ```

### Verification

Confirm successful installation:

```bash
# Verify operator deployment
kubectl get deployment dt-k8ssensor -n <namespace>

# Check CRD installation
kubectl get crd | grep darktrace

# Validate operator logs
kubectl logs -l app=dt-k8ssensor -n <namespace>
```

## Configuration

After successful operator installation, configure and deploy sensors through the Darktrace /CLOUD management console to begin protecting your Kubernetes environment.

For detailed configuration instructions, refer to the Darktrace Customer Portal.

## Upgrading

For instructions on upgrading to a newer version of the operator, including
version-specific notes on breaking changes, see the [Upgrade Guide](UPGRADE.md).

## Uninstalling

Uninstall all Darktrace Kubernetes resources from a cluster:

```bash
kubectl delete dtk8ssensor --all --wait=true --ignore-not-found
kubectl delete crd -l app.kubernetes.io/name=dt-k8ssensor
kubectl delete all --all-namespaces -l app.kubernetes.io/name=dt-k8ssensor
kubectl delete crd,clusterrole,clusterrolebinding,role,serviceaccount,rolebinding --all-namespaces -l app.kubernetes.io/name=dt-k8ssensor
```
