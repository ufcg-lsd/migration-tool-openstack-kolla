# Migration-tool OVS to OVN

Before performing the migration from the OVS plugin to OVN in the Kolla OpenStack environment, it is highly recommended to back up the current state of Kolla to ensure that the system can be restored in case of unexpected issues. The migration process has been simplified through Ansible automation: to execute the migration, simply run the command:

```bash
ansible-playbook -i "all-in-one" init_migration.yml 
```
or 
```bash
ansible-playbook -i "multinode" init_migration.yml
```
depending on your deployment type, and allow the playbook to complete.

This tool was developed recently and, while it has proven functional in all conducted tests, further improvements may be needed in future versions. Users are encouraged to report any issues or suggest enhancements to help improve its reliability and feature set.
