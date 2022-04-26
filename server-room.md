# Server Room

> Much of the information in this doc was found using `lshw`

- [Servers](#servers)
- [Switches](#switches)
- [Pis](#pis)
- [Uninterruptible Power Supplies (UPS)](#uninterruptible-power-supplies)
- [Power Distribution Units (PDU)](#power-distribution-units)

## Layout

```
     Rack 1         Rack 2             Rack 3
+--------------+ +-----------+ +---------------------+
| weird switch | | (see pis) | | Fiber boxes (201)   |
| switch1      | |           | | Fiber boxes (202-3) |
|              | |           | | switch0             |
| gpu          | |           | |                     |
| atlas        | | monitors  | |                     |
| thing7       | |           | |                     |
| thing6       | | switch2   | |                     |
| thing5       | |           | |                     |
| thing4       | | thing2    | | thing3              |
| phi1    ?    | | thing1    | |                     |
| phi0    ?    | | Large UPS | | Small UPS           |
+--------------+ +-----------+ +---------------------+

            (pis)
+-------------+-----------------+
| pitracker   : pdc-cluster-1-1 |
| pirunner    : pdc-cluster-1-2 |
| ckubed-pi-1 : pdc-cluster-1-3 |
| ckubed-pi-2 : pdc-cluster-1-4 |
|             : pdc-cluster-1-5 |
|             : pdc-cluster-2-1 |
|             : pdc-cluster-2-2 |
|             : pdc-cluster-2-3 |
|             : pdc-cluster-2-4 |
|             : pdc-cluster-2-5 |
+-------------+-----------------+
```


## Servers

### atlas

- **Motherboard**: Supermicro X10DRH-iT
- **CPU 0**: Intel Xeon E5-2620 v4 with 8 cores (16 threads) @ 2.10GHz (turbo 3.00GHz)
- **CPU 1**: Intel Xeon E5-2620 v4 with 8 cores (16 threads) @ 2.10GHz (turbo 3.00GHz)
- **RAM**: Samsung M393A4K40BB0-CPB DDR4 Registered ECC 2133MHz 32GiB (x8 = 256GiB)
- **Networking 0**: Intel 82599ES 10-Gigabit SFI/SFP+ Network Connection
- **Networking 1**: Intel 82599ES 10-Gigabit SFI/SFP+ Network Connection
- **Disk 0**: Samsung SSD 850 1024GB
- **Disk 1**: Samsung SSD 850 1024GB
- *RAID drives are not visible in lshw*
- **PSU 0**: Supermicro PWS-1K28P-SQ
- **PSU 1**: Supermicro PWS-1K28P-SQ


### gpu

- **Motherboard**: Supermicro X10DRG-OT+-CPU
- **CPU 0**: Intel Xeon E5-2650 v3 with 10 cores (20 threads) @ 2.30GHz (turbo 3.00GHz)
- **CPU 1**: Intel Xeon E5-2650 v3 with 10 cores (20 threads) @ 2.30GHz (turbo 3.00GHz)
- **RAM**: Micron 36ASF2G72PZ-2G1A2 DDR4 Registered ECC 2133MHz 16GiB (x16 = 256GiB)
- **Networking**: Intel 82599ES 10-Gigabit SFI/SFP+ Network Connection
- **Disk 0**: Samsung SSD 850 1TB
- **Disk 1**: Samsung SSD 850 1TB
- **GPU 0**: Nvidia GM200 (GeForce GTX 980 Ti)
- **GPU 1**: Nvidia GM200 (GeForce GTX 980 Ti)
- **GPU 2**: Nvidia GM200 (GeForce GTX 980 Ti)
- **GPU 3**: Nvidia GM200 (GeForce GTX 980 Ti)
- **GPU 4**: Nvidia GM200 (GeForce GTX 980 Ti)
- **GPU 5**: Nvidia GM200 (GeForce GTX 980 Ti)
- **GPU 6**: Nvidia GM200 (GeForce GTX 980 Ti)
- **PSU 0**: Supermicro PWS-1K66P-1R
- **PSU 1**: Supermicro PWS-1K66P-1R
- **PSU 2**: Supermicro PWS-1K66P-1R
- **PSU 3**: Supermicro PWS-1K66P-1R


### phi0


### phi1


### thing1


### thing2


### thing3

- **Motherboard**: Supermicro H8QGL
- **CPU 0**: AMD Opteron 6276 with 16 cores at 2.3GHz
- **CPU 1**: AMD Opteron 6276 with 16 cores at 2.3GHz
- **CPU 2**: AMD Opteron 6276 with 16 cores at 2.3GHz
- **CPU 3**: AMD Opteron 6276 with 16 cores at 2.3GHz
- **RAM**: Kingston 9965516-051.A00LF DDR3 ECC 1600MHz 8GiB (x16 = 128GiB)
- **Networking**: Intel 82599ES 10-Gigabit SFI/SFP+ Network Connection
- **Disk 0**: Seagate ST31500541AS 1.5TB
- **Disk 1**: ATAPI iHAS124 C DVD-RAM writer
- **Disk 2**: Seagate ST2000DM001-1CH1 2TB
- **PSU 0**: ?
- **PSU 1**: ?


### thing4

- **Motherboard**: Supermicro H8QG6
- **CPU 0**: AMD Opteron 6376 with 16 cores @ 2.3GHz
- **CPU 1**: AMD Opteron 6376 with 16 cores @ 2.3GHz
- **CPU 2**: AMD Opteron 6376 with 16 cores @ 2.3GHz
- **CPU 3**: AMD Opteron 6376 with 16 cores @ 2.3GHz
- **RAM**: Kingston 9965516-477.A00G DDR3 Registered ECC 1600MHz 16GiB (x16 = 256GiB) (*Odd banks report empty*)
- **Networking**: Intel 82599ES 10-Gigabit SFI/SFP+ Network Connection
- **Disk 0**: Samsung SSD 850 1TB
- **Disk 1**: Samsung SSD 850 1TB
- **Disk 2**: Hitachi HDS72101 1TB
- **PSU 0**: ?
- **PSU 1**: ?


### thing5

- **Motherboard**: Supermicro H8QG6
- **CPU 0**: AMD Opteron 6376 with 16 cores @ 2.3GHz
- **CPU 1**: AMD Opteron 6376 with 16 cores @ 2.3GHz
- **CPU 2**: AMD Opteron 6376 with 16 cores @ 2.3GHz
- **CPU 3**: AMD Opteron 6376 with 16 cores @ 2.3GHz
- **RAM**: Kingston 9965516-477.A00G DDR3 Registered ECC 1600MHz 16GiB (x16 = 256GiB) (*Odd banks report empty*)
- **Networking 0**: Intel 82599ES 10-Gigabit SFI/SFP+ Network Connection
- **Networking 1**: Intel 82599ES 10-Gigabit SFI/SFP+ Network Connection
- **Disk 0**: Samsung SSD 850 1TB
- **Disk 1**: Hitachi HUA72302 2TB
- **Disk 2**: HGST HDN724040AL 4TB
- **Disk 3**: HGST HDN724040AL 4TB
- **Disk 4**: Samsung SSD 850 1TB
- **PSU 0**: ?
- **PSU 1**: ?


### thing6

- **Motherboard**: Supermicro H11DSi-NT
- **CPU 0**: AMD EPYC 7821 with 16 cores (32 threads) @ 2.1GHz (boost 2.7GHz)
- **CPU 1**: AMD EPYC 7821 with 16 cores (32 threads) @ 2.1GHz (boost 2.7GHz)
- **RAM**: Crucial CT32G4RFD4266.36FD1 DDR4 Registered ECC 2667MHz 32GiB (x1 = 32GiB) (*All but bank 12 report as empty*)
- **Networking**: Intel 82599ES 10-Gigabit SFI/SFP+ Network Connection
- **Disk 0**: Samsung SSD 860 256GB
- **Disk 1**: Samsung SSD 860 256GB
- **PSU 0**: Supermicro PWS-1K28P-SQ
- **PSU 1**: Supermicro PWS-1K28P-SQ


### thing7

- **Motherboard**: Supermicro H11DSi-NT
- **CPU 0**: AMD EPYC 7821 with 16 cores (32 threads) @ 2.1GHz (boost 2.7GHz)
- **CPU 1**: AMD EPYC 7821 with 16 cores (32 threads) @ 2.1GHz (boost 2.7GHz)
- **RAM**: Crucial CT32G4RFD4266.36FD1 DDR4 Registered ECC 2667MHz 32GiB (x15 = 480GiB) (*Bank 0 reports as empty*)
- **Networking**: Intel 82599ES 10-Gigabit SFI/SFP+ Network Connection
- **Disk 0**: Seagate ST2000DM001 4142MB ?
- **Disk 1**: Seagate ST31500541AS 1.5TB
- **Disk 2**: Seagate ST2000DM001-1CH1 2TB
- **Disk 3**: Samsung SSD 860 256GB
- **Disk 4**: Samsung SSD 860 256GB
- **PSU 0**: Supermicro PWS-1K28P-SQ
- **PSU 1**: Supermicro PWS-1K28P-SQ


## Switches

### switch0

- **Model**: Cisco Nexus 9372PX


### switch1

- **Model**: Cisco Nexus 9372PX


### switch2

- **Model**: HP Procurve 2824


## Pis

### pitracker

- Raspberry Pi 3B


### pirunner

- Raspberry Pi 4B 1GB


### pdc-cluster-#-#

- Raspberry Pi 4B 2GB


## Uninterruptable Power Supplies

### Small UPS

- **Model**: APC SMT2200RM2U
- **Voltage**: 120V
- **Input**: 5-20P
- **Outputs**: 6 5-15R; 2 5-20R


### Large UPS

- **Model**: APC SRT10KXLT
- **Voltage**: 208V
- **Input**: Terminal block
- **Output 1**: 2 L6-20R
- **Output 2**: 2 L6-20R
- **Output 3**: 2 L6-30R


## Power Distribution Units

### Rack 1 Back PDUs

- **Model**: APC AP9567

### Rack 1 Right Front PDU

- **Model**: KMC 12


# Future Upgrades

To utilize the 208V UPS, we first need it connected to the building's electrical supply by an electrician.

To connect machines to the 208V UPS, we need to switch PDUs to the [APC AP7540](https://www.apc.com/shop/us/en/products/Rack-PDU-Basic-Zero-U-20A-208V-20-C13-4-C19/P-AP7540) and the [APC AP7541](https://www.apc.com/shop/us/en/products/Rack-PDU-Basic-Zero-U-30A-200-208V-20-C13-4-C19/P-AP7541), which support 20A and 30A, respectively.
These PDUs have [C13](https://www.cablestogo.com/learning/connector-guides/power#c13-c14) outputs, which is the official name of the connector used to power most servers.
C14 is the official name of the receptacle for the C13, so we will need C13 to C14 cables to connect the servers to the PDU.

When this change is made, **the switches should be left connected to the 120V UPS**.
They do not support 208V power.
**Pis should also be connected to the 120V UPS**.

**When performing this upgrade, double and triple check your work.**
**Be sure that the PDUs of the machines being moved to the 208V circuit will support the higher voltage.**
**Be sure that machines are plugged into the correct places before powering on the circuits.**
