# Darktrace Kubernetes Operator

The Darktrace Kubernetes Operator provides comprehensive security monitoring and threat detection for Kubernetes environments through automated deployment and management of Darktrace sensors.

## Key Features

- **API Security Monitoring**: Detect malicious Kubernetes API events using AuditSensor
- **Network Traffic Analysis**: Identify suspicious network activity with VSensor
- **Container Security**: Manage environment taxonomy aligned with Kubernetes API through ContainerSensor
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
- [Custom Resource Definitions (CRDs)](https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/) for `KSensor`, `VSensor`, `AuditSensor`, and `ContainerSensor`

**Installation Steps:**

1. Download the latest `darktrace-operator.yaml` from the [releases page](releases)
2. Deploy the operator:
   ```bash
   NAMESPACE=<target-namespace> IMAGE=darktrace-operator:latest envsubst < darktrace-operator.yaml | kubectl apply -f -
   ```

### Verification

Confirm successful installation:

```bash
# Verify operator deployment
kubectl get deployment darktrace-operator -n <namespace>

# Check CRD installation
kubectl get crd | grep darktrace

# Validate operator logs
kubectl logs -l app=darktrace-operator -n <namespace>
```

## Configuration

After successful operator installation, configure and deploy sensors through the Darktrace /CLOUD management console to begin protecting your Kubernetes environment.

For detailed configuration instructions, refer to the Darktrace Customer Portal.
