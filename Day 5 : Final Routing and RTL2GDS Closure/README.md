# Day 5 : Final Routing and RTL2GDS Closure


## Task 1 – Building the Power Distribution Network (PDN)

Before routing the signal wires, I built the **Power Distribution Network (PDN)** to ensure every cell in the `picorv32a` core receives stable power without significant voltage drops.

**1. Understanding the PDN Hierarchy**

The PDN is a multi-layer grid carrying high current across the die:

- **Power Rings:** Thick metal tracks around the core distributing power from the pads.  
- **Power Straps:** Horizontal and vertical wide tracks (on `met4`/`met5`) bringing power toward the center.  
- **Power Rails:** Thin `met1` rails along each cell row connecting transistors to power.

**2. Executing the PDN Generation Command**

I ran the OpenLANE command:

- **Command:** `gen_pdn`  
- It read the `pdn.tcl` file, defining strap pitches, widths, and offsets according to Sky130 rules.

**3. Analyzing the Tool Output**

I monitored the script for key steps:

- Calculated the number of horizontal and vertical straps.  
- Placed **Vias** at strap intersections and connections to the rails.  
- Verified that the VDD and GND rails were properly connected.

**4. Verification in Magic**

I opened the DEF in Magic:

- **Command:** `magic -T sky130A.tech read ../results/floorplan/picorv32a.pdn.def`  
- I checked the wide metal mesh and confirmed my inverter rails were connected to the PDN.

**5. Finalizing for Routing**

The PDN DEF was generated, providing a solid foundation for signal routing.

**Screenshots:**
![1_Build_power_distribution_network_1](1_Build_power_distribution_network_1.png)
![1_Build_power_distribution_network_2](1_Build_power_distribution_network_2.png)


## Task 2 – From Power Straps to Standard Cell Connectivity

After building the PDN, I verified that the power straps are correctly aligned with the standard cell rows and checked how the tool ensures connectivity for every cell, including my custom inverter.

**1. Verification of Strap-to-Rail Alignment**

I confirmed that the wide **Power Straps** effectively transfer current to the narrow **Standard Cell Rails**:

- **The "Tap" Process:** Observed how vias connect high-level metals (like `met4`/`met5`) down to the `met1` rails.  
- **Alignment Check:** Ensured horizontal VDD and GND rails of the standard cells aligned with the PDN grid to avoid shorts or open circuits.

**2. Identifying TDC and Special Cells**

I checked for **TDC (Timing Data Cells)** or Tap cells in the layout:

- **Purpose:** These cells connect the N-well to VDD and the P-substrate to GND.  
- **Latch-up Prevention:** Essential to prevent latch-up that could damage the chip.  
- **Observation:** Verified the density and placement of these cells across the `picorv32a` floorplan.

**3. Physical Connectivity of the Custom Inverter**

I inspected my custom `sky130_vsdinv`:

- **Strap Proximity:** Confirmed the inverter is close to a vertical power strap to minimize resistance.  
- **Via Check:** Used Magic to verify vias connecting the inverter’s local `met1` power pins to the global PDN.

**4. Transitioning to Signal Routing**

With power connectivity verified, I prepared the design for routing:

- **Congestion Analysis:** Checked terminal reports for routing resources left after PDN placement.  
- **Routing Grid:** Ensured previously defined routing tracks were clear for the signal router.

**5. Final DEF Export**

Generated a refined DEF including:

1. Placed Standard Cells.  
2. Clock Tree.  
3. Complete Power Distribution Network.  
4. Well-tap and Decap cells.

**Screenshot:**
![2_Lab_step_from_power_strap_to_tdc_cell_1](2_Lab_step_from_power_strap_to_tdc_cell_1.png)


## Task 3 – Global vs Detailed Routing and TritonRoute Configuration

In this task, I moved into the routing phase, where the actual metal connections are created between all placed cells based on the synthesized netlist.

**1. Two-Stage Routing Flow**

Routing is performed in two main stages to manage complexity:

- **Global Routing (FastRoute):** The design is divided into grid-based G-cells, and approximate paths are planned for each net to reduce congestion.  
- **Detailed Routing (TritonRoute):** Exact metal shapes and vias are placed while strictly following Sky130 DRC rules.

**2. TritonRoute Technology Awareness**

I verified how TritonRoute adapts to the Sky130 process:

- **LEF-Based Routing:** The router reads standard cell LEFs and the custom inverter LEF to identify pins and routing blockages.  
- **Routing Algorithm:** TritonRoute applies optimization techniques to prioritize boundary pins first and then complete internal routing.

