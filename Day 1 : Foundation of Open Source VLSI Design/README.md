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



## Task 2 - Design Preparation Deep Dive

**1. Initializing the OpenLANE Workspace**  
I first set up the virtualized environment by entering the containerized shell, giving me access to the specialized EDA toolchain (Yosys, OpenROAD, Magic) and the SkyWater 130nm PDK. I then started an interactive Tcl session, which allowed me to run the physical design flow step by step and observe the results of each command.

**2. Exploring the Design Architecture**  
I performed a design audit by navigating through the `designs/picorv32a` directory.  
- I confirmed the presence of the RTL (Verilog) and Timing Constraints (SDC) files, which define the chip’s functionality and timing.  
- I examined the `.tcl` configuration files, noting that the picorv32a design had specific overrides for the clock period and target density, ensuring optimal synthesis.

**3. Merging Technology and Cell Data (LEF Merging)**  
I executed the preparation step, during which OpenLANE merged the Technology LEF (defining metal layers, via rules, and fabrication constraints) with the Cell LEFs (defining standard cell dimensions and pin locations).  
This produced a merged LEF file, which I know will serve as the foundation for placement and routing to ensure the chip is manufacturable.

**4. Workspace Management and Versioning**  
I observed that OpenLANE automatically created a timestamped directory under `runs/`.  
- This gave me a clean sandbox for my current session.  
- I could now organize all reports, netlists, and layout files in one place.  
- I realized that this setup allows me to compare different runs if I adjust constraints in future iterations.

**5. Final Parameter Verification**  
I verified that all 6 metal layers (li1 through met5) were recognized and confirmed that the `sky130_fd_sc_hd` library was correctly linked. This confirmed that my environment was ready for Synthesis.

**Screenshot:**  
![Design preparation 1](2_Design_preparation_step_1.png)  
![Design preparation 2](2_Design_preparation_step_2.png)  
![Design preparation 3](2_Design_preparation_step_3.png)  
![Design preparation 4](2_Design_preparation_step_4.png)  
![Design preparation 5](2_Design_preparation_step_5.png)  
![Design preparation 6](2_Design_preparation_step_6.png)  
![Design preparation 7](2_Design_preparation_step_7.png)  
![Design preparation 8](2_Design_preparation_step_8.png)  
![Design preparation 9](2_Design_preparation_step_9.png)  
![Design preparation 10](2_Design_preparation_step_10.png)


