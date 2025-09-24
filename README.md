### DataCenter Architecture:

![Datacenter](/img/DataCenter.png)

#### _LEGEND & FLOW_
- **SAN-Fabric:** PowerMax ⇄ SAN Switch (Cisco MDS) A/B ⇄ FI-A/B ⇄ Chassis IOM (FC) ⇄ Blade HBAs
- **LAN-Fabric:** FI-A/B ⇄ Chassis IOM (Eth) ⇄ Blade NICs ⇄ LAN Switches (not shown here)

#### _Notes:_
- Each chassis has dual IOMs – one for Fibre Channel (SAN-Fabric) and one for Ethernet (LAN-Fabric).
- FI provides both SAN uplinks (to SAN fabric) and LAN uplinks (to data/management networks).
- Blades see both SAN and LAN fabrics for redundancy.

#### Real Example Flow (if you ask for a 40GB on vm1 and 200GB on vm2 as E: drive)
- (1) **Storage Admin** creates a **500 GB LUN** (raw storage) on **PowerMax**.  
- (2) **SAN Team** performs **zoning**, making that LUN visible to the **ESXi hosts (on UCS)**.  
- (3) **VMware Admin**, using **vSphere Client**, rescans storage → the new LUN is detected → formats it with **VMFS** to create a **VMFS datastore**.  
- (4) **VMware Admin**, in **vSphere Client**, allocates  new 40 GB and 200GB **VMDK** files on the datastore. This **VMDK** is attached to the VM as a **virtual disk**.  
- (5) **Windows Admin** opens **Disk Management** in the VM1 and VM2, sees the **unallocated space**, initializes it, formats it, and assigns it the drive letter **E:**.  
```text
						[Storage Array]
						+-----------------------------+
						| Array: PowerMax / Unity...  |
						| LUN ID: 500                 |
						| Size: 500 GB                |
						+-----------------------------+
									|
									| exported/assigned (via SAN zoning & masking)
									v
						[Fabric / Switch / Zoning]
						+-----------------------------+
						| Fibre / iSCSI fabric        |
						| Zoning: HostWWN -> Port     |
						| Masking: LUN 500 -> Host A  |
						+-----------------------------+
									|
									v
							[ESXi Host (VMware)]
						+-----------------------------+
						| Host: esxi-01.company.local |
						| Sees: /dev/disks/.. (LUN500)|
						+-----------------------------+
									|
									| Rescan Storage -> Format LUN with VMFS
									v
								[VMFS Datastore]
						+-----------------------------+
						| Datastore: DS_LUN500        |
						| VMFS Version: 6 (example)   |
						| Capacity: 500 GB            |
						+-----------------------------+
								|
								| Create VMDK files (thin/thick) [VMware admin often creates the VMDK smaller than the datastore size]
								v
+-----------------------------+    +-----------------------------+
| VMDK: vm1_root.vmdk         |    | VMDK: vm2_data.vmdk         |
| Size: 40 GB (thin)          |    | Size: 200 GB (thick)        |
+-----------------------------+    +-----------------------------+
           |                                 |
           v                                 v
+-----------------------------+    +-----------------------------+
| Virtual Machine: vm1        |    | Virtual Machine: vm2        |
| vCPU: 4  RAM: 8GB           |    | vCPU: 8  RAM: 32GB          |
| Disks: vm1_root.vmdk        |    | Disks: vm2_data.vmdk        |
+-----------------------------+    +-----------------------------+
```
---
