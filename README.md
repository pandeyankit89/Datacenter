### DataCenter Architecture
```text
                                   ┌─────────────────────────────────────────────────────┐
                                   │                 DELL POWERMAX STORAGE               │
                                   │  [FA Director A Ports]        [FA Director B Ports] │
                                   └─────────────── ┬───────────────────────┬────────────┘
                                                    │                       │
                          (Fiber Channel/FC Links)  │                       │ (Fiber Channel/FC Links)
                                                    │                       │
                     ┌──────────────────────────────▼───────────────────────▼───────────────────────────────┐
                     │                                 SAN FABRIC (Dual)                                    │
                     │             ┌──────────────────────────┐      ┌──────────────────────────┐           │
                     │             │  SAN SWITCH A (Fabric A) │      │  SAN SWITCH B (Fabric B) │           │
                     │             └──────────────┬───────────┘      └──────────────┬───────────┘           │
                     └────────────────────────────┼─────────────────────────────────┼───────────────────────┘
                                                  │                                 │
                                    (Fiber Channel/FC Uplinks)         (Fiber Channel/FC Uplinks)
                                                  │                                 │
                     ┌────────────────────────────▼───────────────┐   ┌─────────────▼──────────────────────────────┐
                     │        UCS FABRIC INTERCONNECT A           │   │      UCS FABRIC INTERCONNECT B             │
                     │                   (FI-A)                   │   │          (FI-B)                            │
                     ├───────────────┬────────────────────────────┤   ├───────────────┬────────────────────────────┤
                     │   SAN-Fabric  │      LAN-Fabric            │   │   SAN-Fabric  │      LAN-Fabric            │
                     └───────────────┴────────────────────────────┘   └───────────────┴────────────────────────────┘
                               │                 │                            │                 │
                       (FC Downlinks)   (Eth Downlinks)                (FC Downlinks)   (Eth Downlinks)
                               │                 │                            │                 │
       ┌───────────────────────▼─────────────────▼────────────────────────────▼─────────────────▼──────────────────┐
       │                                         CISCO UCS DOMAIN (4 Chassis)                                      │ 
       │                                                                                                           │ 
       │   ┌───────────────────────────────────────────────┐     ┌───────────────────────────────────────────────┐ │ 
       │   │                  CHASSIS-1                    │     │                  CHASSIS-2                    │ │ 
       │   │  +-----------+   +-----------+                │     │  +-----------+   +-----------+                │ │ 
       │   │  | IOM A (FC)|   | IOM B (Eth)|               │     │  | IOM A (FC)|   | IOM B (Eth)|               │ │ 
       │   │  +-----------+   +-----------+                │     │  +-----------+   +-----------+                │ │ 
       │   │  |  PSUs x 4 |   | Fans x 8  |                │     │  |  PSUs x 4 |   | Fans x 8  |                │ │ 
       │   │  +-----------+   +-----------+                │     │  +-----------+   +-----------+                │ │ 
       │   │  |  Blades:  |                                │     │  |  Blades:  |                                │ │ 
       │   │  |  B1 B2 B3 |  (Mezz NICs to FC/Eth fabrics) │     │  |  B1 B2 B3 |  (Mezz NICs to FC/Eth fabrics) │ │ 
       │   │  |  B4 B5 B6 |  (Each blade has ESXi running) │     │  |  B4 B5 B6 |  (Each blade has ESXi running) │ │ 
       │   │  |  B7 B8    |                                │     │  |  B7 B8    |                                │ │ 
       │   └───────────────────────────────────────────────┘     └───────────────────────────────────────────────┘ │ 
       │                                                                                                           │ 
       │   ┌───────────────────────────────────────────────┐     ┌───────────────────────────────────────────────┐ │ 
       │   │                  CHASSIS-3                    │     │                  CHASSIS-4                    │ │ 
       │   │  +-----------+   +-----------+                │     │  +-----------+   +-----------+                │ │ 
       │   │  | IOM A (FC)|   | IOM B (Eth)|               │     │  | IOM A (FC)|   | IOM B (Eth)|               │ │ 
       │   │  +-----------+   +-----------+                │     │  +-----------+   +-----------+                │ │ 
       │   │  |  PSUs x 4 |   | Fans x 8  |                │     │  |  PSUs x 4 |   | Fans x 8  |                │ │ 
       │   │  +-----------+   +-----------+                │     │  +-----------+   +-----------+                │ │ 
       │   │  |  Blades:  |                                │     │  |  Blades:  |                                │ │ 
       │   │  |  B1 B2 B3 |  (Mezz NICs to FC/Eth fabrics) │     │  |  B1 B2 B3 |  (Mezz NICs to FC/Eth fabrics) │ │ 
       │   │  |  B4 B5 B6 |  (Each blade has ESXi running) │     │  |  B4 B5 B6 |  (Each blade has ESXi running) │ │ 
       │   │  |  B7 B8    |                                │     │  |  B7 B8    |                                │ │ 
       │   └───────────────────────────────────────────────┘     └───────────────────────────────────────────────┘ │
       └───────────────────────────────────────────────────────────────────────────────────────────────────────────┘


LEGEND & FLOW:

  • SAN-Fabric: PowerMax ⇄ SAN Switch A/B ⇄ FI-A/B ⇄ Chassis IOM (FC) ⇄ Blade HBAs
  • LAN-Fabric: FI-A/B ⇄ Chassis IOM (Eth) ⇄ Blade NICs ⇄ LAN Switches (not shown here)

NOTES:
  • Each chassis has dual IOMs – one for Fibre Channel (SAN-Fabric) and one for Ethernet (LAN-Fabric).
  • FI provides both SAN uplinks (to SAN fabric) and LAN uplinks (to data/management networks).
  • Blades see both SAN and LAN fabrics for redundancy.
```
