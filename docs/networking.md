# Networking

This section covers network topology recommendations and controller network configuration for Galil EPICS IOC deployments.

## Network Topology

The network between the IOC and the Galil controllers should be a private network. Galil controllers are embedded devices with limited network stacks, and excessive broadcast traffic or contention on shared networks can cause communication dropouts or controller lockups.

Three topologies are described below, in order of preference.

### Separate Private Network

This is the recommended topology, especially for older controllers that only support half-duplex communication (DMC-21x3, MC8000).

The IOC host has two network interface cards (NICs):

- **NIC 1** connects to the facility or beamline network for EPICS Channel Access traffic.
- **NIC 2** connects to a dedicated layer-2/3 switch on a private subnet (e.g. `192.168.94.0/24`). All Galil controllers connect to this switch.

This provides complete isolation between Galil traffic and other network activity. The dedicated switch also provides half-duplex support if needed by older controllers.

### Configured Private Network (VLAN)

If a second switch is not practical, configure a group of ports on the beamline switch as a separate private VLAN. The IOC's second NIC and all Galil controllers connect to ports assigned to this VLAN.

This provides logical isolation without additional hardware, but requires switch management access.

### Open Network

When a private network is not possible, controllers can be placed directly on the shared network. This is the least reliable option and should be combined with the broadcast blocking and port blocking procedures described below to reduce dropout risk.

### Half-Duplex Considerations

Older Galil controllers (DMC-21x3, MC8000) only support half-duplex Ethernet. Some modern managed switches no longer support half-duplex negotiation. If you are using these controllers, the separate private network topology with a switch that supports half-duplex is required.

---

## Controller Network Configuration

These procedures use the Galil Design Kit (GDK) software. Ensure no host firewall is active during configuration.

### Master Reset

Before initial configuration, perform a master reset using one of the following methods:

1. Connect to the controller using GDK, open a terminal window, and issue the master reset command: `^R^S`
2. Use the hardware master reset jumper on the controller board.

### Disable DHCP

Newer Galil controllers ship with DHCP enabled, and a master reset also re-enables DHCP. For IOC use, controllers should have static IP addresses.

1. Connect to the controller using GDK.
2. Issue `DH0` to disable DHCP.
3. Issue `BN` to burn the setting to EEPROM (only if the connection is still active after step 2).

### Assign a Static IP Address

1. Ensure the configuration host has an IP address on the same subnet as the target address for the controller.
2. In GDK, press Ctrl-G to open the manager, then click "Available Addresses."
3. Search for controllers with no IP address.
4. Assign the desired static IP address.

The IP address is burned to controller EEPROM automatically.

### Block Broadcast Traffic

If connection dropouts occur, especially on open or busy networks, configure the controller to block broadcast and ARP traffic:

1. Connect to the controller using GDK and open a terminal window.
2. Issue `TH` and record the controller's MAC address.
3. Issue `IB1` to block all broadcast traffic.
4. Issue `BN` to burn the setting.
5. On the IOC host, add a static ARP entry: `arp -s <ip_address> <mac_address>`

!!! warning
    After blocking broadcasts, only hosts with the static ARP entry can connect to the controller. Add the static ARP entry to the IOC host's startup script so it persists across reboots.

To re-enable broadcasts: connect from a host that has the static ARP entry, issue `IB0`, then `BN`. Alternatively, use the master reset jumper.

### Block Low Ports

If connection dropouts persist, blocking most ports below 1000 may help:

1. Connect to the controller using GDK.
2. Issue `IK1` to block most ports below 1000.
3. Issue `BN` to burn the setting.

To re-enable: issue `IK0`, then `BN`.

---

## Troubleshooting

### Cannot Connect

1. Verify that the host running GDK has an IP address on the same subnet as the controller.
2. Verify that no firewall is between the host and controller.
3. If the controller was previously configured to block broadcasts, add a static ARP entry using the procedure above.
4. As a last resort, use the master reset jumper on the controller.

### Controller Lockup

Symptoms: the controller disappears from the network and the error LED often illuminates. The controller must be power cycled to recover.

To reduce occurrences:

1. Use a separate or configured private network.
2. Block broadcast traffic using the procedure above.
3. Update the controller firmware to the latest available version.

### Connection Dropouts

1. Use a separate or configured private network.
2. Block broadcast traffic.
3. Initialize motor commutation at controller power-on only -- do not re-initialize commutation at EPICS driver start.
4. Update the controller firmware.

---

*The network topology and controller configuration guidance in this section is adapted from the "Galil EPICS Driver Networking Guide for 64-bit Operating Systems" (Rev 1.2, June 2020) by Mark Clift, Australian Synchrotron. Refer to the [original documentation](https://github.com/motorapp/Galil/tree/master/documentation) for the full document including network diagrams.*
