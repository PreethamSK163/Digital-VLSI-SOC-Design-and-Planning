# Day 3 – Magic-Based Standard Cell Design and SPICE Verification

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
