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

**Screenshots:**  
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

