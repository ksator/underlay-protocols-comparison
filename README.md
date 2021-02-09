```
git clone https://github.com/aristanetworks/ansible-avd.git
git clone https://github.com/aristanetworks/ansible-cvp.git
git clone https://github.com/ksator/eos_lab.git
cd eos_lab
```

To get the list of lab:
```
ls inventories
```

To print the devices version and model:
```
ansible-playbook playbooks/print_version_and_model.yml -i inventories/{{ lab }}/inventory.yml
```

To load a lab configuration on devices:
```
ls inventories/{{ lab }}/intended/configs
ansible-playbook playbooks/load_intended_config.yml -i inventories/{{ lab }}/inventory.yml
```

To collect "show commands" from devices:
```
ls inventories/{{ lab }}/group_vars/DC1_FABRIC.yml
ansible-playbook playbooks/snapshot.yml -i inventories/{{ lab }}/inventory.yml
ls inventories/{{ lab }}/snapshots
```

If you would like to update the intended configuration, collect the running configuration (using the snapshot.yml playbook) and use the update_intended_config.yml playbook to copy the collected running configuration from the snapshots directory to the intended directory:
```
ansible-playbook playbooks/snapshot.yml -i inventories/{{ lab }}/inventory.yml
more inventories/{{ lab }}/snapshots/{{ inventory_hostname }}/show running-config.txt
ansible-playbook playbooks/update_intended_config.yml -i inventories/{{ lab }}/inventory.yml
ls inventories/{{ lab }}/intended/configs
```

[ebgp](inventories/ebgp):
- 2 spines
- 4 L3 leaves:
 - MLAG to connect servers on L3 Leaves
- 2 L2 leaves:
  - L2 leaves without MLAG
  - connected to L3 Leaves (MLAG on L3 Leaves and LAG on L2 leaves)
 - distributed routing on L3 Leaves
 - EBGP underlay
 - EBGP overlay
 - symmetric IRB
 - VRFs
 - Lo0 for BGP peering (overlay)
 - Lo1 for VTEP source address
 - VLAN aware bundle
