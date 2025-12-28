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

In this task, I bridged the gap between physical layout and timing analysis. Understanding the timing libraries ensures OpenLANE knows how much delay the custom inverter adds to the `picorv32a` design.

**Identifying the Timing Library Corner**

Located the Liberty (.lib) files, primarily using `sky130_fd_sc_hd__tt_025C_1v80.lib`. Verified environmental parameters: **TT** (Typical-Typical process), **25°C**, and **1.80V**, matching real silicon conditions.

**Anatomy of a Cell Definition**

Checked the logical description of standard cells:

- **Cell Area:** Matches LEF dimensions.  
- **Leakage Power:** Observed idle power dissipation.

**Lookup Table (LUT) Model**

Analyzed 2D matrices used for delay calculation:

- **Input Transition vs Output Load:** Delay depends on input transition and output capacitance.  
- **Rise/Fall Tables:** Verified data aligns with delays from **ngspice**.

**Pin Characteristics & Capacitance**

Verified electrical load on pins:

- **Direction:** Pins are defined as `input` or `output`.  
- **Capacitance:** Used to calculate the load for preceding gates.

**Integration in OpenLANE**

Loaded the `.lib` files into the session, linked the `picorv32a` netlist, and confirmed timing models, including the custom inverter, were ready for STA.

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

In this task, I focused on resolving timing violations from the initial synthesis by adjusting OpenLANE configuration to prioritize speed over area and eliminate Negative Slack.

**Identifying the Slack Violation**

Reviewed the STA report:

- **Negative Slack:** WNS indicated paths slower than the required clock period.  
- **Violation Path:** Specific gates in the `picorv32a` netlist exceeded the required arrival time.

**Modifying Synthesis Strategy**

Updated OpenLANE variables:

- **SYNTH_STRATEGY:** Prioritized delay reduction over area.  
- **SYNTH_BUFFERING:** Enabled insertion of buffers to strengthen weak signals.  
- **SYNTH_SIZING:** Enabled gate sizing to replace small gates with larger, higher-drive versions.

**Re-running the Synthesis**

Executed synthesis again:

- **Observation:** Terminal logs showed iterative logic restructuring, cell swapping, and buffer insertion to meet timing constraints.

**Analyzing the Improved Timing Report**

Generated a fresh STA report:

- **Slack Improvement:** WNS improved toward zero or positive.  
- **Area Trade-off:** Slight increase in gate count and area, expected with faster cells.

**Verifying Custom Cell Usage**

Checked the synthesis log:

- **Result:** The custom `sky130_vsdinv` inverter was mapped and contributed to meeting timing goals.


**Screenshots :**

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


## Task 5 – OpenSTA for Post-Synthesis Timing Analysis

In this task, I performed a detailed STA outside of the automated OpenLANE flow to inspect timing paths in the `picorv32a` design after synthesis optimizations.

**Invoking the OpenSTA Tool**

Moved to the dedicated OpenSTA environment:

- Executed `sta` in the terminal.  
- **Purpose:** Provides detailed timing reports and allows isolation of critical paths.

**Loading the Timing Environment**

Sourced the necessary design data:

- **Read Libraries:** Loaded the Sky130 `.lib` files.  
- **Read Netlist:** Used the optimized Verilog netlist from Task 4.  
- **Read SDC:** Loaded the Synopsys Design Constraints file for clock and delay definitions.

**Reporting Timing Path Details**

Generated a comprehensive timing report:

- **Observation:** Detailed line-by-line breakdown of data arrival paths, showing delays from each gate and interconnect.

**Analyzing Fanout and Capacitance**

Checked for electrical violations:

- **Fanout:** Identified pins with high fanout slowing signals.  
- **Capacitance:** Verified nets for maximum allowed capacitance.  
- **Result:** Determined where buffers were needed to strengthen signals across the core.

**Refining the Design via OpenSTA**

Performed manual adjustments for timing closure:

- **Upsizing Cells:** Replaced critical gates with stronger versions.  
- **Slack Verification:** Re-ran reports to confirm improvements and iteratively tuned the netlist before moving to physical design.




**Screenshots :**

![OpenSTA Timing 1](5_OpenSTA_for_post_synth_timing_analysis_1.png)  
![OpenSTA Timing 2](5_OpenSTA_for_post_synth_timing_analysis_2.png)  
![OpenSTA Timing 3](5_OpenSTA_for_post_synth_timing_analysis_3.png)  
![OpenSTA Timing 4](5_OpenSTA_for_post_synth_timing_analysis_4.png)  
![OpenSTA Timing 5](5_OpenSTA_for_post_synth_timing_analysis_5.png)  
![OpenSTA Timing 6](5_OpenSTA_for_post_synth_timing_analysis_6.png)  
![OpenSTA Timing 7](5_OpenSTA_for_post_synth_timing_analysis_7.png)  
![OpenSTA Timing 8](5_OpenSTA_for_post_synth_timing_analysis_8.png)  


