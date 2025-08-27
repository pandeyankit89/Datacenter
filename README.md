### DataCenter Architecture:

![Datacenter](/img/DataCenter.png)

#### _LEGEND & FLOW_
- **SAN-Fabric:** PowerMax ⇄ SAN Switch A/B ⇄ FI-A/B ⇄ Chassis IOM (FC) ⇄ Blade HBAs
- **LAN-Fabric:** FI-A/B ⇄ Chassis IOM (Eth) ⇄ Blade NICs ⇄ LAN Switches (not shown here)

#### _Notes:_
- Each chassis has dual IOMs – one for Fibre Channel (SAN-Fabric) and one for Ethernet (LAN-Fabric).
- FI provides both SAN uplinks (to SAN fabric) and LAN uplinks (to data/management networks).
- Blades see both SAN and LAN fabrics for redundancy.
---
