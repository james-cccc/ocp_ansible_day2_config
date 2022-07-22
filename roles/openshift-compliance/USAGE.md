OpenShift Compliance Operator
=============================

The compliance-operator is a OpenShift Operator that allows an administrator to run compliance scans and provide remediations for the issues found. The operator leverages OpenSCAP under the hood to perform the scans.

By default, the operator runs in the openshift-compliance namespace, so make sure all namespaced resources like the deployment or the custom resources the operator consumes are created there. However, it is possible for the operator to be deployed in other namespaces as well.

The primary interface towards the Compliance Operator is the ComplianceSuite object, representing a set of scans. The ComplianceSuite can be defined either manually or with the help of ScanSetting and ScanSettingBinding objects. Note that while it is possible to use the lower-level ComplianceScan directly as well, it is not recommended.

For further information please read the official documentation: [Understanding the Compliance Operator](https://docs.openshift.com/container-platform/4.10/security/compliance_operator/compliance-operator-understanding.html)

Before the Operator can be used it first needs to be installed. The installation and basic configuration of this operator has been automated with Ansible.

## Example Configuration & Usage

The following task creates the scan binding resource and will enable scanning with the cis and cis-node profiles on all nodes.

```yaml
- name: Example using the cis and cis-node profiles
  kubernetes.core.k8s:
    state: present
    api_version: compliance.openshift.io/v1alpha1
    kind: ScanSettingBinding
    merge_type:
      - strategic-merge
      - merge
    definition:
      metadata:
        name: cis-compliance
        namespace: openshift-compliance
      profiles:
        - name: ocp4-cis-node
          kind: Profile
          apiGroup: compliance.openshift.io/v1alpha1
      settingsRef:
        name: default
        kind: ScanSetting
        apiGroup: compliance.openshift.io/v1alpha1
```

To list all the available profiles:

    oc get -n openshift-compliance profiles.compliance

To view the compliance scan progress:

    oc get compliancescan -w -n openshift-compliance
    oc get pods -n openshift-compliance 

## Using the Operator

#### Running Scans
When the ScanSettingBinding resource is created this will trigger a scan to start. 

    $ oc get compliancescan -w -n openshift-compliance 
    NAME                   PHASE       RESULT
    ocp4-cis               LAUNCHING   NOT-AVAILABLE
    ocp4-cis-node-master   LAUNCHING   NOT-AVAILABLE
    ocp4-cis-node-worker   LAUNCHING   NOT-AVAILABLE
    ocp4-cis-node-master   LAUNCHING   NOT-AVAILABLE
    ocp4-cis-node-worker   RUNNING     NOT-AVAILABLE
    ocp4-cis               LAUNCHING   NOT-AVAILABLE
    ocp4-cis-node-master   RUNNING     NOT-AVAILABLE
    ocp4-cis               RUNNING     NOT-AVAILABLE
    rhcos4-moderate-master   DONE    NON-COMPLIANT
    rhcos4-moderate-worker   DONE    NON-COMPLIANT

The scans progress through the scanning phases and eventually reach the DONE phase when complete. In most cases, the result of the scan is NON-COMPLIANT. You can review the scan results and start applying remediations to make the cluster compliant. See Managing Compliance Operator remediation for more information.

Example of the pods after a scan:

    $ oc get pods -n openshift-compliance
    NAME                                                       READY   STATUS      RESTARTS   AGE
    aggregator-pod-rhcos4-moderate-master                      0/1     Completed   0          5m40s
    aggregator-pod-rhcos4-moderate-worker                      0/1     Completed   0          5m14s
    compliance-operator-77687c687f-m9zx9                       1/1     Running     0          3d
    ocp4-openshift-compliance-pp-7bc46868bc-62dll              1/1     Running     0          3d
    rhcos4-moderate-master-xxxxxxxxxx-data.dev.co.uk-pod       0/2     Completed   0          16m
    rhcos4-moderate-master-xxxxxxxxxx-data.dev.co.uk-pod       0/2     Completed   0          16m
    rhcos4-moderate-master-xxxxxxxxxx-data.dev.co.uk-pod       0/2     Completed   0          16m
    rhcos4-moderate-worker-xxxxxxxxxx-data.dev.co.uk-pod       0/2     Completed   0          16m
    rhcos4-moderate-worker-xxxxxxxxxx-data.dev.co.uk-pod       0/2     Completed   0          16m
    rhcos4-moderate-worker-xxxxxxxxxx-data.dev.co.uk-pod       0/2     Completed   0          16m
    rhcos4-moderate-worker-xxxxxxxxxx-data.dev.co.uk-pod       0/2     Completed   0          16m
    rhcos4-moderate-worker-xxxxxxxxxx-data.dev.co.uk-pod       0/2     Completed   0          16m
    rhcos4-moderate-worker-xxxxxxxxxx-data.dev.co.uk-pod       0/2     Completed   0          16m
    rhcos4-moderate-worker-xxxxxxxxxx-data.dev.co.uk-pod       0/2     Completed   0          16m
    rhcos4-moderate-worker-xxxxxxxxxx-data.dev.co.uk-pod       0/2     Completed   0          16m
    rhcos4-moderate-worker-xxxxxxxxxx-data.dev.co.uk-pod       0/2     Completed   0          16m
    rhcos4-moderate-worker-xxxxxxxxxx-data.dev.co.uk-pod       0/2     Completed   0          16m
    rhcos4-moderate-worker-xxxxxxxxxx-data.dev.co.uk-pod       0/2     Completed   0          16m
    rhcos4-moderate-worker-xxxxxxxxxx-data.dev.co.uk-pod       0/2     Completed   0          16m
    rhcos4-moderate-worker-xxxxxxxxxx-data.dev.co.uk-pod       0/2     Completed   0          16m
    rhcos4-moderate-worker-xxxxxxxxxx-data.dev.co.uk-pod       0/2     Completed   0          16m
    rhcos4-moderate-worker-xxxxxxxxxx-data.dev.co.uk-pod       0/2     Completed   0          16m
    rhcos4-openshift-compliance-pp-66d979db9f-dn89k            1/1     Running     0          3d

#### View Scan Results
Once the scan is done, to view the results at a high level run the following command (do not be alarmed by the high number of results and failures):

    $ oc get compliancecheckresult -n openshift-compliance
    NAME                                                                                                STATUS   SEVERITY
    rhcos4-moderate-master-accounts-no-uid-except-zero                                                  PASS     high
    rhcos4-moderate-master-audit-rules-dac-modification-chmod                                           FAIL     medium
    rhcos4-moderate-master-audit-rules-dac-modification-chown                                           FAIL     medium
    rhcos4-moderate-master-audit-rules-dac-modification-fchmod                                          FAIL     medium
    rhcos4-moderate-master-audit-rules-dac-modification-fchmodat                                        FAIL     medium
    rhcos4-moderate-worker-disable-ctrlaltdel-burstaction                                               FAIL      high
    rhcos4-moderate-worker-chronyd-client-only                                                          FAIL      low
    rhcos4-moderate-worker-chronyd-no-chronyc-network                                                   FAIL      low
    rhcos4-moderate-worker-chronyd-or-ntpd-set-maxpoll                                                  FAIL      medium
    rhcos4-moderate-worker-chronyd-or-ntpd-specify-multiple-servers                                     PASS      medium
    rhcos4-moderate-worker-chronyd-or-ntpd-specify-remote-server                                        PASS      medium
    rhcos4-moderate-worker-configure-crypto-policy                                                      FAIL      high
    rhcos4-moderate-worker-configure-kerberos-crypto-policy                                             PASS      medium
    rhcos4-moderate-worker-configure-openssl-crypto-policy                                              PASS      medium


You can get a quick summary by piping this command through a few greps and line counts, as shown below

    $  oc get compliancecheckresult -n openshift-compliance | grep PASS | wc -l
    92
    $  oc get compliancecheckresult -n openshift-compliance | grep FAIL | wc -l
    370
    $  oc get compliancecheckresult -n openshift-compliance | grep INFO | wc -l
    2
    $  oc get compliancecheckresult -n openshift-compliance | grep MANUAL | wc -l
    6
    $  oc get compliancecheckresult -n openshift-compliance | grep NOT-APPLICABLE | wc -l
    0

The Compliance Operator generates and stores the raw results in a persistent volume. These results are in Asset Reporting Format (ARF). This procedure to extract scan results is illustrated in the following documentation: [Retrieving Compliance Operator raw results](https://docs.openshift.com/container-platform/4.10/security/compliance_operator/compliance-operator-raw-results.html)

```xml
      <oval-def:definitions>
        <oval-def:definition class="compliance" id="oval:ssg-kerberos_disable_no_keytab:def:1" version="1">
          <oval-def:metadata>
            <oval-def:title>Disable Kerberos by removing host keytab</oval-def:title>
            <oval-def:affected family="unix">
              <oval-def:platform>Red Hat Enterprise Linux CoreOS 4</oval-def:platform>
            </oval-def:affected>
            <oval-def:description>Check that there is no Kerberos keytab file present in /etc</oval-def:description>
            <oval-def:reference ref_id="kerberos_disable_no_keytab" source="ssg"/>
          </oval-def:metadata>
          <oval-def:criteria>
            <oval-def:criterion comment="Restrict Kerberos operation by removing keytab files" test_ref="oval:ssg-test_kerberos_disable_no_keytab:tst:1"/>
          </oval-def:criteria>
        </oval-def:definition>
```
The above process assumes the ScanSettingBinding being set to default and not to auto-apply remediations. 

#### View/Apply Scan Remediations
ScanSettingBinding can either be set to use the default, which only scans. However, you can also set the default-auto-apply option by amending the earlier created resource. 

To apply remediations manually, the boolean attribute spec.apply controls whether the remediation should be applied by the Compliance Operator. You can apply the remediation by setting the attribute to true:

    oc patch complianceremediations/<scan_name>-sysctl-net-ipv4-conf-all-accept-redirects --patch '{"spec":{"apply":true}}' --type=merge

e.g. example taken from the previous compliancecheckresult command result.

    $ oc patch complianceremediations/rhcos4-moderate-master-audit-rules-dac-modification-chmod --patch '{"spec":{"apply":true}}' --type=merge -n openshift-compliance   
    complianceremediation.compliance.openshift.io/rhcos4-moderate-master-audit-rules-dac-modification-chmod patched

Due to the high number of remediations it may be a better idea to run a scan again using the default-auto-apply option as previously stated.

After any remediations have been done be sure to run a new scan (either manually or not) to update the report.

#### Restart Scans
To restart the scans you can use commands such as the below:

    oc annotate compliancescans/ocp4-cis compliance.openshift.io/rescan=
    oc annotate compliancescans/rhcos4-moderate-master compliance.openshift.io/rescan=
    oc annotate compliancescans/rhcos4-moderate-worker compliance.openshift.io/rescan=