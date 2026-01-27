# Migration-tool OVS to OVN

Before anything else, it is necessary to create a backup of the OpenStack environment to prevent any issues that may occur. This migration tool was designed based on existing tools, but adapted specifically for Kolla OpenStack. It may still require some adjustments; however, at the time of writing, all performed tests have shown that it is functional.

The Ansible playbook workflow is as follows:

#Step 1:
The playbook starts by stopping the systemd services related to Neutron OVS, which will no longer be used after the migration. After that, it stops the Neutron service itself and cleans the Open vSwitch flows. OVS is not removed during the migration; it remains in place, but control is transferred to OVN. At this stage, the br-tun bridge is cleaned because it is not used by OVN, and the OVS flows are removed to avoid conflicts with the new rules that will be applied by OVN.

#Step 2:
The Neutron plugin is updated in the globals.yml file, and OVN is deployed. Required configurations are added to the Neutron configuration files, and Neutron is reconfigured to adapt to the OVN-related settings.

#Step 3:
Finally, a small change is made to the database, specifically in the providerresourceassociations table. This table contains legacy configuration related to OVS, but it needs to be updated to reflect the OVN configuration. Although the system may continue to work with the OVS configuration, if the machine or the Neutron service is restarted, the network will break. This happens because Neutron attempts to register the correct provider (OVN), but the table is already populated. With this adjustment, the migration is completed.
