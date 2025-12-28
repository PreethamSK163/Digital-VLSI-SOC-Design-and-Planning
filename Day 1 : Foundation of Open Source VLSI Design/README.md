## Day 1 – Foundation of Open Source VLSI Design

**Summary of Day 1:**

- **Objective:** Familiarize with the SkyWater 130nm PDK (`sky130A`) and OpenLANE RTL-to-GDSII flow, preparing the environment for ASIC design.
- **PDK Exploration:**  
  - Studied `libs.ref` for timing (`.lib`), abstract layout (`.lef`), and geometric layout (`.gds`, `.mag`).  
  - Studied `libs.tech` for tool-specific configurations (Magic, Netgen, KLayout).  
  - Explored the `sky130_fd_sc_hd` standard cell library and understood its hierarchy and file types.
- **OpenLANE Environment Setup:**  
  - Entered Docker container to access the pre-configured EDA toolchain (Yosys, OpenROAD, Magic, OpenSTA).  
  - Started an interactive Tcl session for step-by-step RTL-to-GDSII flow.  
  - Prepared the `picorv32a` design using the `prep` command, merging Technology LEF and Cell LEFs and creating timestamped runs directories.
- **Design Architecture Audit:**  
  - Verified the presence of RTL (Verilog) and Timing Constraint (SDC) files.  
  - Examined configuration `.tcl` files and confirmed overrides for clock period and target density.
- **Logic Synthesis & Functional Mapping:**  
  - Converted RTL to Gate-Level Netlist using Yosys.  
  - Performed technology mapping, logic minimization, and optimization with ABC.  
  - Verified cell usage, total gate count, and chip area.  
  - Calculated the Flop Ratio to understand sequential vs. combinational logic density.
- **Characterizing Synthesis Results:**  
  - Verified Gate-Level Netlist and synthesis statistics (cell types, chip area).  
  - Checked preliminary timing using slack and critical path analysis.  
  - Ensured all cells used respected library constraints and fanout limits.
- **Environment Verification:**  
  - Confirmed all 6 metal layers (li1 to met5) were recognized.  
  - Confirmed `sky130_fd_sc_hd` library was correctly linked.  
  - Verified workspace organization under `runs/` for logs, reports, netlists, and layouts.
- **Outcome:**  
  - Achieved a full understanding of the PDK and OpenLANE workflow.  
  - Prepared the `picorv32a` design for synthesis, timing analysis, and physical design.  
  - Established a solid foundation for Day 2 tasks like Floorplanning and Placement.


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

**Screenshots :**  
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


## Task 3 - Logic Synthesis & Functional Mapping

**1. Translation from RTL to Gate-Level Logic**  
I started by converting my behavioral Verilog code for the `picorv32a` processor into structural hardware using **Yosys**.  
- I observed how the tool broke down complex if-else statements and arithmetic operations into basic Boolean expressions.  
- These expressions were then mapped to actual gates available in the **Sky130 High Density (HD)** standard cell library, ensuring that the design is physically realizable.

**2. Technology Mapping & Optimization**  
After logic analysis, the **ABC** tool performed technology mapping and optimization.  
- I watched it select appropriate gate sizes to meet the design's speed and area requirements.  
- Redundant gates were removed through **logic minimization** to save silicon area and power.  
- Every selected gate came from the `sky130_fd_sc_hd` library, ensuring manufacturability in the SkyWater 130nm process.

**3. Quantitative Analysis (Synthesis Statistics)**  
I examined the **Statistics Report** generated at the end of synthesis:  
- **Cell Types:** Identified the standard cells used (AND, OR, NAND, Flip-Flops, etc.).  
- **Chip Complexity:** Checked the number of cells to understand the physical scale of the processor.  
- **Chip Area:** Estimated the total silicon area needed for the design, which will guide Floorplanning.

**4. Calculating the Flop Ratio**  
I manually calculated the **Flop Ratio** to measure sequential density:  
- Counted the number of **D-Flip-Flops** and divided it by the **total cell count**.  
- **Insight:** A higher flop ratio indicates a more pipelined architecture (faster but potentially more power-hungry), while a lower ratio indicates more combinational logic. This helps me anticipate challenges in the **Clock Tree Synthesis (CTS)** stage.

