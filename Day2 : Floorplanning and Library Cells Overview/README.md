# Day2_Floorplanning_and_Library_Cells_Overview


## Task 1 - Floorplan Execution & Result Analysis

This task involves the physical initialization of the design. When the `run_floorplan` command is executed, the **OpenROAD** tool performs several automated calculations and placements based on the constraints defined in the configuration files.

**1. Core and Die Area Initialization**  
The first action the tool performs is calculating the physical boundaries of the silicon.

- **Core Area:** The space required to house all logic gates.
- **Die Area:** Core plus the margin for I/O pins.
- **Data Observation:** Terminal logs show coordinates like `(0 0) (660685 671405)` in micrometers (scaled by 1000 in the DEF file).

**2. Implementation of Design Constraints**  
The tool applies environment variables to shape the chip:

- **Utilization Factor:** Percentage of core area occupied by gates (e.g., 35%), leaving white space to prevent routing congestion.
- **Aspect Ratio:** Maintained to ensure height-to-width ratio (typically 1.0 for a square design).
- **Core Margin:** Distance between core and die boundary to provide space for I/O pins.

**3. I/O Pin Placement (ioplacer)**  

- Automatically distributes input and output ports along the die boundaries.
- Terminal confirms "IO placement successful."
- Pins are assigned to metal layers (`li1`, `met2`) to avoid blocking routing tracks.

**4. Insertion of Physical-Only Cells**  

- **Tap Cells:** Connect -well and substrate to prevent latch-up.
- **Decoupling Capacitors (Decaps):** Act as local charge reservoirs for supply stability.

**5. Creation of Site Rows**  

- Core divided into horizontal tracks.
- Rows define where standard cells will sit during placement, aligned with power and ground rails.

**6. Final Outputs Generated**  

- `picorv32a.floorplan.def` – DEF file with die, core, and pin coordinates.
- `config.tcl` – Log of variables used during this floorplan execution.

**Screenshots :**  
![Floorplan 1](1_Steps_to_run_floorplan_using_openlane_1.png)  
![Floorplan 2](1_Steps_to_run_floorplan_using_openlane_2.png)  
![Floorplan 3](1_Steps_to_run_floorplan_using_openlane_3.png)  
![Floorplan 4](1_Steps_to_run_floorplan_using_openlane_4.png)  
![Floorplan 5](1_Steps_to_run_floorplan_using_openlane_5.png)  
![Floorplan 6](1_Steps_to_run_floorplan_using_openlane_6.png)  
![Floorplan 7](1_Steps_to_run_floorplan_using_openlane_7.png)  
![Floorplan 8](1_Steps_to_run_floorplan_using_openlane_8.png)  
![Floorplan 9](1_Steps_to_run_floorplan_using_openlane_9.png)  
![Floorplan 10](1_Steps_to_run_floorplan_using_openlane_10.png)  



## Task 2 - Library Cell and Design Configuration Analysis

**1. REVIEW OF DESIGN CONFIGURATION FILES**

Navigation was done into the `designs/picorv32a` directory to inspect the configuration hierarchy.

- **`config.tcl`:** Top-level design variables were verified, including the clock period, utilization factor, and aspect ratio.  
- **`sky130_fd_sc_hd_config.tcl`:** Library-specific overrides defining the standard cell parameters for the **High Density (HD)** Sky130 library were identified.

**2. ANALYSIS OF THE MERGED LEF FILE**

The merged Library Exchange Format (**LEF**) file was confirmed to be generated and located successfully.

- **Path:** Located in the `tmp/` directory of the current run.  
- **Content:** Contains macro-level information of the standard cells, combining the **Technology LEF** (layer information, via rules) and **Cell LEF** (physical dimensions and pin locations). This file serves as the placement reference without requiring transistor-level GDSII data.

**3. INSPECTION OF THE FLOORPLAN DEF RESULTS**

The path to the newly created `picorv32a.floorplan.def` file was observed.

- **Unit Mapping:** The `DISTANCE PIXELS PER MICRON` value (typically 1000) was noted, enabling conversion of DEF coordinates into actual micrometers.  
- **Component Count:** Logs listed the number of defined "Components" (standard cells) and "Pins" in the physical layout.

**4. VERIFICATION OF STANDARD CELL LIBRARY PATHS**

Paths to the **Liberty (.lib)** files were checked.

- **Function:** The flow points to `sky130_fd_sc_hd__tt_025C_1v80.lib` (Typical–Typical corner), which provides timing, power, and electrical characterization for all standard cells used in the design.

**5. IDENTIFICATION OF DEFAULT VS. CUSTOM PARAMETERS**

The distinction between **Default OpenLANE variables** and **Design-specific variables** was identified.

- Parameters defined in `designs/picorv32a/config.tcl` were prioritized over system-wide defaults, ensuring the `picorv32a` design adheres to the intended floorplanning and library constraints.

**Screenshots :**  
![Floorplan Review 1](2_Review_floorplan_files_1.png)  
![Floorplan Review 2](2_Review_floorplan_files_2.png)  
![Floorplan Review 3](2_Review_floorplan_files_3.png)  
![Floorplan Review 4](2_Review_floorplan_files_4.png)  
![Floorplan Review 5](2_Review_floorplan_files_5.png)  
![Floorplan Review 6](2_Review_floorplan_files_6.png)  
![Floorplan Review 7](2_Review_floorplan_files_7.png)  
![Floorplan Review 8](2_Review_floorplan_files_8.png)  
![Floorplan Review 9](2_Review_floorplan_files_9.png)  
![Floorplan Review 10](2_Review_floorplan_files_10.png)  
![Floorplan Review 11](2_Review_floorplan_files_11.png)  

