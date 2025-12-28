# Day 4 : Pre-Placement Timing Verification and Clock Tree Optimization

## Summary of Day 4

On Day 4, I focused on bridging the gap between synthesized logic and physical implementation by performing detailed timing verification and optimizing the clock distribution network for the `picorv32a` design. The main achievements of the day include:

1. **Preparing Layout for Routing**
   - Converted custom inverter layouts into track-aligned cells.
   - Ensured pins and boundaries matched the Sky130 routing grid.
   - Generated a LEF file for the custom inverter to integrate with OpenLANE.

2. **Understanding and Integrating Timing Libraries**
   - Analyzed Liberty (.lib) files to link physical cells with timing models.
   - Verified cell delay, pin capacitance, and rise/fall transitions.
   - Ensured STA-ready netlists included both library cells and the custom inverter.

3. **Synthesis Optimization**
   - Identified and resolved slack violations using OpenLANE configuration adjustments.
   - Used gate sizing, buffer insertion, and fanout control to reduce negative slack.
   - Performed manual ECOs to fix critical paths without re-running full synthesis.

4. **Post-Synthesis Timing Analysis**
   - Leveraged OpenSTA to verify WNS, TNS, setup, and hold slacks.
   - Analyzed fanout, capacitance, and net delays to pinpoint performance bottlenecks.
   - Iteratively refined the netlist to meet timing constraints.

5. **Clock Tree Synthesis (CTS)**
   - Built the clock tree using TritonCTS to distribute the clock to all flip-flops.
   - Verified clock insertion delay, skew, and hold slack.
   - Observed placement of buffers in the DEF file for visual confirmation.

6. **Impact of Buffer Drive Strength**
   - Experimented with larger CTS buffers (`clkbuf_4`, `clkbuf_8`) to optimize transition and reduce skew.
   - Identified trade-offs in area and dynamic power consumption.
   - Determined the optimal buffer size that balanced Power, Performance, and Area (PPA).

## Task 1 – Converting Grid Info to Track Info

In this task, the custom inverter layout was prepared for the routing stage by aligning it with the technology grid and track requirements. Proper alignment ensures that the router can successfully connect standard cells without violating design rules.

**1. Accessing the Track Configuration File**

The process began by examining the `tracks.info` file from the Sky130 PDK, which defines routing behavior for each metal layer. Specific coordinates for layers such as `li1`, `met1`, and `met2` were identified. Key data points including the starting position of the first track (Offset) and the distance between consecutive tracks (Pitch) were noted for both horizontal and vertical directions.

**2. Understanding the Routing Grid**

Using the track information, the layout's routing grid was analyzed. The standard cell's width was verified to be an integer multiple of the track pitch. Routing tracks act as paths for metal wires, and misaligned pins would prevent proper automated routing.

**3. Aligning the Layout Grid**

The custom inverter layout was aligned to match the routing tracks. After adjustment, the grid visually represented the actual routing tracks of the Sky130 process, with horizontal and vertical lines intersecting at precise intervals.

**4. Verification of Pin Alignment**

The input and output pins of the inverter were carefully checked to ensure their centers were located at the intersections of horizontal and vertical tracks. This alignment guarantees that the global router can successfully connect the pins during the automated routing stage.

**5. Ensuring Symmetry and Boundary Alignment**

The boundaries of the standard cell were verified to ensure that the height and width are multiples of the vertical and horizontal track pitch, respectively. Additionally, the power (VDD) and ground (GND) rails were confirmed to be centered on the tracks, which is essential for proper automated Power Distribution Network (PDN) generation.



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

**1. Naming and Port Definition**

The first step was to associate all physical geometries with logical ports. Each port was assigned a class (input, output, or feedthrough) and a use (signal, power, or ground). The cell was given a unique name, `sky130_vsdinv`, to distinguish it from default library cells.

**2. Setting the Cell Boundary (PR Boundary)**

The "Place and Route" (PR) boundary of the cell was defined. This bounding box informs the placement tool of the space occupied by the cell. The boundary edges were aligned with the routing tracks from Task 1 to prevent overlaps or gaps when cells are placed side-by-side.

**3. Executing the LEF Extraction**

The Magic layout was converted into a Library Exchange Format (LEF) file. The terminal confirmed generation of `sky130_vsdinv.lef`. This LEF file contains abstract information such as the cell's size, pin locations (A and Y), and layer data, without including internal transistor details.

