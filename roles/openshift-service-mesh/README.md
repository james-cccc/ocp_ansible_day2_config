Ansible Role: OpenShift Service Mesh
====================================

Installs and configures OpenShift Servce Mesh (OSSM).

For more detailed information, please see: [OpenShift Service Mesh USAGE](./USAGE.md) 

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

## Dependencies

- **Infra-nodes** - for the mesh stack components to run on, please refer to: [Infrastructure Nodes in OpenShift 4](https://access.redhat.com/solutions/5034771)
- **StorageClass** - for storage