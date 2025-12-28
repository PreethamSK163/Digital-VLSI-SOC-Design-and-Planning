# Day 3 – Magic-Based Standard Cell Design and SPICE Verification

## Summary of Day 3 

On Day 3, the focus was on **custom standard cell creation, layout verification, extraction, and electrical characterization** using Magic and ngspice, preparing the cell for integration into the `picorv32a` SoC design.

1. **I/O Placer Revision**
   - Refined the floorplan by adjusting I/O pin configurations.
   - Ensured uniform pin spacing to reduce routing congestion and improve timing slack.
   - Verified pin positions using Magic (`s` and `what` commands) and terminal logs.

2. **Cloning Custom Inverter Repository**
   - Pulled the `vsdstdcelldesign` repository containing the inverter layout and initial `.lib`/`.lef` files.
   - Verified correct directory structure and opened the inverter layout in Magic.
   - Ensured Sky130 technology file (`sky130A.tech`) was properly linked for accurate layer visualization.

3. **Sky130 Layer and Layout Analysis**
   - Identified PMOS and NMOS regions, polysilicon gates, metal layers, and substrate taps.
   - Mapped visual layout colors to actual Sky130 layers using Magic query commands.
   - Verified design rule compliance (DRC) to ensure manufacturability.

4. **Standard Cell Layout Creation and Port Identification**
   - Verified input (A), output (Y), VDD, and GND ports.
   - Created the extraction file (`.ext`) and converted it to a SPICE-compatible netlist (`.spice`) for simulation.
   - Inspected all horizontal and vertical components to confirm correctness.

5. **SPICE Deck Creation**
   - Incorporated Sky130 NMOS and PMOS models into the SPICE file.
   - Defined power supplies, input pulses, output load, and transient simulation commands.
   - Successfully ran the SPICE deck in ngspice with no errors.

6. **Inverter Characterization**
   - Plotted input vs output waveforms to observe inverter switching.
   - Measured **Rise Time (Tr)**, **Fall Time (Tf)**, and **Propagation Delay**.
   - Verified signal integrity and inverter threshold voltage (Vth) for balanced transistor sizing.


## Task 1 – I/O Placer Revision

This task involved refining the floorplan by adjusting the I/O pin configuration to optimize routing and timing for the `picorv32a` core.

**1. Modifying the I/O configuration**  

- **FP_IO_MODE:** Updated to switch between placement modes (e.g., from default/random to structured/equidistant).  
- **Engineering Goal:** Ensures shorter signal paths to internal logic, reducing wire length and improving timing slack.

**2. Re-running the floorplan stage**  

- **Command:** Executed `run_floorplan` or `place_io` to apply the new pin constraints.  
- **Observation:** Previous pin positions were cleared, and new coordinates were calculated based on the updated `FP_IO_MODE`.

**3. Verification of pin distribution in the logs**  

- **Pin Spacing:** Terminal confirmed number of pins per side (North, South, East, West).  
- **Layer Check:** Pins remained on assigned metal layers (e.g., `met2` for vertical, `met3` for horizontal) following new spacing rules.

**4. Visual comparison in Magic**  

- **Visual Audit:** Loaded updated `.def` file in **Magic** to observe physical shift in pin positions.  
- **Key Difference:** Pins were more uniformly spaced, avoiding clustering along die edges.

**5. Selection and querying of revised pins**  

- **Selection:** Hovered over pins and pressed **`s`** to highlight.  
- **Identification:** Used **`what`** command to verify correct net names and metal layer assignments.

**Screenshots :**  
![IO Placer Revision 1](1_IO_Placer%20revision_1.png)  
![IO Placer Revision 2](1_IO_Placer%20revision_2.png)  
![IO Placer Revision 3](1_IO_Placer%20revision_3.png)  
![IO Placer Revision 4](1_IO_Placer%20revision_4.png)  
![IO Placer Revision 5](1_IO_Placer%20revision_5.png)  
![IO Placer Revision 6](1_IO_Placer%20revision_6.png)  
![IO Placer Revision 7](1_IO_Placer%20revision_7.png)  
![IO Placer Revision 8](1_IO_Placer%20revision_8.png)  
![IO Placer Revision 9](1_IO_Placer%20revision_9.png)  
![IO Placer Revision 10](1_IO_Placer%20revision_10.png)

## Task 2 – Cloning the Custom Inverter Repository

This task involves setting up a custom standard cell that will be integrated into the `picorv32a` design for characterization and later use in the OpenLANE flow.

**1. Navigating to the OpenLANE working directory**  

- **Command:** Used `cd` to enter the `openlane` directory.  
- **Purpose:** Ensures that the custom inverter cell repository is placed in the correct location for integration into the flow.

**2. Executing the Git clone command**  

- **Command:** `git clone https://github.com/nickson-jose/vsdstdcelldesign.git`  
- **Observation:** Terminal logs showed "receiving objects" and "resolving deltas," confirming successful cloning of the repository.

**3. Verifying the cloned directory structure**  