**4. Inspecting the Generated LEF File**

The LEF file was opened in a text editor to verify content:

- **MACRO Definition:** `MACRO sky130_vsdinv` block was present.  
- **PIN Information:** The `PIN A` and `PIN Y` sections matched the grid intersections set in Task 1.  
- **OBS (Obstruction):** Areas reserved for internal metals were correctly defined to prevent routing conflicts.

**5. Moving the LEF to the Design Directory**

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

**1. Identifying the Timing Library Corner**

Located the Liberty (.lib) files, primarily using `sky130_fd_sc_hd__tt_025C_1v80.lib`. Verified environmental parameters: **TT** (Typical-Typical process), **25°C**, and **1.80V**, matching real silicon conditions.

**2. Anatomy of a Cell Definition**

Checked the logical description of standard cells:

- **Cell Area:** Matches LEF dimensions.  
- **Leakage Power:** Observed idle power dissipation.

**3. Lookup Table (LUT) Model**

Analyzed 2D matrices used for delay calculation:

- **Input Transition vs Output Load:** Delay depends on input transition and output capacitance.  
- **Rise/Fall Tables:** Verified data aligns with delays from **ngspice**.

**4. Pin Characteristics & Capacitance**

Verified electrical load on pins:

- **Direction:** Pins are defined as `input` or `output`.  
- **Capacitance:** Used to calculate the load for preceding gates.

**5. Integration in OpenLANE**

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

**1. Identifying the Slack Violation**

Reviewed the STA report:

- **Negative Slack:** WNS indicated paths slower than the required clock period.  
- **Violation Path:** Specific gates in the `picorv32a` netlist exceeded the required arrival time.

**2. Modifying Synthesis Strategy**

Updated OpenLANE variables:

- **SYNTH_STRATEGY:** Prioritized delay reduction over area.  
- **SYNTH_BUFFERING:** Enabled insertion of buffers to strengthen weak signals.  
- **SYNTH_SIZING:** Enabled gate sizing to replace small gates with larger, higher-drive versions.

**3. Re-running the Synthesis**

Executed synthesis again:

- **Observation:** Terminal logs showed iterative logic restructuring, cell swapping, and buffer insertion to meet timing constraints.

**4. Analyzing the Improved Timing Report**

Generated a fresh STA report:

- **Slack Improvement:** WNS improved toward zero or positive.  
- **Area Trade-off:** Slight increase in gate count and area, expected with faster cells.

**5. Verifying Custom Cell Usage**

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

**1. Invoking the OpenSTA Tool**

Moved to the dedicated OpenSTA environment:

- Executed `sta` in the terminal.  
- **Purpose:** Provides detailed timing reports and allows isolation of critical paths.

**2. Loading the Timing Environment**

Sourced the necessary design data:

- **Read Libraries:** Loaded the Sky130 `.lib` files.  
- **Read Netlist:** Used the optimized Verilog netlist from Task 4.  
- **Read SDC:** Loaded the Synopsys Design Constraints file for clock and delay definitions.

**3. Reporting Timing Path Details**

Generated a comprehensive timing report:

- **Observation:** Detailed line-by-line breakdown of data arrival paths, showing delays from each gate and interconnect.

**4. Analyzing Fanout and Capacitance**

Checked for electrical violations:

- **Fanout:** Identified pins with high fanout slowing signals.  
- **Capacitance:** Verified nets for maximum allowed capacitance.  
- **Result:** Determined where buffers were needed to strengthen signals across the core.

**5. Refining the Design via OpenSTA**

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

**1. Identifying Setup Slack Violations**

I started by analyzing the STA reports to find paths where the **Data Arrival Time** was dangerously close to, or exceeding, the **Required Time**:

- **Setup Violation:** Occurs when the logic is too slow, and data doesn’t reach the destination flip-flop before the next clock edge.  
- **Targeting the WNS:** I focused on the **Worst Negative Slack (WNS)** to identify the longest logic chains in the `picorv32a` design.

**2. Adjusting Fanout Constraints**

I observed that high fanout can slow signals and cause setup violations:

- **`SYNTH_MAX_FANOUT`:** I lowered this value (e.g., 4 or 5) in the OpenLANE configuration.  
- **Effect:** This forced the tool to insert extra buffers to "split" the load, allowing each gate to switch quickly.

**3. Logic Restructuring and Gate Sizing**

I enabled more aggressive optimization settings to shrink the delay:

