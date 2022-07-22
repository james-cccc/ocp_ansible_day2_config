OpenShift File Integrity Operator
=================================

The file-integrity-operator is a OpenShift Operator that continually runs file integrity checks on the cluster nodes. It deploys a DaemonSet that initializes and runs privileged AIDE (Advanced Intrusion Detection Environment) containers on each node, providing a log of files that have been modified since the initial run of the DaemonSet pods.

For further information please read the official documentation: [Understanding the File Integrity Operator](https://docs.openshift.com/container-platform/4.10/security/file_integrity_operator/file-integrity-operator-understanding.html)

Before the Operator can be used it first needs to be installed. The installation and basic configuration of this operator has been automated with Ansible.

## Example Configuration & Usage

The following task creates the FileIntegrity resource and will enable scanning on all nodes.
```yaml
- name: Example scanning being enabled on all nodes
  community.kubernetes.k8s:
    state: present
    api_version: fileintegrity.openshift.io/v1alpha1
    kind: FileIntegrity
    merge_type:
      - strategic-merge
      - merge
    definition:
      metadata:
        name: nodes-fileintegrity
        namespace: openshift-file-integrity
      spec:
        config:
          gracePeriod: 3600
```

The following commands will list any FileIntegrity resources created. Right now the automation creates a single resource which enables scanning on all nodes, however if reuired you could create seperare resources for each type of node e.g. worker and master nodes.
```
oc get fileintegrity
oc get fileintegrities -n openshift-file-integrity
```

Grace period by default is set to 900, it can be changed to any value required e.g. the value 3600 will run an AIDE check every hour.
```yaml
spec:
  config:
    gracePeriod: 300
```

If you wish to debug this operator you can enable the debug flag.
```yaml
spec:
  config:
    gracePeriod: 3600
    debug: true
```

Additional configuration such as scheduleing the scan instances, supplying a custom AIDE configuration file, and specify tolerations to schedule on nodes with custom taints can be done and is documented in the below links:

- [Configuring the Custom File Integrity Operator](https://docs.openshift.com/container-platform/4.10/security/file_integrity_operator/file-integrity-operator-configuring.html)
- [Project repository](https://github.com/openshift/file-integrity-operator)

Once two AIDE scans have been completed you will be able to see reports for each node by running the following command:
```
oc get fileintegritynodestatus
```
The results field can contain up to three entries. The most recent Successful scan, the most recent Failed scan (if any), and the most recent Errored scan (if any). When there are multiple entries, the newest lastProbeTime indicates the current status.

A Failed scan indicates that there were changes to the files that AIDE monitors, and displays a brief status. The resultConfigMap fields point to a ConfigMap containing a more detailed report.

if you wish to reinitialize the database the below command can wil do this:
```
oc annotate fileintegrities/example-fileintegrity file-integrity.openshift.io/re-init=
```
