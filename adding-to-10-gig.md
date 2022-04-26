# Connecting a Machine to the 10 Gigabit Network

## Things You Need

### For Most Devices

- Fiber Optic Cables: Two kinds
  - Blue end - Blue end: For connecting machines to the switch
  - Blue end - Green end: For connecting machines to boxes under the floor or switch to box over the switch
- SFPs - Blue cable ends go into SFPs. SFPs go into machines and switches
- 10-Gigabit Network Cards

Make sure whichever machine you are connecting has a 10-Gig compatible Ethernet Card inside where you will plug in the SFP.

### For Devices that Don't Support 10-Gig Network Cards

If the device cannot support adding a 10-Gig network card (like a Raspberry Pi), you can use a 1-Gigabit Ethernet SFP instead.
Note that the port on the other end must support gigabit ethernet (100 megabit fast ethernet won't work).

- Gigabit Ethernet SFP
- Ethernet Cable

As of Fall 2020, we have set up a separate ethernet switch for devices such as Pis, allowing us to use only one ethernet SFP.
New devices that need ethernet should be added to this switch.

## Connections

### Connecting a Machine Directly to the Switch

- Insert SFP into switch (Note port number)
- Insert SFP into machine
- Take off the small white caps (2) from the fiber optic cable ends (try to not look into the ends)
- Insert one end into switch SFP
- Insert other end into machine SFP

### Connecting a Machine Directly to the Switch with a 1 Gigabit Ethernet SFP

> Remember that the port on the other end must support gigabit ethernet.
> This means that some devices such as a Raspberry Pi 3B must connect with a USB gigabit ethernet dongle instead of using the onboard ethernet port.

- Insert ethernet SFP into switch (Note port number)
- Connect ethernet cable to SFP and device
- Configure the switch and then the device should be able to connect

### Connecting a Machine Through a Box Under the Floor

- Insert SFP into machine
- Take off the small white caps (2) from the fiber optic cable ends (try to not look into the ends)
- Insert blue end into SFP
- Insert other (green) end into port in the box under the floor (note orientation and number)
- Take another SFP and cable and repeat steps 1 to 3 but insert it into the switch (note port number)
- Take other (green) end and insert with opposite orientation into the same number port in the box above switch0

## Configuration

Now we need to configure the switch and device.

### Switch Config

- SSH into the switch where you configured things and do as described in the [Networking Guide](networking.md) manual to configure the switch

### Device Config

Note that this step is for devices using a 10-Gig fiber optic SFP.
Devices using an ethernet connection should just work (hopefully).

- On the machine, `cd` into `/etc/modprobe.d` so we can tell Ubuntu to accept the new SFP
- Create a file called `ixgbe.conf` and put the following line into it:
  - `options ixgbe allow_unsupported_sfp=1`
- Then, run `rmmod ixgbe; modprobe ixgbe`
- To make it persist through reboots, edit `/etc/default/grub`
  - What used to say `GRUB_CMDLINE_LINUX=""` should now say `GRUB_CMDLINE_LINUX="ixgbe.allow_unsupported_sfp=1"`
  - Then, run `grub-mkconfig -o /boot/grub/grub.cfg`
- Run `ifconfig <interfacename> down; ifconfig <interfacename> up` and it should be working (hopefully)