- **Cell Upsizing:** Allowed the tool to use "Drive Strength 2" or "Drive Strength 4" versions of cells to reduce delay.  
- **Logic Flattening:** Reduced the number of logic levels between flip-flops to simplify paths and save timing.

**4. Running the Optimized Synthesis**

With the updated constraints, I ran the synthesis again:

- **Terminal Observation:** I monitored the **ABC** pass, which performed technology mapping and logic optimization.  
- **Comparison:** I verified that both "Gate Delay" and "Net Delay" decreased compared to the previous run.

**5. Post-Optimization Verification**

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

**1. Identifying the Critical Path in OpenSTA**

I used OpenSTA to find nets or gates causing timing violations:

- Ran `report_checks` to identify a path with negative slack.  
- Targeted pins with high slew or capacitance slowing the signal.  
- Observed a specific buffer or inverter that was too small to drive its load.

**2. Performing the Manual Cell Replacement**

Instead of changing global variables, I swapped the problematic cell locally:

- Used `replace_cell <instance_name> <new_lib_cell_name>` to replace a small inverter with a larger, stronger version.  
- This fixed the local timing issue without affecting the rest of the design.

**3. Verifying the Slack Improvement**

After replacement, I re-ran timing checks:

- Used `report_checks -through <affected_net>` to observe slack.  
- Slack for that path improved from negative to positive by upsizing the gate and reducing transition time.

**4. Updating the Netlist**

Once satisfied with the changes, I saved the updated netlist:

- Ran `write_verilog <filename>.v` to store the ECOed netlist.  
- This new netlist now contains the faster gates and is ready for subsequent CTS and routing stages.

**5. Final Timing Clean-up**

I performed a final check to ensure no new violations were introduced:

- Verified that Total Negative Slack (TNS) for the entire `picorv32a` design had decreased toward zero.

**Screenshots :**
![7_Basic_timing_eco_1](7_Basic_timing_eco_1.png)
![7_Basic_timing_eco_2](7_Basic_timing_eco_2.png)


## Task 8 – Running CTS using TritonCTS

In this task, I transitioned from a timing-optimized netlist to building the actual clock distribution network. This ensures the clock signal reaches every flip-flop in the `picorv32a` core simultaneously, minimizing clock skew and fixing hold violations.

**1. Preparing the Environment for CTS**

Before running clock tree synthesis, I made sure the design was ready:

- Placement DEF file (from Day 2) and timing-optimized netlist (from ECO task) were available.  
- Cells were legalized and placed in rows, so the tool could locate all clock pins.  

**2. Executing the TritonCTS Command**

I ran the TritonCTS engine to construct the clock tree:

- The tool analyzed the clock net and built a "tree" structure.  
- Clock buffers from the Sky130 library were inserted to balance load and maintain signal integrity.  

**3. Monitoring the Clock Tree Construction**

While TritonCTS ran, I observed:

- **Root Pin Identification:** Main `clk` input pin detected.  
- **Sink Clustering:** Nearby flip-flops grouped under local clock buffers.  
- **Level-by-Level Balancing:** Buffers added to equalize delays from root to all flip-flops.

**4. Reviewing CTS Reports & Hold Slack Fix**

After CTS completed, I checked the reports:

- **Clock Skew:** Difference between fastest and slowest flip-flop arrival times.  
- **Clock Latency:** Time for clock to propagate from source to sinks.  
- **Hold Slack Improvement:** Adding physical buffers and delays naturally reduced hold violations.

**5. Inspecting the Post-CTS DEF**

The terminal confirmed creation of `results/cts/picorv32a.cts.def`:

- File contains thousands of new components (Clock Buffers like `sky130_fd_sc_hd__clkbuf_1`).  
- `clk` net now connects through a complex network of nets and buffers rather than directly to every flip-flop.


