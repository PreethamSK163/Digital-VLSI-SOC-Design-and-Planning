# Day 4 : Pre-Placement Timing Verification and Clock Tree Optimization


## Task 1 – Converting Grid Info to Track Info

In this task, the custom inverter layout was prepared for the routing stage by aligning it with the technology grid and track requirements. Proper alignment ensures that the router can successfully connect standard cells without violating design rules.

**Accessing the Track Configuration File**

The process began by examining the `tracks.info` file from the Sky130 PDK, which defines routing behavior for each metal layer. Specific coordinates for layers such as `li1`, `met1`, and `met2` were identified. Key data points including the starting position of the first track (Offset) and the distance between consecutive tracks (Pitch) were noted for both horizontal and vertical directions.

**Understanding the Routing Grid**

Using the track information, the layout's routing grid was analyzed. The standard cell's width was verified to be an integer multiple of the track pitch. Routing tracks act as paths for metal wires, and misaligned pins would prevent proper automated routing.

**Aligning the Layout Grid**

The custom inverter layout was aligned to match the routing tracks. After adjustment, the grid visually represented the actual routing tracks of the Sky130 process, with horizontal and vertical lines intersecting at precise intervals.

**Verification of Pin Alignment**

The input and output pins of the inverter were carefully checked to ensure their centers were located at the intersections of horizontal and vertical tracks. This alignment guarantees that the global router can successfully connect the pins during the automated routing stage.

**Ensuring Symmetry and Boundary Alignment**

The boundaries of the standard cell were verified to ensure that the height and width are multiples of the vertical and horizontal track pitch, respectively. Additionally, the power (VDD) and ground (GND) rails were confirmed to be centered on the tracks, which is essential for proper automated Power Distribution Network (PDN) generation.

---

**Screenshots :**

![Grid to Track 1](1_Convert_grid_info_to_track_info_1.png)  
![Grid to Track 2](1_Convert_grid_info_to_track_info_2.png)  
![Grid to Track 3](1_Convert_grid_info_to_track_info_3.png)  
![Grid to Track 4](1_Convert_grid_info_to_track_info_4.png)  
![Grid to Track 5](1_Convert_grid_info_to_track_info_5.png)  
![Grid to Track 6](1_Convert_grid_info_to_track_info_6.png)  
![Grid to Track 7](1_Convert_grid_info_to_track_info_7.png)  
![Grid to Track 8](1_Convert_grid_info_to_track_info_8.png)


## Task 2 – Converting Magic Layout to Standard Cell (LEF)

In this task, the manual Magic layout of the custom inverter was converted into a standardized format (LEF) that the OpenLANE flow can recognize as a usable macro or "Standard Cell."


**Naming and Port Definition**

The first step was to associate all physical geometries with logical ports. Each port was assigned a class (input, output, or feedthrough) and a use (signal, power, or ground). The cell was given a unique name, `sky130_vsdinv`, to distinguish it from default library cells.

**Setting the Cell Boundary (PR Boundary)**

The "Place and Route" (PR) boundary of the cell was defined. This bounding box informs the placement tool of the space occupied by the cell. The boundary edges were aligned with the routing tracks from Task 1 to prevent overlaps or gaps when cells are placed side-by-side.

**Executing the LEF Extraction**

The Magic layout was converted into a Library Exchange Format (LEF) file. The terminal confirmed generation of `sky130_vsdinv.lef`. This LEF file contains abstract information such as the cell's size, pin locations (A and Y), and layer data, without including internal transistor details.

**Inspecting the Generated LEF File**

The LEF file was opened in a text editor to verify content:

- **MACRO Definition:** `MACRO sky130_vsdinv` block was present.  
- **PIN Information:** The `PIN A` and `PIN Y` sections matched the grid intersections set in Task 1.  
- **OBS (Obstruction):** Areas reserved for internal metals were correctly defined to prevent routing conflicts.

**Moving the LEF to the Design Directory**

The generated LEF file was moved to the `designs/picorv32a/src/` folder to integrate with the project. Configuration files (`config.tcl`) or environment variables were updated to include this LEF file in the library search path.


**Screenshots**

![LEF Conversion 1](2_Convert_magic_layout_to_std_cell_1.png)  
![LEF Conversion 2](2_Convert_magic_layout_to_std_cell_2.png)  
![LEF Conversion 3](2_Convert_magic_layout_to_std_cell_3.png)  
![LEF Conversion 4](2_Convert_magic_layout_to_std_cell_4.png)  
![LEF Conversion 5](2_Convert_magic_layout_to_std_cell_5.png)  
![LEF Conversion 6](2_Convert_magic_layout_to_std_cell_6.png)  
![LEF Conversion 7](2_Convert_magic_layout_to_std_cell_7.png)


## Task 3 – Introduction to Timing Libraries (.lib)

In this task, you bridge the gap between physical layout and timing analysis. By understanding the timing libraries, you ensure that OpenLANE knows exactly how much delay your custom inverter adds to the `picorv32a` design.

**Identifying the Timing Library Corner**

You navigated the directory structure to locate the Liberty (.lib) files. The primary reference was `sky130_fd_sc_hd__tt_025C_1v80.lib`. The environmental parameters were verified: **TT** (Typical-Typical process), **25°C** (Ambient Temperature), and **1.80V** (Operating Voltage). This ensures timing calculations match real silicon conditions.

**Anatomy of a Cell Definition**

Inside the `.lib` file, you inspected the logical description of standard cells:

- **Cell Area:** Confirmed the `area` attribute matches the LEF dimensions.  
- **Leakage Power:** Noted the power dissipation values for the cell when idle.

