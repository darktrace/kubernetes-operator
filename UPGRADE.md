# Upgrading the Darktrace Kubernetes Operator

## Contents

- [Version scheme](#version-scheme)
- [Upgrade procedure](#upgrade-procedure)
  - [Before you upgrade](#before-you-upgrade)
  - [Performing the upgrade](#performing-the-upgrade)
  - [After you upgrade](#after-you-upgrade)
  - [Rollback](#rollback)
- [Skipping versions](#skipping-versions)
- [Version notes](#version-notes)

---

## Version scheme

Operator versions follow [semantic versioning](https://semver.org) with optional
pre-release tags:

```text
<major>.<minor>.<patch>[-<stage>.<n>]
```

| Stage                | Example         | Audience       |
| -------------------- | --------------- | -------------- |
| Beta                 | `1.2.0-beta.1`  | Early adopters |
| General Availability | `1.2.0`         | All users      |

**Beta** — Feature-complete with new functionality available ahead of general
release. Recommended for users who want early access to improvements and are
comfortable providing feedback.

**General Availability** — Production-ready. Recommended for all deployments.

When choosing a version, select the latest General Availability (GA) release unless you have a specific
reason to opt into a Beta for early access to new features.

---

## Upgrade procedure

These steps apply to **every** upgrade regardless of version.

### Before you upgrade

1. Check the [version notes](#version-notes) below for every version between
   your current and target version.
2. Back up your CRDs:

   ```bash
   kubectl get crds -l app.kubernetes.io/name=dt-k8ssensor -o yaml > crds-backup.yaml
   ```

3. Note your current operator version:

   ```bash
   kubectl get deployment dt-k8ssensor -n darktrace -o jsonpath='{.spec.template.spec.containers[0].image}'
   ```

### Performing the upgrade

#### Using YAML manifests

Apply the target version's manifest directly from the repository:

```bash
kubectl apply --server-side -f https://raw.githubusercontent.com/darktrace/kubernetes-operator/<version>/dt-k8ssensor-operator.yaml
```

This updates CRDs, RBAC, and the operator deployment in a single step.

#### Using Helm

Upgrade the chart to the target version:

```bash
helm upgrade dt-k8ssensor-operator oci://public.ecr.aws/darktrace/k8ssensor-operator-chart \
  --version <version> \
  --namespace darktrace
```

This updates CRDs, RBAC, and the operator deployment. The DtK8sSensor CR is
not affected — the operator handles any reconciliation of managed components
after the upgrade.

Upgrading CRDs may introduce new validation rules or required fields that
affect existing custom resources. Review the [version notes](#version-notes)
for any CRD-related changes before upgrading. For more detail on how Helm
handles CRD lifecycle, see the
[Helm documentation on CRD limitations](https://helm.sh/docs/chart_best_practices/custom_resource_definitions/#some-caveats-and-explanations).

### After you upgrade

1. Verify the operator pod is running:

   ```bash
   kubectl get pods -n darktrace -l app=dt-k8ssensor
   ```

2. Check logs for errors:

   ```bash
   kubectl logs -n darktrace -l app=dt-k8ssensor --tail=50
   ```

3. Confirm managed components are healthy:

   ```bash
   unhealthy=$(kubectl get dtk8ssensor dt-k8ssensor -o jsonpath='{range .status.conditions[?(@.status!="True")]}{.type}: {.message}{"\n"}{end}'); [ -z "$unhealthy" ] && echo "HEALTHY" || echo -e "UNHEALTHY\n$unhealthy"
   ```

   Output is `HEALTHY` on success, or `UNHEALTHY` with the failing conditions
   listed. If the failure indicates an application error, contact Darktrace
   support via the [Customer Portal](https://customerportal.darktrace.com).

### Rollback

#### Using YAML manifests

To re-apply the previous version's manifest:

```bash
kubectl apply --server-side -f https://raw.githubusercontent.com/darktrace/kubernetes-operator/<previous-version>/dt-k8ssensor-operator.yaml
```

#### Using Helm

```bash
helm rollback dt-k8ssensor-operator <revision> --namespace darktrace
```

To find the previous revision number:

```bash
helm history dt-k8ssensor-operator --namespace darktrace
```

---

## Migrating from YAML manifests to Helm

If you have an existing installation deployed via `kubectl apply` and want to
switch to Helm-managed upgrades, follow these steps:

1. Annotate and label the CRDs so Helm can adopt them. CRDs must be preserved
   to retain existing DtK8sSensor resources and all managed sub-resources:

   ```bash
   RELEASE_NAME=dt-k8ssensor-operator
   NAMESPACE=darktrace

   for CRD in $(kubectl get crds --no-headers -o custom-columns=NAME:.metadata.name | grep darktrace.com); do
       kubectl annotate crd "$CRD" meta.helm.sh/release-name="$RELEASE_NAME" --overwrite
       kubectl annotate crd "$CRD" meta.helm.sh/release-namespace="$NAMESPACE" --overwrite
       kubectl label crd "$CRD" app.kubernetes.io/managed-by=Helm --overwrite
   done
   ```

2. Delete the existing operator resources (not the CRDs or DtK8sSensor CRs):

   ```bash
   kubectl delete -n darktrace deployment,serviceaccount,role,rolebinding,service -l app.kubernetes.io/part-of=dt-k8ssensor
   kubectl delete clusterrole,clusterrolebinding -l app.kubernetes.io/part-of=dt-k8ssensor
   ```

3. Install the operator via Helm. Helm will adopt the annotated CRDs and
   create fresh copies of all other resources:

   ```bash
   helm install dt-k8ssensor-operator oci://public.ecr.aws/darktrace/k8ssensor-operator-chart \
     --version <version> \
     --namespace darktrace
   ```

4. Verify the operator is running and managed resources are healthy:

   ```bash
   kubectl get pods -n darktrace
   kubectl get dtk8ssensor dt-k8ssensor -o jsonpath='{.status.conditions}'
   ```

The DtK8sSensor CR and all managed sub-resources (server, agents) continue
running throughout this process. The new operator pod will reconcile them on
startup.

---

## Skipping versions

Incremental upgrades (one minor version at a time) are recommended. If you skip
versions, read **all** intermediate [version notes](#version-notes) — they may
contain required migration steps.

---

## Version notes

Notes are only added when a version requires user action or changes behavior
beyond the standard procedure above. If a version isn't listed here, no
additional steps are needed.

<!--
Template for new entries (add newest at the top):

### vX.Y.Z

**Date:** YYYY-MM-DD | **Breaking:** Yes/No

**Before upgrading:**
- Description of required action.

**Behavioral changes:**
- Description of changed defaults or behavior.

**Deprecations:**
- `spec.oldField` is deprecated, will be removed in vX+1.0.0. Migrate to `spec.newField`.
-->