## Task 6 – Optimizing Synthesis to Reduce Setup Violations

In this task, I focused on a final round of synthesis optimization. While previous tasks targeted general timing, this step was specifically aimed at reducing **Setup Violations** by aggressively restructuring the logic and adjusting cell sizes.

**Identifying Setup Slack Violations**

I started by analyzing the STA reports to find paths where the **Data Arrival Time** was dangerously close to, or exceeding, the **Required Time**:

- **Setup Violation:** Occurs when the logic is too slow, and data doesn’t reach the destination flip-flop before the next clock edge.  
- **Targeting the WNS:** I focused on the **Worst Negative Slack (WNS)** to identify the longest logic chains in the `picorv32a` design.

**Adjusting Fanout Constraints**

I observed that high fanout can slow signals and cause setup violations:

- **`SYNTH_MAX_FANOUT`:** I lowered this value (e.g., 4 or 5) in the OpenLANE configuration.  
- **Effect:** This forced the tool to insert extra buffers to "split" the load, allowing each gate to switch quickly.

**Logic Restructuring and Gate Sizing**

I enabled more aggressive optimization settings to shrink the delay:

- **Cell Upsizing:** Allowed the tool to use "Drive Strength 2" or "Drive Strength 4" versions of cells to reduce delay.  
- **Logic Flattening:** Reduced the number of logic levels between flip-flops to simplify paths and save timing.

**Running the Optimized Synthesis**

With the updated constraints, I ran the synthesis again:

- **Terminal Observation:** I monitored the **ABC** pass, which performed technology mapping and logic optimization.  
- **Comparison:** I verified that both "Gate Delay" and "Net Delay" decreased compared to the previous run.

**Post-Optimization Verification**

Finally, I checked the results of the optimization:

- **Slack Status:** I confirmed that the setup violations were eliminated or significantly reduced.  
- **Netlist Integrity:** I ensured that the functional logic remained intact despite the aggressive optimization and added buffers.

**Screenshots**

![Optimized Synthesis 1](6_Optimize_synthesis_to_reduce_step_violation_1.png)  
![Optimized Synthesis 2](6_Optimize_synthesis_to_reduce_step_violation_2.png)  
![Optimized Synthesis 3](6_Optimize_synthesis_to_reduce_step_violation_3.png)  
![Optimized Synthesis 4](6_Optimize_synthesis_to_reduce_step_violation_4.png)  
![Optimized Synthesis 5](6_Optimize_synthesis_to_reduce_step_violation_5.png)  
![Optimized Synthesis 6](6_Optimize_synthesis_to_reduce_step_violation_6.png)  
![Optimized Synthesis 7](6_Optimize_synthesis_to_reduce_step_violation_7.png)  
![Optimized Synthesis 8](6_Optimize_synthesis_to_reduce_step_violation_8.png)  
![Optimized Synthesis 9](6_Optimize_synthesis_to_reduce_step_violation_9.png)  
![Optimized Synthesis 10](6_Optimize_synthesis_to_reduce_step_violation_10.png)


## Task 7 – Basic Timing ECO (Engineering Change Order)

In this task, I performed a manual ECO to fix specific timing violations without re-running the entire synthesis flow. This was crucial for closing timing on critical paths where automated tools needed extra guidance.

**Identifying the Critical Path in OpenSTA**

I used OpenSTA to find nets or gates causing timing violations:

- Ran `report_checks` to identify a path with negative slack.  
- Targeted pins with high slew or capacitance slowing the signal.  
- Observed a specific buffer or inverter that was too small to drive its load.

**Performing the Manual Cell Replacement**

Instead of changing global variables, I swapped the problematic cell locally:

- Used `replace_cell <instance_name> <new_lib_cell_name>` to replace a small inverter with a larger, stronger version.  
- This fixed the local timing issue without affecting the rest of the design.

**Verifying the Slack Improvement**

After replacement, I re-ran timing checks:

- Used `report_checks -through <affected_net>` to observe slack.  
- Slack for that path improved from negative to positive by upsizing the gate and reducing transition time.

**Updating the Netlist**

Once satisfied with the changes, I saved the updated netlist:

- Ran `write_verilog <filename>.v` to store the ECOed netlist.  
- This new netlist now contains the faster gates and is ready for subsequent CTS and routing stages.

**Final Timing Clean-up**

I performed a final check to ensure no new violations were introduced:

- Verified that Total Negative Slack (TNS) for the entire `picorv32a` design had decreased toward zero.

**Screenshots :**
![7_Basic_timing_eco_1](7_Basic_timing_eco_1.png)
![7_Basic_timing_eco_2](7_Basic_timing_eco_2.png)
