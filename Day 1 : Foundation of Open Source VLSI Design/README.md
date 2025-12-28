# Day 1 – Foundation of Open Source VLSI Design

## Task 1: Directory Structure and Details

My objective for this session was to understand the organization of the **SkyWater 130nm PDK** and how **OpenLANE** interfaces with it to perform an automated RTL-to-GDSII ASIC design flow.

**Sky130 PDK Organization**

- **libs.ref (Reference Libraries):** Timing (`.lib`), abstract layout (`.lef`), and geometric layout (`.gds`, `.mag`) for cells.  
- **libs.tech (Tool-Specific Files):** Configuration for EDA tools like Magic, Netgen, and KLayout.

**Standard Cell Library: `sky130_fd_sc_hd`**

- **sky130** – 130nm process node  
- **fd** – Foundry-provided  
- **sc** – Standard cells  
- **hd** – High density, optimized for small area  

**Key File Types**

| File Type | Purpose |
|----------|--------|
| `.lib` | Timing and power characterization for synthesis and STA |
| `.lef` | Abstract physical view for placement and routing |
| `.gds` | Final mask-level layout for fabrication |
| `.mag` | Editable layout for inspection in Magic |

**OpenLANE-PDK Integration**
- I explored how OpenLANE maps RTL outputs to standard cells  
- I verified that timing, placement, routing, and verification use process-consistent data  

I successfully initialized the OpenLANE environment with the **Sky130A PDK**. I explored the `sky130_fd_sc_hd` library and confirmed that it contains all the necessary timing, physical, and layout views, giving me a solid foundation to proceed with synthesis and physical design.

**Screenshots :**
![Directory structure 1](1_Directory_structure_and_%20details_1.png)  
![Directory structure 2](1_Directory_structure_and_%20details_2.png)  
![Directory structure 3](1_Directory_structure_and_%20details_3.png)  
![Directory structure 4](1_Directory_structure_and_%20details_4.png)



