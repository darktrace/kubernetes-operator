## 7.1.6

**Release date: 25-06-2026**

- Bug fixes


## 7.1.5

**Release date: 23-06-2026**

### Added

- Suspend/resume reconciliation for all agent components
- Node agent liveness and readiness probes with reduced startup time
- Improved metrics authentication for K8sSensor Server
- Kubernetes recommended labels on managed resources
- Support for manual agent component updates

### Fixed

- CVE-2026-24051
- Status update race conditions
- Deadlock on operator shutdown
- Audit agent service selector
- Infinite reconcile when preserving deployment/daemonset annotations
- Event watch permissions

## 7.1.4

**Release date: 15-04-2026**

- Bug fixes

## 7.1.3

**Release date: 09-04-2026**

- Template DtK8sSensor release artifact

## 7.1.2

**Release date: 07-04-2026**

- Refer to secrets in Custom Resources by Secret Ref
- Prometheus server deployed for monitoring DtK8sSensorServer
- Improved error recovery
- Simplified configuration for selecting sub-master in a unified view setup

## 7.1.1

**Release date: 13-03-2026**

🚀 Initial release!