**Screenshots :**
![8_Lab_steps_to_run_CTS_using_Triton_CTS_1](8_Lab_steps_to_run_CTS_using_Triton_CTS_1.png)
![8_Lab_steps_to_run_CTS_using_Triton_CTS_2](8_Lab_steps_to_run_CTS_using_Triton_CTS_2.png)
![8_Lab_steps_to_run_CTS_using_Triton_CTS_3](8_Lab_steps_to_run_CTS_using_Triton_CTS_3.png)
![8_Lab_steps_to_run_CTS_using_Triton_CTS_4](8_Lab_steps_to_run_CTS_using_Triton_CTS_4.png)
![8_Lab_steps_to_run_CTS_using_Triton_CTS_5](8_Lab_steps_to_run_CTS_using_Triton_CTS_5.png)
![8_Lab_steps_to_run_CTS_using_Triton_CTS_6](8_Lab_steps_to_run_CTS_using_Triton_CTS_6.png)
![8_Lab_steps_to_run_CTS_using_Triton_CTS_7](8_Lab_steps_to_run_CTS_using_Triton_CTS_7.png)
![8_Lab_steps_to_run_CTS_using_Triton_CTS_8](8_Lab_steps_to_run_CTS_using_Triton_CTS_8.png)
![8_Lab_steps_to_run_CTS_using_Triton_CTS_9](8_Lab_steps_to_run_CTS_using_Triton_CTS_9.png)
![8_Lab_steps_to_run_CTS_using_Triton_CTS_10](8_Lab_steps_to_run_CTS_using_Triton_CTS_10.png)
![8_Lab_steps_to_run_CTS_using_Triton_CTS_11](8_Lab_steps_to_run_CTS_using_Triton_CTS_11.png)
![8_Lab_steps_to_run_CTS_using_Triton_CTS_12](8_Lab_steps_to_run_CTS_using_Triton_CTS_12.png)
![8_Lab_steps_to_run_CTS_using_Triton_CTS_13](8_Lab_steps_to_run_CTS_using_Triton_CTS_13.png)
![8_Lab_steps_to_run_CTS_using_Triton_CTS_14](8_Lab_steps_to_run_CTS_using_Triton_CTS_14.png)


## Task 9 – Verifying the CTS Run

In this task, I performed the final verification of the Clock Tree Synthesis. This step moves the design from "ideal" timing to "real" timing, where the clock travels through physical buffers and wires.

**1. Verification of the Post-CTS Netlist**

I first confirmed that the netlist reflects the physical clock tree:

- Used `read_verilog` or `current_design` to ensure the CTS-integrated netlist was loaded.  
- Searched for `clkbuf` instances and verified that the single `clk` net was replaced by a hierarchical network of Sky130 clock buffers.

**2. Inspecting the New Clock Path in OpenSTA**

I returned to OpenSTA to analyze how physical buffers affected timing:

- Ran `report_checks -path_delay min_max`.  
- Observed insertion delay representing the actual time for the clock to reach capture flip-flops.  
- Verified that source latency now had a positive value, reflecting real clock propagation.

**3. Skew and Slack Confirmation**

I analyzed the clock skew and hold slack:

- Verified global skew was within limits (typically < 10% of the clock period).  
- Checked hold slack, which improved due to buffer delays in the real clock network.

**4. Visual Verification in Magic**

To see the CTS results physically, I opened the generated DEF file in Magic:

- Ran `magic -T sky130A.tech read ../results/cts/picorv32a.cts.def`.  
- Zoomed into cell rows and identified clock buffers placed at regular intervals or clustered patterns.  
- Hovered over a clock buffer and used the `what` command to confirm it was part of the `clk` net hierarchy.

**5. Final Timing Report Generation**

Generated a final summary report for the `picorv32a` design:

- WNS (Worst Negative Slack) near zero or positive.  
- TNS (Total Negative Slack) minimized.  
- Design is now ready for final global and detailed routing.


**Screenshot:**
![9_Lab_steps_to_verify_cts_run_1](9_Lab_steps_to_verify_cts_run_1.png)
![9_Lab_steps_to_verify_cts_run_2](9_Lab_steps_to_verify_cts_run_2.png)
![9_Lab_steps_to_verify_cts_run_3](9_Lab_steps_to_verify_cts_run_3.png)
![9_Lab_steps_to_verify_cts_run_4](9_Lab_steps_to_verify_cts_run_4.png)
![9_Lab_steps_to_verify_cts_run_5](9_Lab_steps_to_verify_cts_run_5.png)
![9_Lab_steps_to_verify_cts_run_6](9_Lab_steps_to_verify_cts_run_6.png)
![9_Lab_steps_to_verify_cts_run_7](9_Lab_steps_to_verify_cts_run_7.png)
![9_Lab_steps_to_verify_cts_run_8](9_Lab_steps_to_verify_cts_run_8.png)
![9_Lab_steps_to_verify_cts_run_9](9_Lab_steps_to_verify_cts_run_9.png)
![9_Lab_steps_to_verify_cts_run_10](9_Lab_steps_to_verify_cts_run_10.png)
![9_Lab_steps_to_verify_cts_run_11](9_Lab_steps_to_verify_cts_run_11.png)
![9_Lab_steps_to_verify_cts_run_12](9_Lab_steps_to_verify_cts_run_12.png)
![9_Lab_steps_to_verify_cts_run_13](9_Lab_steps_to_verify_cts_run_13.png)

