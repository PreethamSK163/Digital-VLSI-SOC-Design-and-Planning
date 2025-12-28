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

