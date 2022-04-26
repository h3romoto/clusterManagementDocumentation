# Adding a Device to the DHCP Server

*The new device/machine/VM that you are adding to the DHCP server will be referred to as __host__ in this document*

- Find the MAC address of your host.
It should look something like this: `02:46:8a:ce:13:57`

- Open the [VLAN plan](https://docs.google.com/spreadsheets/d/1QndJV4XLq7IcQobumQrSXTRhUPy_f3p9OEDSXcXawHc/edit).
This chart is used to track what we have assigned IP addresses to, as well as other information.
Find the appropriate section and add the new host's name and MAC address to the chart next to your preferred IP

- Log into the DHCP Server VM on thing5

- Open `/etc/dhcp/dhcpd.conf` in your favorite editor

- In the config file, find the section from the VLAN Plan where you added your new host.
For example, the [Bare Metal Servers](https://docs.google.com/spreadsheets/d/1QndJV4XLq7IcQobumQrSXTRhUPy_f3p9OEDSXcXawHc/edit#range=18:18) section in the VLAN plan corresponds to the section following the `# VLAN 32 - Bare Metal Servers (162.210.90.16/28)` comment

- Add a new line with the format `host HOSTNAME { hardware ethernet MAC; fixed-address IP; }`
  - `HOSTNAME` should be the same as the value in the `Host` column in the VLAN Plan
  - `MAC` should be the same as the value in the `MAC Address` column in the VLAN Plan
  - `IP` should be the same as the value in the `IP Address` column in the VLAN Plan

  *For consistency, lines should be in the same order as in the VLAN plan, e.g. the line assigning IP 162.210.90.23 should be between the lines assigning 162.210.90.22 and 162.210.90.24*

- Run `sudo systemctl restart isc-dhcp-server` to apply the changes
