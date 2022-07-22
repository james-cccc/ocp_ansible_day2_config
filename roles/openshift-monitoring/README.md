Ansible Role: OpenShift Monitoring
=======================================

Configures OpenShift cluster monitoring.

For more detailed information, please see: [OpenShift Cluster Monitoring USAGE](./USAGE.md)

## Requirements

The following python3 packages:
- openshift
- kubernetes (included within openshift)

The following Ansible collections:
- kubernetes.core

## Role Variables

Available variables are listed below, along with default values (see `defaults/main.yml`):

You can set the storage class to use here.
```yaml
monitoring_storage_class: localstorage
```

Define here the URL for where you will forward aletermanager alerts.
```yaml
alertmanager_reciever: http://example.com/probe/webhook/prometheus
```

## Dependencies

- **Infra-nodes** - for the monitoring stack components to run on, please refer to: [Infrastructure Nodes in OpenShift 4](https://access.redhat.com/solutions/5034771)
- **StorageClass** - for promethous storage