**5. Verification of the Netlist**  
I verified that a new **Gate-Level Netlist (.v)** was generated in the `runs` directory.  
- This netlist is a detailed list of gates and connections, ready for the next steps in physical design.  

**Screenshots :**  
![Synthesis 1](3_Logic_Synthesis_and_%20Functional_%20Mapping_2.png)  
![Synthesis 2](3_Logic_Synthesis_and_%20Functional_%20Mapping_3.png)  
![Synthesis 3](3_Logic_Synthesis_and_%20Functional_%20Mapping_4.png)  
![Synthesis 4](3_Logic_Synthesis_and_%20Functional_%20Mapping_5.png)  
![Synthesis 5](3_Logic_Synthesis_and_%20Functional_%20Mapping_6.png)  
![Synthesis 6](3_Logic_Synthesis_and_%20Functional_%20Mapping_7.png)  
![Synthesis 7](3_Logic_Synthesis_and_%20Functional_%20Mapping_8.png)  
![Synthesis 8](3_Logic_Synthesis_and_%20Functional_%20Mapping_9.png)  
![Synthesis 9](3_Logic_Synthesis_and_%20Functional_%20Mapping_1.png)

## Task 4 - Characterizing & Analyzing Synthesis Results

**1. Verification of the Gate-Level Netlist (GLN)**  
I first verified that the **Gate-Level Netlist** was successfully generated.  
- I navigated to the `results/synthesis/` directory to locate the output file (usually `picorv32a.synthesis.v`).  
- This netlist is the structural version of my design, where high-level Verilog has been replaced by specific standard cells from the **Sky130_fd_sc_hd** library.

**2. Analysis of the Synthesis Statistics Report**  
I examined the `.stat` report and terminal logs to audit the design quantitatively.  
- **Cell Mapping Audit:** Checked which gates were used most (e.g., NAND2, NOR2, Inverters).  
- **Area Calculation:** Noted the total "Chip Area," which sets the minimum size of the chip for Floorplanning.

**3. Calculating the Flop Ratio (Sequential vs. Combinational)**  
I calculated the **Flop Ratio** to understand logic density:  
- Counted the number of **Flip-Flops** (e.g., `sky130_fd_sc_hd__dfxtp_2`) and divided by the **Total Number of Cells**.  
- **Insight:** A ratio around **10-15%** for `picorv32a` indicates a balanced architecture and helps anticipate Clock Tree Synthesis complexity.

**4. Timing Characterization (Pre-Layout STA)**  
I performed a preliminary timing check using synthesis timing reports:  
- **Setup Slack Analysis:** Verified that slack values were positive, confirming the design can run at the target clock frequency (e.g., 200MHz).  
- **Critical Path Identification:** Identified the longest path in the logic, allowing me to optimize RTL or synthesis strategy before physical design.

**5. Cell Library Characterization Mapping**  
I ensured the synthesis respected the target library:  
- Verified that no "don't use" cells were employed.  
- Checked gate driving strengths to confirm they meet fanout constraints from `config.tcl`.

**Screenshots :**  
![Synthesis Analysis 1](4_Characterizing_and_Analyzing_Synthesis_Results_1.png)  
![Synthesis Analysis 2](4_Characterizing_and_Analyzing_Synthesis_Results_2.png)  
![Synthesis Analysis 3](4_Characterizing_and_Analyzing_Synthesis_Results_3.png)  
![Synthesis Analysis 4](4_Characterizing_and_Analyzing_Synthesis_Results_4.png)  
![Synthesis Analysis 5](4_Characterizing_and_Analyzing_Synthesis_Results_5.png)  
![Synthesis Analysis 6](4_Characterizing_and_Analyzing_Synthesis_Results_6.png)  
![Synthesis Analysis 7](4_Characterizing_and_Analyzing_Synthesis_Results_7.png)  
![Synthesis Analysis 8](4_Characterizing_and_Analyzing_Synthesis_Results_8.png)  
![Synthesis Analysis 9](4_Characterizing_and_Analyzing_Synthesis_Results_9.png)

