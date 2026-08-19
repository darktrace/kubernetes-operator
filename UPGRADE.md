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

Apply the target version's manifest directly from the repository:

```bash
kubectl apply --server-side -f https://raw.githubusercontent.com/darktrace/kubernetes-operator/<version>/dt-k8ssensor-operator.yaml
```

This updates CRDs, RBAC, and the operator deployment in a single step.

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

To re-apply the previous version's manifest:

```bash
kubectl apply --server-side -f https://raw.githubusercontent.com/darktrace/kubernetes-operator/<previous-version>/dt-k8ssensor-operator.yaml
```

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
