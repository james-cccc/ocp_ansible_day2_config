Ansible Role: OpenShift Cluster Logging
=======================================

Installs and configures OpenShift cluster logging by deploying the Elasticsearch and Cluster Logging Operators.

For more detailed information, please see: [OpenShift Cluster Logging USAGE](./USAGE.md) 

## Requirements

The following python3 packages:
- jmespath
- openshift
- kubernetes (included within openshift)

The following Ansible collections:
- kubernetes.core

## Role Variables

Available variables are listed below, along with default values (see `defaults/main.yml`):

You can set the operator channels here.
```yaml
elasticsearch_channel: stable
clusterlogging_channel: stable
```

If using a custom mirrored operator catalog you can set it here.
```yaml
catalog_source: redhat-operator-index
```

You can set the storage class to use here.
```yaml
logging_storage_class: localstorage
```

## Dependencies

- **Infra-nodes** - for the logging stack components to run on, please refer to: [Infrastructure Nodes in OpenShift 4](https://access.redhat.com/solutions/5034771)
- **StorageClass** - for elasticsearch storage