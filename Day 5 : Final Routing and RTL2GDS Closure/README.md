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