- Used `ls` to inspect the contents of the `vsdstdcelldesign` folder.  
- **Key files identified:**  
  - `sky130_inv.mag` – Magic layout file for the inverter.  
  - `sky130_itools.tech` – Technology file for Magic to interpret Sky130 layers.  
  - `libs/` – Contains initial `.lib` and `.lef` files for the custom cell.

**4. Opening the layout in Magic**  

- **Command:** `magic -T sky130A.tech sky130_inv.mag &`  
- **Observation:** Magic opened the inverter layout, showing PMOS (top), NMOS (bottom), and layers like `ndiff`, `pdiff`, and `poly`.

**5. Environment synchronization**  

- Ensured that `sky130A.tech` is present or correctly linked.  
- Vital for Magic to correctly display layers, colors, and Sky130 design rules.

**Screenshots :**  
![Clone 1](2_Lab_steps_to_clone_github_1.png)  
![Clone 2](2_Lab_steps_to_clone_github_2.png)  
![Clone 3](2_Lab_steps_to_clone_github_3.png)  
![Clone 4](2_Lab_steps_to_clone_github_4.png)  
![Clone 5](2_Lab_steps_to_clone_github_5.png)


## Task 3 - Sky130 Basic Layer and Layout Analysis

**1. Identifying CMOS Components**  

With the `sky130_inv.mag` file open in Magic, you examined the physical structure of the inverter to identify the two primary transistors:

- **PMOS Transistor:** Located at the top of the layout, identified by the P-diffusion (`pdiff`) layer and its connection to the VDD rail.  
- **NMOS Transistor:** Located at the bottom of the layout, identified by the N-diffusion (`ndiff`) layer and its connection to the Ground (GND) rail.

**2. Analyzing the Poly-Silicon Gate**  

You observed the vertical Polysilicon (`poly`) strip that crosses both the `ndiff` and `pdiff` regions:

- **Gate Formation:** Intersection of the Poly layer and Diffusion layers forms the transistor channel.  
- **Common Input:** The Poly strip acts as a common gate for both transistors, forming the single input (A) of the inverter.

**3. Connection and Layer Verification**  

Using selection and query techniques, you identified the interconnects:

- **Metal 1 (li1):** Local interconnect layer for internal connections and output (Y) port.  
- **Contacts (mcon/viali):** Small squares connecting diffusion areas to metal layers.  
- **Substrate Taps:** N-well and P-substrate contacts bias the transistor bodies to prevent latch-up.

**4. Extracting Layer Information**  

You used the Magic command window to confirm layer details:

- **Selection:** Hovered cursor over a colored region and pressed `s`.  
- **Command:** Typed `what` in the `tkcon` window.  
- **Result:** Output displayed layer name (e.g., `nwell`, `poly`, `li1`), allowing mapping of visual colors to actual Sky130 layers.

**5. DRC (Design Rule Check) Status**  

You monitored the DRC indicator at the top of Magic:

- "0" or green checkmark confirmed layout follows Sky130 design rules (spacing, width, etc.).  
- Ensures manufacturability before extraction.

**Screenshots :**  
![Sky130 Layer Layout 1](3_Lab_to_Sky130_basic_layerlayouts_1.png)  
![Sky130 Layer Layout 2](3_Lab_to_Sky130_basic_layerlayouts_2.png)


## Task 4 - Standard Cell Layout Creation and Port Identification

**1. Verification of Port Connections**  

Using Magic, the input and output nodes of the inverter were verified:

- **Input Port (A):** Polysilicon gate connection.  
- **Output Port (Y):** Local Interconnect (`li1`) connecting PMOS and NMOS drains.  
- **Power & Ground Ports:** Top rail labeled VDD, bottom rail labeled GND.

**2. Layout Selection and Verification**  

- **`s` (Select):** Highlighted all transistors and interconnects to ensure selection.  
- **`v` (View):** Fit the layout in the window to visually check symmetry between PMOS and NMOS.

**3. Creating the Extraction File (.ext)**  

- **Command:** `extract all` in Magic `tkcon`.  
- **Result:** Generated `.ext` file containing layer areas, overlaps, and parasitic information.

**4. Converting to SPICE Format**  

- **Commands:**  
  1. `ext2spice lvs` – Layout vs Schematic mode.  
  2. `ext2spice` – Final SPICE netlist conversion.  
- **Observation:** Created `sky130_inv.spice` representing transistors with parasitic capacitances.

**5. Final Layout Inspection with 'what' Command**  

- **Process:** Placed cursor on VDD rail (horizontal) and Poly gate (vertical), pressed `s`, typed `what`.  
- **Confirmation:** Horizontal metal is `li1`, vertical component is `poly`, verifying layout correctness.

**Screenshots :**  
![Std Cell Layout 1](4_Lab_step_to_creat_std_layout_1.png)  
![Std Cell Layout 2](4_Lab_step_to_creat_std_layout_2.png)  
![Std Cell Layout 3](4_Lab_step_to_creat_std_layout_3.png)  
![Std Cell Layout 4](4_Lab_step_to_creat_std_layout_4.png)  
![Std Cell Layout 5](4_Lab_step_to_creat_std_layout_5.png)  
![Std Cell Layout 6](4_Lab_step_to_creat_std_layout_6.png)  
![Std Cell Layout 7](4_Lab_step_to_creat_std_layout_7.png)  
![Std Cell Layout 8](4_Lab_step_to_creat_std_layout_8.png)

