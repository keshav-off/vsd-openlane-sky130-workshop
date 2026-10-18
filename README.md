# Digital VLSI SoC Design and Planning

![OS](https://img.shields.io/badge/OS-linux-orange)
![EDA Tools](https://img.shields.io/badge/EDA%20Tools-OpenLANE%2C%20Yosys%2C%20ABC%2C%20OpenROAD%2C%20TritonRoute%2C%20OpenSTA%2C%20Magic%2C%20Netgen-navy)
![Languages](https://img.shields.io/badge/languages-Verilog%2C%20Bash%2C%20TCL-crimson)

> Two-week digital VLSI SoC design and planning workshop covering the complete RTL to GDSII flow, organised by VSD in collaboration with NASSCOM (Advanced Physical Design using OpenLANE / Sky130).

This repository documents my work for the workshop: theory notes, commands, screenshots and results for every section.

## Progress

| Section | Topic | Status |
|---|---|---|
| 1 | Inception of open-source EDA, OpenLANE and Sky130 PDK | Done |
| 2 | Good floorplan vs bad floorplan and introduction to library cells | Pending |
| 3 | Design library cell using Magic layout and ngspice characterization | Pending |
| 4 | Pre-layout timing analysis and importance of good clock tree | Pending |
| 5 | Final steps for RTL to GDSII | Pending |

## Table of Contents

- [Section 1 - Inception of open-source EDA, OpenLANE and Sky130 PDK](#section-1---inception-of-open-source-eda-openlane-and-sky130-pdk)
- [Section 2 - Good floorplan vs bad floorplan and introduction to library cells](#section-2---good-floorplan-vs-bad-floorplan-and-introduction-to-library-cells)
- [Section 3 - Design library cell using Magic layout and ngspice characterization](#section-3---design-library-cell-using-magic-layout-and-ngspice-characterization)
- [Section 4 - Pre-layout timing analysis and importance of good clock tree](#section-4---pre-layout-timing-analysis-and-importance-of-good-clock-tree)
- [Section 5 - Final steps for RTL to GDSII](#section-5---final-steps-for-rtl-to-gdsii)

---

## Section 1 - Inception of open-source EDA, OpenLANE and Sky130 PDK

**Dates:** DD/MM/YYYY - DD/MM/YYYY

### Theory

<details>
<summary><b>Expand or Collapse</b></summary>

#### Package and chip

- In any embedded board we have seen, the part of the board we consider as the chip is only the PACKAGE of the chip which is nothing but a protective layer or packet bound over the actual chip and the actual manufatured chip is usually present at the center of a package wherein, the connections from package is fed to the chip by WIRE BOUND method which is none other than basic wired connection.



- Inside the chip, signals enter and leave through **pads**. The area enclosed by the pads is the **core**, where the digital logic lives. Core plus pads make up the **die**, the basic unit of manufacturing.
- A **foundry** is where chips are fabricated. **Foundry IPs** are blocks tied to a particular foundry, while **macros** are reusable digital blocks.

#### From application to hardware (ISA)

- A C program is compiled into assembly instructions (for example, **RISC-V ISA**), which an assembler turns into binary machine code.
- To run those instructions, we need an **RTL** implementation of the ISA. The RTL is synthesised into a gate-level netlist and taken through physical design to a layout.
- The course has three parts: the RISC-V ISA, RTL and synthesis of the picorv32 core, and physical design of picorv32.

> Add your own screenshots from the workshop slides here.

#### Open-source ASIC design

Open-source ASIC implementation needs three enablers:

1. RTL designs
2. EDA tools
3. PDK data

- A **PDK (Process Design Kit)** is the interface between designers and the fab: device models, technology information, design rules, standard cell libraries, I/O libraries and more.
- PDKs were traditionally shared only under NDA. In 2020, Google and SkyWater released the first open-source PDK for the 130 nm process (**Sky130**).
- An **ASIC flow** is software that links the required tools together to carry a design from RTL to GDSII.

#### OpenLANE ASIC flow

The main stages of the flow are:

- **Synthesis:** converts RTL into a gate-level netlist built from standard cells, functionally equivalent to the RTL.
- **Floorplanning:** chip floorplan, macro floorplan and power planning (upper metal layers are used for power because they are thicker and have lower resistance, which reduces IR drop and electromigration).
- **Placement:** global placement gives approximate cell locations (cells may overlap), and detailed placement legalises them onto site rows.
- **Clock tree synthesis (CTS):** distributes the clock to all sequential elements while limiting clock skew.
- **Routing:** global and detailed routing. Sky130 has six routing layers: a local interconnect layer followed by five metal layers.
- **Sign-off:** DRC (design rule check), LVS (layout vs schematic) and STA (static timing analysis).

</details>

### Implementation

**Tasks**

1. Run `picorv32a` design synthesis using the OpenLANE flow and generate the outputs.
2. Calculate the flop ratio.


$$Flop\Ratio = \frac{Number\ of\ D\ Flip\ Flops}{Total\ Number\ of\ Cells}$$

$$Percentage\ of\ DFFs = Flop\ Ratio \times 100$$

All Section 1 logs, reports and results are in this run folder:

[Section 1 Run - RUN_FOLDER_NAME](openlane/designs/picorv32a/runs/RUN_FOLDER_NAME)

#### 1. Run `picorv32a` synthesis using the OpenLANE flow

Commands to invoke the OpenLANE flow and run synthesis:

```bash
# Change to the OpenLANE flow directory
cd Desktop/work/tools/openlane_working_dir/openlane

# 'docker' is an alias for the long docker run command, set up earlier:
# alias docker='docker run -it -v $(pwd):/openLANE_flow -v $PDK_ROOT:$PDK_ROOT -e PDK_ROOT=$PDK_ROOT -u $(id -u $USER):$(id -g $USER) efabless/openlane:v0.21'
docker
```

```tcl
# Start the OpenLANE flow in interactive mode
./flow.tcl -interactive

# Load the required OpenLANE package
package require openlane 0.9

# Prepare the design (creates the necessary files and directories)
prep -design picorv32a

# Run synthesis
run_synthesis

# Exit the OpenLANE flow
exit

# Exit the docker sub-system
exit
```

Screenshots of the commands:

<!-- Replace the file names below with your own screenshots -->
<img width="1216" height="437" alt="Screenshot 2026-10-17 083117" src="https://github.com/user-attachments/assets/8c6528d6-ee67-4ecc-998b-9c10082d9a57" />

<img width="1225" height="527" alt="Screenshot 2026-10-17 083133" src="https://github.com/user-attachments/assets/07c7633b-52b6-4231-ab22-f02297136051" />

<img width="1212" height="802" alt="Screenshot 2026-10-17 084319" src="https://github.com/user-attachments/assets/2f4ec468-06fb-43f7-a68b-0a05815496ad" />


#### 2. Calculate the flop ratio

Screenshots of the synthesis statistics report with the required values highlighted:

<img width="812" height="575" alt="Screenshot 2026-10-17 085404" src="https://github.com/user-attachments/assets/795918e7-9405-49e9-a0bf-f6872a79f1e9" />

<img width="842" height="377" alt="Screenshot 2026-10-17 085428" src="https://github.com/user-attachments/assets/6db79a2b-e30e-4930-bb92-fc732e9cb43c" />

Calculation from the synthesis statistics report:

$$Flop\ Ratio = \frac{1613}{14876} = 0.108429685$$

$$Percentage\ of\ DFFs = 0.108429685 \times 100 = 10.8429685 \%$$

---

## Section 2 - Good floorplan vs bad floorplan and introduction to library cells

**Dates:** DD/MM/YYYY - DD/MM/YYYY

### Implementation

**Tasks**

1. Run `picorv32a` floorplan using the OpenLANE flow and generate the outputs.
2. Calculate the die area in microns from the values in the floorplan DEF.
3. Load the generated floorplan DEF in Magic and explore it.
4. Run congestion-aware placement for `picorv32a`.
5. Load the generated placement DEF in Magic and explore it.

$$Area\ of\ die = Die\ width\ (\mu m) \times Die\ height\ (\mu m)$$

_To be added._

---

## Section 3 - Design library cell using Magic layout and ngspice characterization

**Dates:** DD/MM/YYYY - DD/MM/YYYY

### Implementation

**Tasks**

1. Clone the custom inverter standard cell design from [vsdstdcelldesign](https://github.com/nickson-jose/vsdstdcelldesign).
2. Load the custom inverter layout in Magic and explore it.
3. Extract SPICE from the inverter layout in Magic.
4. Edit the SPICE model file for simulation.
5. Run post-layout ngspice simulations.
6. Find and fix the DRC problems in the old Magic tech file for the SkyWater process.

_To be added._

---

## Section 4 - Pre-layout timing analysis and importance of good clock tree

**Dates:** DD/MM/YYYY - DD/MM/YYYY

### Implementation

**Tasks**

_To be added after the section is completed._

---

## Section 5 - Final steps for RTL to GDSII


**Dates:** DD/MM/YYYY - DD/MM/YYYY

### Implementation

**Tasks**

_To be added after the section is completed._

---

## Acknowledgements

- [VLSI System Design (VSD)](https://www.vlsisystemdesign.com/) and NASSCOM for the workshop.
- The OpenLANE, SkyWater PDK and Magic open-source communities.
