Ansible Role: OpenShift File Integrity
======================================

Installs and configures the OpenShift File Integrity Operator.

For more detailed information, please see: [OpenShift File Integrity USAGE](./USAGE.md) 

## Requirements

The following python3 packages:
- jmespath
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
file_integrity_channel: release-0.1
```

## Dependencies

None.