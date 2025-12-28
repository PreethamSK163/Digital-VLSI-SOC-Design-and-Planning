# Digital-VLSI-SOC-Design-and-Planning

## RTL to GDSII Implementation of RISC-V (picorv32a) using Sky130 PDK

This repository documents my complete end-to-end implementation of the **RTL to GDSII physical design flow** for a RISC-V core (`picorv32a`) using the **Sky130 open-source PDK** and industry-relevant open-source EDA tools.  
The project demonstrates practical hands-on experience across **synthesis, floorplanning, placement, CTS, timing closure, PDN design, routing, parasitic extraction, and final GDSII generation**.

The objective of this project was not only to run an automated flow, but to **understand, debug, optimize, and verify each physical design stage**, including the integration of a **custom-designed standard cell**.

---

## Tools and Technology Stack

- **PDK:** Sky130 (sky130A)
- **Design:** `picorv32a` RISC-V Core
- **Flow Manager:** OpenLANE
- **Synthesis:** Yosys + ABC
- **STA:** OpenSTA
- **CTS:** TritonCTS
- **Routing:** TritonRoute
- **Layout & GDS:** Magic
- **Parasitic Extraction:** SPEF Extractor
- **SPICE Simulation:** Ngspice
- **OS:** Ubuntu Linux

---

## Project Structure Overview

The repository is organized day-wise to reflect a real physical design learning and execution flow:

- **Day 1:** Open-source VLSI flow setup and synthesis
- **Day 2:** Floorplanning, placement, and standard cell alignment
- **Day 3:** Custom standard cell design and library integration
- **Day 4:** Timing analysis, ECOs, and clock tree synthesis
- **Day 5:** PDN, routing, parasitic extraction, and GDSII generation

Each day contains:
- Task-wise documentation
- Tool observations
- Timing and physical insights
- Screenshots for verification

---

## Day 1 – RTL Synthesis and Flow Initialization

On Day 1, I initialized the OpenLANE environment and synthesized the `picorv32a` RTL.

Key work done:
- Loaded RTL and verified design hierarchy
- Performed logic synthesis using Yosys
- Technology mapping using ABC with Sky130 standard cells
- Analyzed synthesis reports for:
  - Cell utilization
  - Area
  - Initial timing slack

This stage converted the **RTL into a gate-level netlist**, forming the foundation for physical implementation.

---

## Day 2 – Floorplanning, Placement, and Track Alignment

Day 2 focused on preparing the design for routing and power delivery.

Key work done:
- Defined die area and core utilization
- Generated standard cell rows aligned with routing tracks
- Performed placement and legalization
- Verified row orientation, spacing, and pin accessibility
- Analyzed congestion and placement density

This ensured the design was **physically feasible** before clock and signal routing.

---

## Day 3 – Custom Standard Cell Design and Library Integration

On Day 3, I designed and integrated a **custom inverter (`sky130_vsdinv`)** into the OpenLANE flow.

Key work done:
- Designed inverter schematic and simulated using Ngspice
- Created layout in Magic following Sky130 DRC rules
- Aligned layout to routing tracks
- Extracted LEF for physical abstraction
- Verified pin placement, boundaries, and obstructions
- Studied timing libraries (`.lib`) to understand:
  - Delay models
  - Slew
  - Capacitance
- Integrated the custom cell into synthesis and STA

This step bridged **circuit design, layout, and timing modeling**, providing full-stack understanding.

---

## Day 4 – Timing Closure and Clock Tree Synthesis (CTS)

Day 4 was dedicated to **timing optimization and clock distribution**.

Key work done:
- Pre-layout STA using OpenSTA
- Identified setup and hold violations
- Tuned synthesis parameters to reduce negative slack
- Performed manual Timing ECOs by upsizing cells
- Verified improvements using updated STA reports
- Executed CTS using TritonCTS
- Verified:
  - Clock skew
  - Clock latency
  - Hold slack
- Analyzed impact of bigger CTS buffers on:
  - Slew
  - Skew
  - Area
  - Power

This stage transformed the design from **ideal clock timing to real clock timing**, a critical step in physical sign-off.

---

## Day 5 – PDN, Routing, Parasitics, and GDSII Generation

Day 5 completed the RTL-to-GDSII flow.

Key work done:
- Generated Power Distribution Network (PDN)
  - Power rings
  - Power straps
  - Standard cell rails
- Verified power connectivity to all cells including custom inverter
- Executed global and detailed routing using TritonRoute
- Analyzed routing congestion and DRC behavior
- Performed SPEF extraction to model wire parasitics
- Executed post-route STA using real RC data
- Verified final setup and hold timing
- Generated final GDSII using Magic
- Performed final visual inspection of the layout

This stage produced a **fabrication-ready GDSII**.

---

## Key Technical Learnings

Through this project, I gained hands-on experience in:

- RTL-to-GDSII physical design flow
- Timing-driven synthesis and ECOs
- Clock tree synthesis and skew management
- Power integrity and PDN design
- Routing constraints and DRC awareness
- Parasitic extraction and sign-off STA
- Integration of custom standard cells into ASIC flow

---

## Project Status

The project is **fully completed**.

- RTL synthesized
- Timing closed
- CTS verified
- Routing completed
- Parasitics extracted
- GDSII generated
- Design is DRC-clean and timing-safe

---

## Conclusion

This repository represents a **complete, industry-aligned ASIC physical design project** using open-source tools and a real PDK.  
It reflects my ability to not only run tools, but to **analyze reports, debug violations, make architectural trade-offs, and achieve closure** across all major stages of chip implementation.

---

