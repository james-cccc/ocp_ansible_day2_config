Ansible Role: OpenShift Compliance
==================================

Installs and configures the OpenShift Compliance Operator.

For more detailed information, please see: [OpenShift Compliance USAGE](./USAGE.md) 

## Requirements

The following python3 packages:
- openshift
- kubernetes (included within openshift)

The following Ansible collections:
- kubernetes.core

## Role Variables

Available variables are listed below, along with default values (see `defaults/main.yml`):

If using a custom mirrored operator catalog you can set it here.
```yaml
catalog_source: redhat-operator-index
```

You can set the operator channel here.
```yaml
compliance_channel: release-0.1
```

You can set the storage class to use here.
```yaml
compliance_storage_class: ocs-storagecluster-cephfs
```

Define how often the default scan runs    
```yaml
compliance_scan_schedule: 0 1 * * *
```

## Dependencies

- **StorageClass** - for storing the reports