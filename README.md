OpenShift Cluster Configuration
===============================

This repository contains the following:

- Ansible playbooks per cluster, each with its own configuration file
- Ansible roles for the OpenShift cluster configuration
  - Documentation:
    - Ansible role overview
    - Usage overview

This is very much a WIP and was put together for the sole purpose of illustrating how Ansible ran against OCP/K8s could be clearly structured.

# Contributing

Please note that specific branches should exist for each major OpenShift release e.g. 4.9, 4.10.

The majority of this codebase uses the Ansible k8s modules, Jeff Geerling writes an excellent book on this topic: [Ansible for Kubernetes](https://www.ansibleforkubernetes.com/)

# Ansible

## Requirements

To use this Ansible codebase to peform your OpenShift cluster configuration you’ll need the following:
- Ansible 2.9.17 or latest installed

```bash
dnf install ansible -y
ansible --version
```

The following python3 packages:
- jmespath
- openshift
- kubernetes (included within openshift)

```bash
pip3 install jmespath
pip3 install openshift
```

The following Ansible collections:
- kubernetes.core

```bash
ansible-galaxy collection install kubernetes.core
```

## Role Variables

The role variables are all set in a single file inside the vars directory - each cluster will have its own vars file. e.g.

```bash
ansible/vars/
├── dev.yml
└── vault_dev.yml
```

In addition to the variables files, sensitive variables are stored in Ansible Vault files.

For more information on Vault please see: [Encrypting content with Ansible Vault](https://docs.ansible.com/ansible/latest/user_guide/vault.html#encrypting-content-with-ansible-vault)

To encrypt a file to be a vault:

```bash
 ansible-vault encrypt vars/vault_dev.yml
```

To then ammend that file:

```bash
 ansible-vault edit vars/vault_dev.yml
```

## Playbook Usage and Configuration

To execute the automation run the playbook of the relevant cluster. The playbook points to the relevant kubeconfig and variables file(s).

If using a vault password file:
```bash
ansible-playbook playbooks/<cluster_config_file>.yaml --vault-password-file .vault_pass.txt
```

If you want a prommpt for the vault password:
```bash
ansible-playbook playbooks/<cluster_config_file>.yaml --ask-vault-pass
```

## Roles

There are multiple roles contained within this project with their documentation being available within the role structure. Clicking the links below will take you to the relevant docs for the role.

- [openshift-logging](roles/openshift-logging/README.md) - Deploys the Red Hat openshift logging stack
- [openshift-monitoring](roles/openshift-monitoring/README.md) - Configures the Red Hat openshift monitoring stack
- [openshift-file-integrity](roles/openshift-file-integrity/README.md) - Deploys and configures the Red Hat File Integrity (AIDE) operator
- [openshift-compliance](roles/openshift-compliance/README.md) - Deploys and configures the Red Hat Compliance (OpenSCAP) operator
- [openshift-etcd](roles/openshift-etcd/README.md) - Configures etcd
- [openshift-service-mesh](roles/openshift-service-mesh/README.md) - Deploys OSSM
