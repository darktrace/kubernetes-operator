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

#### Option 1: Using YAML manifests

1. Download the latest `dt-k8ssensor-operator.yaml` from the [releases page](releases)
2. Deploy the operator:

   ```bash
   kubectl apply --server-side -f dt-k8ssensor-operator.yaml
   ```

#### Option 2: Using Helm

```bash
helm install dt-k8ssensor-operator oci://public.ecr.aws/darktrace/k8ssensor-operator-chart \
  --version <version> \
  --namespace darktrace \
  --create-namespace
```

The chart is also available from Docker Hub (`oci://registry-1.docker.io/darktrace/k8ssensor-operator-chart`).
Public ECR is recommended as it has higher rate limits.

### Configuring the sensor

After the operator is installed, create the auth secret and deploy a DtK8sSensor
custom resource to begin monitoring:

```bash
kubectl create secret generic dt-k8ssensor-auth \
  --namespace darktrace \
  --from-literal=clientSecret=<your-client-secret>
```

Then apply a DtK8sSensor custom resource configured with your Darktrace
credentials. Refer to the Darktrace Customer Portal for detailed configuration
instructions.

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

### Using Helm

Delete all DtK8sSensor custom resources first to allow finalizer cleanup,
then uninstall the chart:

```bash
kubectl delete dtk8ssensors --all --timeout=120s --ignore-not-found
helm uninstall dt-k8ssensor-operator -n darktrace
```

CRDs are retained by default after uninstall. To fully remove them:

```bash
kubectl delete crd -l app.kubernetes.io/part-of=dt-k8ssensor
```

### Using YAML manifests

```bash
kubectl delete dtk8ssensor --all --wait=true --ignore-not-found
kubectl delete -f dt-k8ssensor-operator.yaml
kubectl delete crd -l app.kubernetes.io/part-of=dt-k8ssensor
```