**3. Routing Configuration Parameters**

Before running routing, I checked key OpenLANE variables:

- **`ROUTING_STRATEGY`:** Controls the balance between runtime and routing quality.  
- **`ROUTING_OPT_ITERS`:** Defines the number of optimization passes to reduce DRC violations.

**4. Pre-routing Analysis**

Before detailed routing, the tool performs preparation steps:

1. Identifies all nets in the `picorv32a` design.  
2. Builds a connectivity graph.  
3. Assigns metal layers (`met1`–`met5`) based on net length and resistance requirements, with long nets pushed to higher layers.

**5. Executing the Routing Command**

I initiated the routing process using:

- **Command:** `run_routing`  
- **Observation:** Initial DRC violations were reported during early iterations, which is expected and handled in subsequent optimization passes.

**Screenshot:**
![3_Basics_of_global_and_detail_routing_and_configure_TritonRoute_1](3_Basics_of_global_and_detail_routing_and_configure_TritonRoute_1.png)
![3_Basics_of_global_and_detail_routing_and_configure_TritonRoute_2](3_Basics_of_global_and_detail_routing_and_configure_TritonRoute_2.png)
![3_Basics_of_global_and_detail_routing_and_configure_TritonRoute_3](3_Basics_of_global_and_detail_routing_and_configure_TritonRoute_3.png)
![3_Basics_of_global_and_detail_routing_and_configure_TritonRoute_4](3_Basics_of_global_and_detail_routing_and_configure_TritonRoute_4.png)

## Task 4 – Post-Route Parasitic Extraction and Final GDSII Generation

In this task, I completed the post-routing sign-off steps by extracting real wire parasitics and generating the final manufacturable GDSII layout of the `picorv32a` design.

**1. Post-Route Parasitic Extraction (SPEF)**

After detailed routing, all interconnects became physical metal wires with resistance and capacitance. I extracted these parasitics using a standalone SPEF extractor to make timing analysis realistic.

- The merged LEF and final routed DEF were used as inputs.  
- The extractor generated a `.spef` file containing wire RC data for every net.

**SPEF Generation Syntax Used**

The following commands were used to perform post-route parasitic extraction using the standalone SPEF extractor:

**Change directory**

- `cd Desktop/work/tools/SPEF_EXTRACTOR`

**Command extract spef**

- `python3 main.py /home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/27-12_12-17/tmp/merged.lef /home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/27-12_12-17/results/routing/14-fastroute.def`


**2. Transition from Routing to Timing Sign-off**

With the SPEF file available, timing analysis now reflected real physical behavior:

- Wire delays were added to cell delays.  
- Setup and hold timing were verified with actual interconnect resistance and capacitance.  
- This confirmed that timing closure was still maintained after routing.

**3. Final GDSII Generation (Tape-Out Stage)**

Once timing was validated, I proceeded to generate the final layout database:

- Magic was used to stream out the GDSII file.  
- The generated `picorv32a.gds` contains all geometric layers required for fabrication.  
- This step marks the official RTL-to-GDS completion.

**4. Visual Inspection of Final Layout**

The final GDSII was opened in Magic for inspection:

- Verified complete signal routing, clock tree, and PDN integration.  
- Confirmed presence of standard cells, clock buffers, and the custom `sky130_vsdinv` cell.  
- Ensured the design was DRC-clean and fabrication-ready.

**Screenshot:**
![4_Post_Route_Parasitic_Extraction_and_Final_GDSII_Generation_1](4_Post_Route_Parasitic_Extraction_and_Final_GDSII_Generation_1.png)
![4_Post_Route_Parasitic_Extraction_and_Final_GDSII_Generation_2](4_Post_Route_Parasitic_Extraction_and_Final_GDSII_Generation_2.png)
![4_Post_Route_Parasitic_Extraction_and_Final_GDSII_Generation_3](4_Post_Route_Parasitic_Extraction_and_Final_GDSII_Generation_3.png)
![4_Post_Route_Parasitic_Extraction_and_Final_GDSII_Generation_4](4_Post_Route_Parasitic_Extraction_and_Final_GDSII_Generation_4.png)
![4_Post_Route_Parasitic_Extraction_and_Final_GDSII_Generation_5](4_Post_Route_Parasitic_Extraction_and_Final_GDSII_Generation_5.png)
![4_Post_Route_Parasitic_Extraction_and_Final_GDSII_Generation_6](4_Post_Route_Parasitic_Extraction_and_Final_GDSII_Generation_6.png)
