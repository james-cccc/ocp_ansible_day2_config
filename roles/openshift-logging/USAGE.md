OpenShift Cluster Logging
=========================

Installs and configures OpenShift cluster logging by deploying the Elasticsearch and Cluster Logging Operators.

- The Elasticsearch Operator creates and manages the Elasticsearch cluster used by cluster logging.
- The Cluster Logging Operator creates and manages the components of the logging stack.

The cluster logging components are based upon Elasticsearch, Fluentd, and Kibana (EFK).

The collector, Fluentd, is deployed to each node in the OpenShift Container Platform cluster. It collects all node and container logs and writes them to Elasticsearch (ES). Kibana is the centralized, web UI where users and administrators can create rich visualizations and dashboards with the aggregated data.

There are currently 5 different types of cluster logging components:

- **logStore** - This is where the logs will be stored. The current implementation is Elasticsearch.
- **collection** - This is the component that collects logs from the node, formats them, and stores them in the `logStore`. The current implementation is Fluentd.
- **visualization** - This is the UI component used to view logs, graphs, charts, and so forth. The current implementation is Kibana.
- **curation** - This is the component that trims logs by age. The current implementation is Curator.
- **event routing** - This component forwards OpenShift Container Platform events to cluster logging. The current implementation is Event Router.

For more about Cluster Logging, please refer to: [Understanding cluster logging](https://docs.openshift.com/container-platform/4.10/logging/cluster-logging.html)