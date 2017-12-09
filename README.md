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

## Global Variables & Vaults
```
em_hostnames: []
em_ip_addresses: []

debug: True
```

## Example Playbook
```
TODO
```
