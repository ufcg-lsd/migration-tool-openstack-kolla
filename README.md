# Migration Tool – OVS to OVN (Kolla OpenStack)

## Overview

This repository provides an **Ansible-based migration tool** to migrate an OpenStack environment from **Neutron Open vSwitch (OVS)** to **Neutron OVN**, specifically designed for **Kolla / Kolla-Ansible deployments**.

The migration process is based on existing community approaches, with adaptations to better fit the Kolla architecture. While the tool has been tested in several scenarios, it may still require adjustments depending on the target environment.

>  **Important:** Before running this migration, it is **strongly recommended** to create a **full backup** of your OpenStack environment.

---

## Scope and Assumptions

- OpenStack deployed using **Kolla / Kolla-Ansible**
- Migration path: **Neutron OVS → Neutron OVN**
- Open vSwitch is **not removed** during the migration
- Networking control is transferred from OVS to OVN

---

## Migration Workflow

The migration is executed through an **Ansible playbook** and is divided into three main steps.

---

### Step 1 – Stop OVS Services and Clean OVS State

The playbook performs the following actions:

- Stops systemd services related to **Neutron OVS**
- Stops the **Neutron service**
- Removes existing **Open vSwitch flows**
- Cleans the **`br-tun` bridge**

#### Why this step is required

- `br-tun` is **not used by OVN**
- Existing OVS flows may conflict with OVN rules
- Ensures a clean transition to OVN

> ℹ️ **Note:** OVS remains installed after this step, but it is no longer managed by Neutron.

---

### Step 2 – Switch Neutron Plugin and Deploy OVN

This step updates the OpenStack configuration to use OVN:

- Updates the Neutron plugin in `globals.yml`
- Deploys **OVN services**
- Adds required **OVN configuration options** to Neutron configuration files
- Reconfigures Neutron to apply OVN settings

After this step, Neutron is fully configured to operate with OVN.

---

### Step 3 – Database Adjustment

A small but critical database change is performed:

- Updates the `providerresourceassociations` table
- Removes legacy **OVS-related entries**
- Ensures the provider information reflects **OVN**

#### Why this step is necessary

Without this adjustment, the system may appear functional but will fail after:

- Neutron service restarts
- Host reboots

Neutron will attempt to register OVN as the provider, but the table will already be populated with OVS data, causing network failures.

Once this step is completed, the migration is considered **finalized**.

---

## Important Notes

-  **Always back up your environment before running the migration**
-  Tested in limited environments; additional tuning may be required
-  **Octavia compatibility**

>  **This migration has NOT been tested with Octavia (Load Balancer as a Service).**  
> Environments using Octavia may encounter unexpected behavior.  
> Thorough testing in a staging environment is strongly recommended.

---

## Migration Status

- [x] Neutron OVS to OVN migration
- [x] Kolla-specific workflow
- [ ] Tested with Octavia

---

## Disclaimer

This tool modifies core networking components and database entries in OpenStack.  
Use it at your own risk and **never run directly in production without prior validation**.

---

## Contributing

Contributions, bug reports, and improvements are welcome.  
Please open an issue or submit a pull request.


