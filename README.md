### DataCenter Architecture:

![Datacenter](/img/DataCenter.png)

#### _LEGEND & FLOW_
- **SAN-Fabric:** PowerMax ⇄ SAN Switch (Cisco MDS) A/B ⇄ FI-A/B ⇄ Chassis IOM (FC) ⇄ Blade HBAs
- **LAN-Fabric:** FI-A/B ⇄ Chassis IOM (Eth) ⇄ Blade NICs ⇄ LAN Switches (not shown here)

#### _Notes:_
- Each chassis has dual IOMs – one for Fibre Channel (SAN-Fabric) and one for Ethernet (LAN-Fabric).
- FI provides both SAN uplinks (to SAN fabric) and LAN uplinks (to data/management networks).
- Blades see both SAN and LAN fabrics for redundancy.

#### Real Example Flow (if you ask for a 500 GB E: drive)
- (1) **Storage Admin** creates a **500 GB LUN** (raw storage) on **PowerMax**.  
- (2) **SAN Team** performs **zoning**, making that LUN visible to the **ESXi hosts (on UCS)**.  
- (3) **VMware Admin**, using **vSphere Client**, rescans storage → the new LUN is detected → formats it with **VMFS** to create a **VMFS datastore**.  
- (4) **VMware Admin**, in **vSphere Client**, allocates a new **500 GB VMDK** file on the datastore. This **VMDK** is attached to the VM as a **virtual disk**.  
- (5) **Windows Admin** opens **Disk Management** in the VM, sees the **unallocated space**, initializes it, formats it, and assigns it the drive letter **E:**.  

---
