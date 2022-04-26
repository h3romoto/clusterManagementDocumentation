# Networking Guide

Tip: Knowledge about networking, switches, VLANs would be useful before working on these things.

Pro Tip: Bidit’s CURI Logs from Summer 2017 has a lot of information (ask RAB for access). So do the CISCO Switch Manuals

## Switch IPs

### Cisco Switches

> `Switch0` - `162.210.90.1`  
> `Switch1` - `162.210.90.2`

Switch username is `admin` **(not bw-admin)**.  

The password is on the bw-admin file 

Also anywhere there is a `?` replace it with `/` because it doesn’t like the shift key


### HP Switch

> `Switch2` - `162.210.90.14`

This switch (currently) cannot be SSH'd to directly, so SSHing into Switch1 will allow you to then SSH into Switch2.
Switch password is the same as the Cisco switches but without the prefix and suffix, due to password length restrictions.

## Commands (TAB for autocompletion):

### View Current Config

```
show running-config
```

(Use enter to scroll)

### Edit interface configuration


```bash
configure terminal      # Terminal prompt should be switch(config)# after this
interface ethernet NUM  # Terminal prompt should be switch(config-if)# after this
# Enter lines one by one as seen in running-config
exit                    # Terminal prompt should return to switch(config)# after this
exit                    # Terminal prompt should return to switch# after this
copy running-config startup-config
```
```bash
switch# show running-config (Enter to scroll)
switch# configure terminal - to change things
switch(config)# interface ethernet (#number) - to configure that interface
switch(config-if)# <enter lines one by one as seen in running-config>
switch(config-if)# exit
switch(config)# default interface (#number) - to reset config for that interface
switch(config)# exit
switch# copy running-config startup-config
```

## Bridge a Machine to VLANs

Make sure machine is trunked to all VLANs on the switch
Now on the machine (suppose we are adding bridge X):

```
$ brctl addbr brX
```

Modify `/etc/network/interfaces` (add the following lines):

```
auto <interface_name>.X
iface <interface_name>.X inet manual

auto brX
iface br96 inet manual
	bridge_ports <interface_name>.X
	bridge_stp off
```

Find the proper interface (the one that is connected to the switch and gets internet) using: 

```
$ ip ad
or
$ ifconfig
```

You may have to put the interface in manual mode, then add it (bridge it) to the appropriate bridge.
It should be on and then configure the bridge to get dhcp.
