network
========

This roles enables users to configure various network components  in thier servers. The role can be used to configure

-  Ethernet Interfaces

-  Bridge Interfaces

-  Bonding interfaces

- Routes in the machine.


Requirements
------------

This role requires ansible 1.4 or higher and platform requirements are listed in the metadata file

Role Variables
--------------

The variables that can be passed to this role and a brief description about them are as follows:

```
network_ether_interfaces: []                   #The list of ethernet interfaces to be added to the sytem
network_bridge_interfaces: []                  #The list of bridge interfaces to be added to the sytem
network_bond_interfaces: []                    #The list of bond interfaces to be added to the sytem
```

Note: The values for the list are listed in the example's below.

- Examples

1) Configure eth1 and eth2 of a host with static ip and a dhcp ip also static routes defined and a gateway.

```

- hosts: myhost
  roles:
    - role: network
      network_ether_interfaces:
       - device: eth1
         bootproto: static
         address: 192.168.10.18
         netmask: 255.255.255.0
         gateway: 192.168.10.1
         route:
          - network: 192.168.200.0
            netmask: 255.255.255.0
            gateway: 192.168.10.1
          - network: 192.168.100.0
            netmask: 255.255.255.0
            gateway: 192.168.10.1
       - device: eth2
         bootproto: dhcp

```

2) Configure a bridge interface with multiple nics added to the bridge.

```

- hosts: myhost
  roles:
    - role: network
      network_bridge_interfaces:
       -  device: br1
          type: bridge
          address: 192.168.10.10
          netmask: 255.255.255.0
          bootproto: static
          stp: "on"
          ports: [eth1, eth2]

```

Note: Route's can also be added for this interface in the same way route's are added for interface.


3) Configure a bond interface with an Active-Backup slave configuration.

```

- hosts: myhost
  roles:
    - role: network
      network_bond_interfaces:
        - device: bond0
          address: 192.168.10.128
          netmask: 255.255.255.0
          bootproto: static
          bond_mode: active-backup
          bond_miimon: 100
          bond_slaves: [eth1, eth2]
          route:
          - network: 192.168.222.0
            netmask: 255.255.255.0
            gateway: 192.168.10.1

```


4) Configure a bonding interface with 802.3ad as the bonding mode and address is got via dhcp

```
- hosts: myhost
  roles:
    - role: network
      network_bond_interfaces:
        - device: bond0
          bootproto: dhcp
          bond_mode: 802.3ad
          bond_miimon: 100
          bond_slaves: [eth1, eth2]

```

1) All the above examples show how to configure a single host, The below example shows how to define your network configurations
for all your machines.

- Assuming your Inventory looks as follows:

```
/etc/ansible/hosts

[dc1]
host1
host2

```

- Describe your network configuration for each host in hostvars

```
host_vars/host1
--------------


network_ether_interfaces:
       - device: eth1
         bootproto: static
         address: 192.168.10.18
         netmask: 255.255.255.0
         gateway: 192.168.10.1
         route:
          - network: 192.168.200.0
            netmask: 255.255.255.0
            gateway: 192.168.10.1
network_bond_interfaces:
        - device: bond0
          bootproto: dhcp
          bond_mode: 802.3ad
          bond_miimon: 100
          bond_slaves: [eth2, eth3]


host_vars/host2
---------------

network_ether_interfaces:
       - device: eth0
         bootproto: static
         address: 192.168.10.18
         netmask: 255.255.255.0
         gateway: 192.168.10.1


```

- Create a playbook which applis these role to all hosts as shown below and run the playbook and all the servers shoudl have thier
network iterfaces configured and routed updated.

```
- hosts: all
  roles:
    - role: network

```

Dependencies
------------

None

License
-------

BSD

Author Information
------------------

Benno Joy