## Task 3 - Reviewing Floorplan Layout in Magic

**1. VISUALIZING THE FLOORPLAN**

The floorplan DEF file was loaded into the **Magic VLSI** tool to perform a visual verification of the physical layout. This step validates that logical constraints such as core area, die area, utilization factor, and aspect ratio were correctly translated into physical geometry on silicon.

**2. LAYOUT NAVIGATION AND CONTROL**

Standard Magic navigation commands were used to inspect the layout efficiently.

- **View Centering:** Pressing `v` aligned the entire die area to the center of the window, providing a complete view of chip boundaries.  
- **Object Selection:** Pressing `s` enabled selection of layout objects or layers.  
- **Precision Zooming:** A bounding box was created using left-click (first corner) and right-click (diagonal corner), followed by pressing `z` to zoom into the selected region for detailed inspection.

**3. COMPONENT INSPECTION AND IDENTIFICATION**

Specific layout objects were selected and queried to verify correctness.

- **Horizontal Object Inspection:** Horizontal elements such as power straps and I/O pins were selected using `s`.  
- **Object Query:** The `what` command was executed in the Magic console (tkcon) to identify object properties, including metal layer assignment (e.g., `met2`) and coordinate information.

**4. ROW AND PIN VERIFICATION**

The core region was inspected to confirm the presence and correctness of key floorplan elements.

- **Standard Cell Rows:** Horizontal placement rows were observed, confirming readiness for standard cell placement.  
- **I/O Pins:** Pins were verified along the die boundary with correct spacing and alignment.  
- **Physical-Only Cells:** Tap cells and decoupling capacitor (Decap) cells inserted during floorplanning were confirmed, ensuring electrical reliability.

**5. VERTICAL COMPONENT IDENTIFICATION**

Vertical layout components were inspected using the same selection and query process.

- Vertical power straps and side I/O pins were selected and analyzed using `s` and `what`.  
- Metal layer assignments (typically `met3`) were verified, confirming a valid multi-layer routing grid for subsequent placement and routing stages.

**Screenshots :**
![Magic Floorplan 1](3_Review_floorplan_layout_in_magic_1.png)
![Magic Floorplan 2](3_Review_floorplan_layout_in_magic_2.png)
![Magic Floorplan 3](3_Review_floorplan_layout_in_magic_3.png)
![Magic Floorplan 4](3_Review_floorplan_layout_in_magic_4.png)
![Magic Floorplan 5](3_Review_floorplan_layout_in_magic_5.png)
![Magic Floorplan 6](3_Review_floorplan_layout_in_magic_6.png)

## Task 4 – Congestion-Aware Placement using RePlAce

This task transitions the design from an empty floorplan to the actual physical placement of logic gates. The **RePlAce** engine within the OpenLANE flow is used to perform congestion-aware global placement followed by legalized detailed placement.

**1. Placement Command Execution**  

The placement stage was initiated using the `run_placement` command.  
This step consumes the synthesized gate-level netlist along with the floorplan DEF and begins mapping standard cells into the predefined site rows generated during floorplanning.

**2. Global Placement using RePlAce**  

The **RePlAce** engine performs the global placement phase.

- Standard cells are treated as a continuous distribution and spread across the core area.  
- Initial positions are computed mathematically to minimize estimated wire length.  
- Placement is performed with congestion awareness to ensure sufficient routing resources remain available for later stages.

**3. Monitoring HPWL and Overflow Metrics**  

During placement optimization, terminal logs provide real-time feedback through key metrics:

- **HPWL (Half-Perimeter Wire Length):** Represents estimated total interconnect length. This value progressively decreases as placement improves.  
- **Overflow:** Indicates cell overlap density. Iterative optimization continues until overflow is reduced to an acceptable level (typically below 0.1), confirming cells are evenly distributed.

**4. Detailed Placement and Legalization**  

After global placement convergence, the flow proceeds to **Detailed Placement** using **OpenDP**.

- All standard cells are snapped precisely onto legal site rows.  
- Power rail alignment and spacing rules are enforced.  
- Overlaps are fully eliminated, ensuring placement legality.  

At the end of this step, every standard cell has a fixed and valid coordinate on the silicon grid.

**5. Placement DEF Generation**  

Successful completion of placement is confirmed by the generation of the placement DEF file:

- **Output File:** `results/placement/picorv32a.placement.def`  
- The file contains a complete list of placed components with exact `(x, y)` coordinates.  
- Compared to the floorplan DEF, this file includes detailed placement information for all standard cells.

**Screenshots :**  
![Placement 1](4_Congestion_aware_placement_using_RePIAce_1.png)  
![Placement 2](4_Congestion_aware_placement_using_RePIAce_2.png)  
![Placement 3](4_Congestion_aware_placement_using_RePIAce_3.png)  
![Placement 4](4_Congestion_aware_placement_using_RePIAce_4.png)  
![Placement 5](4_Congestion_aware_placement_using_RePIAce_5.png)

