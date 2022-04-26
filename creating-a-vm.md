# Creating a VM

## Getting Started

### Requirements

- Need `sudo` on the host machine
- When using `ssh`, need to run an X-server 
  - Look for “how to enable x forwarding”
  - On Windows, [MobaXterm](https://mobaxterm.mobatek.net/) is a good option
- Need Ubuntu downloaded on host machine before creating the VM	
  - Recommended: 18.04
  - Server install image


## Creating the VM

- `ssh` into the host machine
- `wget UBUNTU_LINK` (if not already downloaded)
- `sudo virt-manager`
  - If running an x server the window will pop up

- Click the "Create a new virtual machine" button in the top left
  - Use Local install media
    - Use an ISO image and browse to find the Ubuntu image you downloaded
    - Specify what OS
  - Select amount of RAM… in MebiBytes (10^2)
    - Can be changed later
  - Create a disk image
  - Hard Disk
    - Put it on Datavmpool or vmpool
    - For Ldap we named ldap, with 100 GB on vmpool
    - Give it less rather than more. Easier to grow then to shrink
  - Fill in Name - ldap
    - Can also be changed later
  - Network Selection*** - make sure it is done properly
    - On thing5 we use a fixed IP - and uses bridges to connect
    - St olaf Ips = 162.210.90.(0-255)
    - Log it into the VLAN Plan cluster manager doc
      - Find empty IP address to assign your vm to
      - VLAN ID is the bridge that you want
      - Ldap goes into Core Infrastructure
  - DHCP server needs to be told what IP to give the vm
    - Find MAC address in NIC
    - Put the Mac address in VLAN Plan
    - Find DHCP server
    - Log into DHCP Server bw-admin
    - Edit /etc/dhcp/dhcpd.conf
    - Edit the Conf to list the new vm (look at other entries to get format)
      - host name ( )
  - Restart the dhcp server
    - sudo systemctl restart isc-dhcp server
  - IDE CDROM - need to go there to reconnect the ISO you downloaded after turning off the machine
    - Under Boot Options Enable IDE CDROM 1
  - Start machine
    - Should be setting up ubuntu
    - Server name: the same as on the VLAN Plan sheet
    - Pick a username: bw-admin
    - Click space to mean yes, choose yes to Install OpenSHH Server
    - Reboot once you finish as it should give you the option to
  - Go Back to change the Boot Options to uncheck IDE CDROM 1 so that it boots from the hard disk instead
  - Reboot and log on
  - Check if there is an ip ifconfig
  - Check if there is internet ping 8.8.8.8
  - sudo apt update
  - sudo apt upgrade