## Task 5 - Creating the Final SPICE Deck

**1. Opening the Extracted SPICE File**  

Opened `sky130_inv.spice` and verified transistor definitions (PMOS/NMOS) and parasitic capacitances from Magic extraction.

**2. Including Sky130 Technology Models**  

- Added include statements for Sky130 NMOS (`nshort.lib`) and PMOS (`pshort.lib`) files.  
- Ensures simulator recognizes transistor electrical characteristics (Vth, mobility, etc.).

**3. Defining Power Supplies and Inputs**  

- **VDD Supply:** 1.8V between VDD and GND.  
- **Pulse Input (A):** Defined initial value, pulsed value, delay, rise/fall times, and pulse width at input node.  
- **Observation:** Allows observation of inverter switching behavior.

**4. Setting the Parasitic Capacitance and Load**  

- Verified extracted parasitic capacitances and output load (Cload) at output node Y.  
- Ensures realistic delay and waveform simulation.

**5. Adding Simulation Commands**  

- **`.tran` (Transient Analysis):** e.g., `0.01n` step to `20n` total time.  
- **`.option`:** Scale set to microns to match Magic layout.

**6. Executing the Simulation in ngspice**  

- **Command:** `ngspice sky130_inv.spice`  
- Simulator loads the circuit, includes libraries, and prepares waveform data.  
- Terminal checked for errors or warnings to confirm SPICE deck correctness.

**Screenshots :**  
![SPICE Deck 1](5_Create_final_SPICE_deck_using_Sky130_tech_1.png)  
![SPICE Deck 2](5_Create_final_SPICE_deck_using_Sky130_tech_2.png)  
![SPICE Deck 3](5_Create_final_SPICE_deck_using_Sky130_tech_3.png)  
![SPICE Deck 4](5_Create_final_SPICE_deck_using_Sky130_tech_4.png)  
![SPICE Deck 5](5_Create_final_SPICE_deck_using_Sky130_tech_5.png)  
![SPICE Deck 6](5_Create_final_SPICE_deck_using_Sky130_tech_6.png)  
![SPICE Deck 7](5_Create_final_SPICE_deck_using_Sky130_tech_7.png)  
![SPICE Deck 8](5_Create_final_SPICE_deck_using_Sky130_tech_8.png)  
![SPICE Deck 9](5_Create_final_SPICE_deck_using_Sky130_tech_9.png)

## Task 6 - Inverter Characterization and Timing Analysis

**1. Generating the Transient Waveform**  

- Ran the SPICE deck in `ngspice` and plotted input vs output.  
- **Command:** `plot y vs time a`  
- Observed classic inverter behavior with non-ideal slopes due to parasitic capacitances.

**2. Measuring Rise Time (Tr)**  

- Rise time is the duration for the output to transition from 10% to 90% of VDD.  
- Used cursor in ngspice plot to mark 10% and 90% points.  
- **Calculation:** Tr = t90% - t10%

**3. Measuring Fall Time (Tf)**  

- Fall time is the duration for the output to transition from 90% to 10% of VDD.  
- Used plot cursor to identify t90% and t10% on falling edge.  
- **Calculation:** Tf = t90% - t10%

**4. Calculating Propagation Delay**  

- Measured the time difference between input and output reaching 50% voltage.  
- **Rise Delay:** Output rising.  
- **Fall Delay:** Output falling.  
- Influenced by PMOS/NMOS sizing and output load (Cload).

**5. Analyzing Signal Integrity**  

- Verified inverter threshold voltage (Vth) at the crossing point of input/output waveforms.  
- Checked that crossing occurs near VDD/2 to ensure balanced PMOS and NMOS sizing.

**Screenshots:**  
![Inverter Characterization 1](6_Characterize_inverter_using_sky130_model_files_1.png)  
![Inverter Characterization 2](6_Characterize_inverter_using_sky130_model_files_2.png)  
![Inverter Characterization 3](6_Characterize_inverter_using_sky130_model_files_3.png)  
![Inverter Characterization 4](6_Characterize_inverter_using_sky130_model_files_4.png)  
![Inverter Characterization 5](6_Characterize_inverter_using_sky130_model_files_5.png)  
![Inverter Characterization 6](6_Characterize_inverter_using_sky130_model_files_6.png)  
![Inverter Characterization 7](6_Characterize_inverter_using_sky130_model_files_7.png)  
![Inverter Characterization 8](6_Characterize_inverter_using_sky130_model_files_8.png)  
![Inverter Characterization 9](6_Characterize_inverter_using_sky130_model_files_9.png)  
![Inverter Characterization 10](6_Characterize_inverter_using_sky130_model_files_10.png)  
![Inverter Characterization 11](6_Characterize_inverter_using_sky130_model_files_11.png)  
![Inverter Characterization 12](6_Characterize_inverter_using_sky130_model_files_12.png)