## Task 10 – Analyzing Timing with Real Clock

In this task, I performed the final timing audit of the `picorv32a` design using the Real Clock model. This accounts for the actual propagation delays through the physical buffers, verifying that the clock tree built earlier is robust.

**1. Switching from Ideal to Real Clock**

I configured the timing tool to recognize the physical clock tree:

- Set propagation mode with `set_propagated_clock [all_clocks]`.  
- This ensures that the timing engine calculates the delay through every buffer in the clock tree instead of assuming an ideal zero-delay clock.

**2. Deep Dive into Setup Analysis (Real Clock)**

I analyzed the Setup Slack with the real clock:

- Observed that the clock takes time to reach the Capture flip-flop, slightly shifting the available window for logic to settle.  
- Checked Worst Negative Slack (WNS) to ensure insertion delays did not cause setup violations.

**3. Deep Dive into Hold Analysis (Real Clock)**

Hold violations are affected by the physical clock distribution:

- Verified that the clock reaches Launch and Capture flip-flops at slightly different times.  
- Confirmed Hold Slack remained positive. Any negative slack here would require additional data-path buffers.

**4. Analyzing Slew and Transition Times**

With the real clock, signal slopes are physically realized:

- Inspected `report_clock_skew` output to view transition times at leaf clock pins.  
- Verified no clock pin exceeded the maximum transition time allowed by the Sky130 library, ensuring signal integrity and avoiding excessive power consumption.

**5. Final Timing Closure Report**

Generated full timing reports in OpenSTA:

- `report_checks -path_delay max` (for Setup)  
- `report_checks -path_delay min` (for Hold)  
- Confirmed that both Setup and Hold slacks were positive, meaning the design is stable and ready for final routing.


**Screenshot:**
![10_Analyze_timing_with_real_clock_1](10_Analyze_timing_with_real_clock_1.png)
![10_Analyze_timing_with_real_clock_2](10_Analyze_timing_with_real_clock_2.png)
![10_Analyze_timing_with_real_clock_3](10_Analyze_timing_with_real_clock_3.png)
![10_Analyze_timing_with_real_clock_4](10_Analyze_timing_with_real_clock_4.png)
![10_Analyze_timing_with_real_clock_5](10_Analyze_timing_with_real_clock_5.png)
![10_Analyze_timing_with_real_clock_6](10_Analyze_timing_with_real_clock_6.png)
![10_Analyze_timing_with_real_clock_7](10_Analyze_timing_with_real_clock_7.png)


## Task 11 – Executing OpenSTA with Correct Timing Libraries

In this task, I performed high-fidelity timing verification using OpenSTA, ensuring the analysis matched the exact same timing libraries used by OpenLANE. This step was essential for sign-off quality results before moving to the final physical stages.

**1. Synchronizing the Library Environment**

I configured OpenSTA to use the specific Sky130 library corners:

- Selected `sky130_fd_sc_hd__tt_025C_1v80.lib` (Typical corner) for setup analysis.  
- Verified that both the max (slowest) and min (fastest) library files were available to cover Setup and Hold scenarios.

**2. Loading the Post-CTS Netlist and DEF**

I loaded the latest physical data:

- The netlist generated after Clock Tree Synthesis (Task 8), which includes the clock buffers.  
- The DEF file with the physical placement of cells, enabling OpenSTA to estimate wire RC based on distances between pins.

**3. Setting Up the Analysis Variables**

Configured the STA environment to match OpenLANE:

- Set units with `set_cmd_units` (time in ns, capacitance in pF).  
- Read the Sky130 process models using `read_liberty`.  
- Loaded and linked the `picorv32a` netlist with `read_verilog` and `link_design`.

**4. Running the Timing Check**

Executed the timing analysis:

- Command: `report_checks -path_delay min_max -format full_clock_expanded`  
- Observed the true slack values, accounting for real drive strengths of the clock buffers and actual cell capacitances.

**5. Final Comparison and Sign-off**

Compared these results with OpenLANE internal logs:

