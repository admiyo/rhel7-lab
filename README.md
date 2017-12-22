# RHEL 7 En Masse
## Introduction
This set of roles was written to support the provisioning of RHEL 7 en masse. Using this role it is trivial to provision 1 or 100 virtual machines. We support cleanup and retirement as well. The various roles currently provide integration with the following:

- phpIPAM
- IdM (FreeIPA)
- CloudForms (ManageIQ)
- RHV (oVirt)

## Requirements
Many of the modules we use require Ansible 2.4. We also require dnspython and python-ovirt-engine-sdk4 packages to be installed on the system that is executing the playbooks.

Naturally we are cloning templates as part of the provisioning process. For the roles to work properly (especially the IdM role), your template needs the following minimal configuration:

1. cloud-init and ipa-client packages installed
2. The cloud-init daemon enabled and running
3. The firewalld  daemon should be enabled and running. Also, ssh should be blocked by default. Once the ipa-client-install command has successfully completed, we will re-enable ssh on the firewall using the cloud-cfg script and firewall-cmd. This is done to ensure the system is properly registered to IdM before the wait_for module is called.

## Global Variables

```
cfme_hostname: CloudForms Server with Web Services Role Enabled
cfme_username: Username for CloudForms (needs administrator privileges)
cfme_password: Password for CloudForms
rhv_hostname: RHV-M FQDN (NOT API URL)
rhv_username: Username in RHV-M admin@internal Format (needs administrator privileges)
rhv_password: Password for RHV-M
php_ipam_hostname: phpipam FQDN (NOT API URL)
php_ipam_username: Username for phpipam (needs administrator privileges)
php_ipam_password: Password for phpipam
php_ipam_api_user: API user for token
php_ipam_subnet_id: Subnet ID Used to Request Resources
ipa_username: Username for IdM (needs administrator privileges)
ipa_password: Password for IdM
ipa_hostname: IdM FQDN (NOT API URL)
```

## Example Playbook
```
---
- hosts: localhost
  name: Provision VM(s)
  connection: local
  gather_facts: false

  pre_tasks:
    - name: Convert host_csv to Array
      set_fact:
        hostnames: "{{ host_csv.split(',') }}"

    - debug: var=hostnames
      verbosity: 2

    - name: Get Host Array Length
      set_fact:
        number_vms: "{{ hostnames | length }}"

    - debug: var=number_vms
      verbosity: 2

    - name: Fail if number_vms > 5
      fail:
        msg: "Number of VMs too large: {{ number_vms }}"
      when: number_vms | int > 5

  roles:
    - role: phpipam
    - role: dynamic_hostgroup
    - role: idm
    - role: rhv
    - { role: manageiq, when: manageiq is defined }

# Example for use with dynamic_inventory (probably more suitable as another role)
- hosts: dynamic_inventory
  name: Configure VM(s)

  tasks:
    - name: Ensure SELinux is enabled/enforcing
      selinux:
        policy: targeted
        state: enforcing
```
