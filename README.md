# Darktrace Kubernetes Operator

The Darktrace Kubernetes Operator lets you:

- Identify malicious Kubernetes API events (thanks to auditSensor).
- Identify malicious Kubernetes network traffic (thanks to vSensor).
- Manage your environment in taxonomy in-line with Kubernetes' API (thanks to containerSensor).
- Monitor the health of your Darktrace Kubernetes deployment.
- Easily manage updates to your Darktrace deployment.

## Setting up the Kubernetes Operator

### Prerequisites

Darktrace Kubernetes Operator must be configured with valid Darktrace Active AI Security Portal client credentials. The operator uses these credentials to authenticate requests to /CLOUD and /NETWORK to automate deployment and provide core functionality.

### Installation

The operator installation creates a dedicated namespace, an operator `Deployment` in that namespace, read only RBAC for the operator, and [Custom Resource Definitions (CRDs)](https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/) of types: `KSensor`, `VSensor`, `AuditSensor`, `ContainerSensor`.

You can install the Darktrace Kubernetes Operator by:

1. Downloading the `darktrace-operator.yaml` manifest file.
2. `$ NAMESPACE=<namespace to deploy resources to> IMAGE=darktace-operator:latest envsubst < darktrace-operator.yaml | kubectl apply -f -`

### Validation

Verify that the Darktrace operator deployment exists in the configured namespace and is healthy. Check that all the CRDs exist.

## Deploy a `KSensor` and Start Protecting your Kubernetes Cluster

To start protecting your Kubernetes Cluster, begin the setup process in the /CLOUD management console.