- Verified that the slack values matched, confirming the automated flow’s timing is consistent with the standalone sign-off tool.  
- Ensured the `picorv32a` design cleared final timing verification with accurate physical delay modeling.


**Screenshot:**
![11_Execute_OPENSTA_with_right_timing_libraries_1](11_Execute_OPENSTA_with_right_timing_libraries_1.png)
![11_Execute_OPENSTA_with_right_timing_libraries_2](11_Execute_OPENSTA_with_right_timing_libraries_2.png)
![11_Execute_OPENSTA_with_right_timing_libraries_3](11_Execute_OPENSTA_with_right_timing_libraries_3.png)
![11_Execute_OPENSTA_with_right_timing_libraries_4](11_Execute_OPENSTA_with_right_timing_libraries_4.png)
![11_Execute_OPENSTA_with_right_timing_libraries_5](11_Execute_OPENSTA_with_right_timing_libraries_5.png)
![11_Execute_OPENSTA_with_right_timing_libraries_6](11_Execute_OPENSTA_with_right_timing_libraries_6.png)
![11_Execute_OPENSTA_with_right_timing_libraries_7](11_Execute_OPENSTA_with_right_timing_libraries_7.png)


## Task 12 – Observing the Impact of Bigger CTS Buffers

In this task, I explored the physical trade-offs of Clock Tree Synthesis by analyzing how the size of the clock buffers impacts timing, power, and area. By experimenting with larger buffers, I observed how "drive strength" can be used to fix skew and transition violations.

**1. Modifying the CTS Buffer List**

I modified the `CTS_CLK_BUFFER_LIST` to include larger, higher-drive variants from the Sky130 library (e.g., `sky130_fd_sc_hd__clkbuf_4` or `sky130_fd_sc_hd__clkbuf_8`).

- Larger buffers have lower output resistance, allowing them to charge the gate capacitances of the flip-flops faster.

**2. Impact on Transition (Slew) and Delay**

After re-running CTS with the larger buffers, I analyzed the timing reports:

- Faster Transitions: The **Slew Rate** on the clock pins decreased, resulting in sharper clock edges and improved switching predictability.  
- Reduced Insertion Delay: Stronger buffers reduced the total delay from the clock root to the sinks, helping with setup timing.

**3. Analyzing the Impact on Clock Skew**

I compared the skew of the design with small vs. large buffers:

- Better Balancing: Larger buffers can drive more flip-flops simultaneously, allowing a shallower clock tree and resulting in **Lower Clock Skew**.  
- Verified the improvement using `report_clock_skew` to see the arrival time difference between branches of the `picorv32a` tree.

**4. Trade-offs: Area and Power**

While timing improved, I observed the costs of using larger buffers:

- Increased Area: Larger buffers occupy more tracks in the layout.  
- Higher Dynamic Power: Larger transistors have higher gate capacitance, requiring more current to switch, increasing the power consumption of the clock network.

**5. Final Optimization Selection**

Based on the results, I determined the "Sweet Spot" for the `picorv32a` design:

- Verified that the improvement in timing and skew justified the increase in area, balancing Power, Performance, and Area (PPA) for the project.


**Screenshot:**
![12_Observe_impact_of_bigger_cts_buffers_1](12_Observe_impact_of_bigger_cts_buffers_1.png)
![12_Observe_impact_of_bigger_cts_buffers_2](12_Observe_impact_of_bigger_cts_buffers_2.png)
![12_Observe_impact_of_bigger_cts_buffers_3](12_Observe_impact_of_bigger_cts_buffers_3.png)
![12_Observe_impact_of_bigger_cts_buffers_4](12_Observe_impact_of_bigger_cts_buffers_4.png)
![12_Observe_impact_of_bigger_cts_buffers_5](12_Observe_impact_of_bigger_cts_buffers_5.png)
![12_Observe_impact_of_bigger_cts_buffers_6](12_Observe_impact_of_bigger_cts_buffers_6.png)
![12_Observe_impact_of_bigger_cts_buffers_7](12_Observe_impact_of_bigger_cts_buffers_7.png)
![12_Observe_impact_of_bigger_cts_buffers_8](12_Observe_impact_of_bigger_cts_buffers_8.png)
![12_Observe_impact_of_bigger_cts_buffers_9](12_Observe_impact_of_bigger_cts_buffers_9.png)
![12_Observe_impact_of_bigger_cts_buffers_10](12_Observe_impact_of_bigger_cts_buffers_10.png)
