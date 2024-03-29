## About this repository

This repository provides examples of EOS configuration files for an EVPN-VXLAN fabric using various underlay options:
- [EBGP](inventories/ebgp)
  - Leaves <-> spines interfaces are configured with an IPv4 address
- [RFC5549](inventories/rfc5549)
  - No IPv4 address configured on leaves <-> spines interfaces
  - IPv6 enabled on leaves <-> spines interfaces
    - IPv6 link-local address is automatically configured
    - The IPv6 link-local address of the remote end is discovered via IPv6’s Neighbor Discovery Router Advertisement protocol
  - EBGP peers address are not configured
    - On leaves, BGP uses the discovered IPv6 link-local address of the remote end (spines)
    - On spines, BGP is configured with listen range
  - IPv4 NLRI advertisement with an IPv6 Next Hop
  - Routing of an IPv4 packet over a IPv6 network
- [OSPF](inventories/ospf)
  - Single area
  - Lo0 and Lo1 are passive interfaces
  - Leaves <-> spines interfaces are configured with an IP address
- [OSPF_unnumbered](inventories/ospf_unnumbered)
  - Single area
  - Lo0 and Lo1 are passive interfaces
  - Leaves <-> spines interfaces are using Lo0 interface IP address
- [ISIS_adv_passive_only](inventories/ISIS_adv_passive_only)
  - Level 1
  - Lo0 and Lo1 are passive interfaces
  - ISIS advertises only passive interfaces (Lo0 and Lo1)
  - Leaves <-> spines interfaces are configured with an IP address
- [ISIS_unnumbered](inventories/ISIS_unnumbered)
  - Level 1
  - Lo0 and Lo1 are passive interfaces
  - Leaves <-> spines interfaces are using Lo0 interface IP address

This repository also provides various `show commands` output for each scenario.

## Topology details

- 2 spines
  - DC1-SPINE1 and DC1-SPINE2
- 4 L3 leaves
  - MLAG to connect servers on L3 Leaves
    - DC1-LEAF1A and DC1-LEAF1B
    - DC1-LEAF2A and DC1-LEAF2B
  - Distributed routing on L3 Leaves
- EBGP overlay
  - Symmetric IRB
  - Lo0 for BGP peering (overlay)
  - Lo1 for VTEP source address
  - VLAN aware bundle
  - VRFs

![lab.png](lab.png)

## Repository usage

Clone the repository:
```
git clone https://github.com/aristanetworks/ansible-avd.git
git clone https://github.com/ksator/underlay-protocols-comparison.git
cd underlay-protocols-comparison
```

To get the list of [lab](inventories):
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

To collect `show commands` from devices:
```
ls inventories/{{ lab }}/group_vars/DC1_FABRIC.yml
ansible-playbook playbooks/snapshot.yml -i inventories/{{ lab }}/inventory.yml
```
```
ls inventories/{{ lab }}/snapshots
```

If you would like to update the intended configuration from the devices running configuration:
- Collect the running configuration (using the [snapshot.yml](playbooks/snapshot.yml) playbook)
```
ansible-playbook playbooks/snapshot.yml -i inventories/{{ lab }}/inventory.yml
more inventories/{{ lab }}/snapshots/{{ inventory_hostname }}/show running-config.txt
```
- Then use the [update_intended_config.yml](playbooks/update_intended_config.yml) playbook to copy the collected running configuration from the `snapshots` directory to the `intended` directory
```
ansible-playbook playbooks/update_intended_config.yml -i inventories/{{ lab }}/inventory.yml
```
```
ls inventories/{{ lab }}/intended/configs
```