**The Lookup Table (LUT) Model**

The LUT is the "brain" of timing analysis. You analyzed 2D matrices that calculate delays based on:

- **Input Transition vs Output Load:** Gate delay depends on the input signal transition speed and the output load capacitance.  
- **Table Search:** Verified `rise_transition` and `fall_transition` tables, which correlate with the delays measured in **ngspice**.

**Pin Characteristics & Capacitance**

You checked the pin definitions to verify electrical load:

- **Directionality:** Pins are marked as `input` or `output`.  
- **Capacitance:** Input pin capacitances are used to calculate the load seen by preceding gates.

**Integration in OpenLANE**

To finalize this task, the following steps were executed:

- **Read Library:** Loaded the `.lib` files into the current OpenLANE session.  
- **Link Design:** Ensured the synthesized `picorv32a` netlist used these library definitions.  
- **Verification:** Confirmed that timing models for all gates, including the custom inverter, were loaded and ready for Static Timing Analysis (STA).

**Screenshots :**

![Timing Libs 1](3_Introduction_to_timing_libs_1.png)  
![Timing Libs 2](3_Introduction_to_timing_libs_2.png)  
![Timing Libs 3](3_Introduction_to_timing_libs_3.png)  
![Timing Libs 4](3_Introduction_to_timing_libs_4.png)  
![Timing Libs 5](3_Introduction_to_timing_libs_5.png)  
![Timing Libs 6](3_Introduction_to_timing_libs_6.png)  
![Timing Libs 7](3_Introduction_to_timing_libs_7.png)  
![Timing Libs 8](3_Introduction_to_timing_libs_8.png)  
![Timing Libs 9](3_Introduction_to_timing_libs_9.png)  
![Timing Libs 10](3_Introduction_to_timing_libs_10.png)  
![Timing Libs 11](3_Introduction_to_timing_libs_11.png)  
![Timing Libs 12](3_Introduction_to_timing_libs_12.png)  
![Timing Libs 13](3_Introduction_to_timing_libs_13.png)  
![Timing Libs 14](3_Introduction_to_timing_libs_14.png)  
![Timing Libs 15](3_Introduction_to_timing_libs_15.png)  
![Timing Libs 16](3_Introduction_to_timing_libs_16.png)  
![Timing Libs 17](3_Introduction_to_timing_libs_17.png)  
![Timing Libs 18](3_Introduction_to_timing_libs_18.png)

## Task 4 – Configuring Synthesis to Fix Slack

In this task, you focused on resolving timing violations identified during the initial synthesis. By adjusting the OpenLANE configuration variables, you guided the tool to prioritize speed over area to eliminate "Negative Slack."

**Identifying the Slack Violation**

You began by reviewing the initial Static Timing Analysis (STA) report in the terminal:

- **Negative Slack:** Observed a "Worst Negative Slack" (WNS) value, indicating that the data path was slower than the required clock period.  
- **Violation Path:** The report highlighted specific gates in the `picorv32a` netlist where the "Data Arrival Time" exceeded the "Required Time."

**Modifying Synthesis Strategy**

To fix the timing, the following OpenLANE environment variables were updated:

- **SYNTH_STRATEGY:** Adjusted to focus on reducing delay even if total chip area increases.  
- **SYNTH_BUFFERING:** Enabled to allow buffer insertion to strengthen weak signals.  
- **SYNTH_SIZING:** Enabled gate sizing, replacing small, slow gates with larger, higher-drive versions.

**Re-running the Synthesis**

After updating the configurations, synthesis was executed again:

- **Observation:** Terminal logs showed iterative logic restructuring, swapping cells, and inserting buffers to meet the timing constraints defined in the `.sdc` file.

**Analyzing the Improved Timing Report**

Once synthesis completed, a fresh STA report was generated:

- **Slack Improvement:** WNS moved from a negative value toward zero or became positive.  
- **Area Trade-off:** Slight increase in gate count and total area, which is expected when using larger, faster cells to fix timing.

**Verifying Custom Cell Usage**

The synthesis log was checked to confirm that the custom `sky130_vsdinv` cell was mapped in the netlist:

- **Result:** The log confirmed that the custom inverter was utilized, helping to meet the overall timing goals.


**Screenshots**

![Synthesis Slack 1](4_Configure_synthesis_setting%20to_fix_slack_1.png)  
![Synthesis Slack 2](4_Configure_synthesis_setting%20to_fix_slack_2.png)  
![Synthesis Slack 3](4_Configure_synthesis_setting%20to_fix_slack_3.png)  
![Synthesis Slack 4](4_Configure_synthesis_setting%20to_fix_slack_4.png)  
![Synthesis Slack 5](4_Configure_synthesis_setting%20to_fix_slack_5.png)  
![Synthesis Slack 6](4_Configure_synthesis_setting%20to_fix_slack_6.png)  
![Synthesis Slack 7](4_Configure_synthesis_setting%20to_fix_slack_7.png)  
![Synthesis Slack 8](4_Configure_synthesis_setting%20to_fix_slack_8.png)  
![Synthesis Slack 9](4_Configure_synthesis_setting%20to_fix_slack_9.png)  
![Synthesis Slack 10](4_Configure_synthesis_setting%20to_fix_slack_10.png)  
![Synthesis Slack 11](4_Configure_synthesis_setting%20to_fix_slack_11.png)  
![Synthesis Slack 12](4_Configure_synthesis_setting%20to_fix_slack_12.png)  
![Synthesis Slack 13](4_Configure_synthesis_setting%20to_fix_slack_13.png)  

