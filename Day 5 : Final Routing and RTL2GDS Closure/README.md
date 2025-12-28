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

