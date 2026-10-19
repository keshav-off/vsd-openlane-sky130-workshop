# Digital VLSI SoC Design and Planning

> Two-week digital VLSI SoC design and planning workshop covering the RTL-to-GDSII flow using OpenLANE and the Sky130 PDK, organised by VSD in collaboration with NASSCOM.

This repository documents my progress through the workshop. Commands follow the workshop environment; screenshots and run-specific results are intentionally left as placeholders so I can add my own evidence.

## Table of Contents

- [Workshop Overview](#workshop-overview)
- [Tools and PDK](#tools-and-pdk)
- [Section 1 — Open-source EDA, OpenLANE and Sky130](#section-1--open-source-eda-openlane-and-sky130)
- [Section 2 — Floorplanning and Placement](#section-2--floorplanning-and-placement)
- [Section 3 — Standard-cell Layout and Characterisation](#section-3--standard-cell-layout-and-characterisation)
- [Section 4 — Timing Analysis and Custom-cell Integration](#section-4--timing-analysis-and-custom-cell-integration)
- [Section 5 — RTL-to-GDSII: PDN, Routing and Timing](#section-5--rtl-to-gdsii-pdn-routing-and-timing)
- [Key Concepts](#key-concepts)
- [Certificate](#certificate)
- [Acknowledgements](#acknowledgements)
- [Resources](#resources)

## Workshop Overview

The workshop introduces the open-source digital ASIC implementation flow, from RTL and synthesis through floorplanning, placement, clock-tree synthesis, power distribution, routing, and sign-off analysis.

### Typical RTL-to-GDSII flow

1. RTL design and configuration
2. Synthesis and gate-level netlist generation
3. Floorplanning and I/O placement
4. Power planning
5. Standard-cell placement
6. Clock-tree synthesis (CTS)
7. Global and detailed routing
8. Parasitic extraction
9. Static timing analysis (STA), DRC and LVS checks

### Screenshot — Workshop / Flow Overview

<!-- Add screenshot here -->
<!-- ![RTL-to-GDSII flow](images/section-0-flow.png) -->

## Tools and PDK

- **OpenLANE** — automated RTL-to-GDSII implementation flow
- **Sky130** — open-source 130 nm process design kit
- **Magic** — layout viewing and editing
- **ngspice** — circuit simulation
- **OpenSTA / OpenROAD** — timing analysis and physical-design operations
- **Docker** — reproducible tool environment

> **Environment note:** Paths, run tags, PDK locations and tool versions can differ by machine. Update paths to match your installation. Do not assume that a run directory or report value from another machine will match yours.

---

## Section 1 — Open-source EDA, OpenLANE and Sky130

### Theory

#### Package, die, core and pads

- **Package:** Protective enclosure and external connection interface for a chip.
- **Die:** The manufactured silicon containing the chip circuitry.
- **Core:** Region in which the main digital logic is placed.
- **Pads:** Interfaces that connect signals and power between the core and the outside world.
- **Foundry:** Facility that manufactures semiconductor devices.
- **PDK:** Process Design Kit containing process rules, device models, libraries and technology information.
- **Standard-cell library:** Collection of reusable logic cells with characterised timing and physical views.

#### From software to silicon

A software program is compiled into machine instructions for a target instruction-set architecture (ISA). Hardware described in RTL implements that architecture. Synthesis maps RTL into standard cells; physical-design stages arrange and connect those cells to produce the layout.

### Implementation

The workshop uses the `picorv32a` design as the example design.

#### 1. Start OpenLANE

Run these commands from the OpenLANE working directory in the workshop environment:

```bash
cd ~/Desktop/work/tools/openlane_working_dir/openlane
docker
```

Inside the OpenLANE container:

```tcl
./flow.tcl -interactive
package require openlane 0.9
prep -design picorv32a
run_synthesis
```

When finished, exit the interactive flow and then the container shell:

```tcl
exit
```

```bash
exit
```

### Screenshot — OpenLANE Setup and Synthesis

<!-- Add screenshot(s) here -->
<!-- ![OpenLANE setup](images/section-1-openlane-setup.png) -->
<!-- ![Synthesis output](images/section-1-synthesis.png) -->

#### 2. Calculate the flop ratio

Use the values from **your own synthesis statistics report**.

\[
\text{Flop Ratio} =
\frac{\text{Number of D Flip-Flops}}{\text{Total Number of Cells}}
\]

\[
\text{DFF Percentage} = \text{Flop Ratio} \times 100\%
\]

- Number of D flip-flops: **[enter value from report]**
- Total number of cells: **[enter value from report]**
- Flop ratio: **[calculate]**
- DFF percentage: **[calculate]**

### Screenshot — Synthesis Statistics and Calculation

<!-- Add screenshot(s) here -->
<!-- ![Synthesis statistics](images/section-1-synthesis-statistics.png) -->

---

## Section 2 — Floorplanning and Placement

### Theory

Floorplanning establishes the chip/core dimensions and I/O locations. Placement assigns legal locations to standard cells. A good floorplan balances area, routability, timing and power considerations.

The die area can be calculated from the die dimensions in the DEF file:

\[
\text{Die Area} = \text{Die Width} \times \text{Die Height}
\]

Check the DEF database units before converting dimensions to microns.

### Implementation

#### 1. Run floorplanning

In the OpenLANE interactive session, after preparing the design:

```tcl
run_floorplan
```

### Screenshot — Floorplan Run

<!-- Add screenshot here -->
<!-- ![Floorplan run](images/section-2-floorplan-run.png) -->

#### 2. Inspect die dimensions and calculate area

Open the generated floorplan DEF and record the die coordinates. If the DEF uses 1000 database units per micron, convert each dimension as follows:

\[
\text{Dimension in }\mu m =
\frac{\text{Dimension in database units}}{1000}
\]

\[
\text{Die Area in }\mu m^2 =
\text{Width in }\mu m \times \text{Height in }\mu m
\]

- Die width: **[calculate from your DEF]**
- Die height: **[calculate from your DEF]**
- Die area: **[calculate]**

### Screenshot — DEF Dimensions and Area Calculation

<!-- Add screenshot(s) here -->
<!-- ![DEF dimensions](images/section-2-def-dimensions.png) -->

#### 3. Load the floorplan in Magic

Change to the directory containing the generated floorplan DEF. Adjust the run tag and paths to match your installation.

```bash
cd ~/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/<RUN_TAG>/results/floorplan/
magic -T ~/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech \
  lef read ../../tmp/merged.lef def read picorv32a.floorplan.def &
```

### Screenshot — Floorplan in Magic

<!-- Add screenshot(s) here -->
<!-- ![Floorplan in Magic](images/section-2-floorplan-magic.png) -->
<!-- ![I/O pins and tap cells](images/section-2-io-tap-cells.png) -->

#### 4. Run placement

In the OpenLANE interactive session:

```tcl
run_placement
```

### Screenshot — Placement Run

<!-- Add screenshot here -->
<!-- ![Placement run](images/section-2-placement-run.png) -->

#### 5. Load the placement DEF in Magic

```bash
cd ~/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/<RUN_TAG>/results/placement/
magic -T ~/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech \
  lef read ../../tmp/merged.lef def read picorv32a.placement.def &
```

### Screenshot — Placement in Magic

<!-- Add screenshot(s) here -->
<!-- ![Placement in Magic](images/section-2-placement-magic.png) -->

---

## Section 3 — Standard-cell Layout and Characterisation

### Theory

This section explores a custom inverter standard cell, layout verification, parasitic extraction and post-layout SPICE simulation. Layout geometry and connectivity must satisfy the technology rules and the cell must be compatible with the target standard-cell library.

### Implementation

#### 1. Clone the custom standard-cell design

```bash
cd ~/Desktop/work/tools/openlane_working_dir/openlane
git clone https://github.com/nickson-jose/vsdstdcelldesign
cd vsdstdcelldesign
cp ~/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech .
ls
magic -T sky130A.tech sky130_inv.mag &
```

### Screenshot — Repository and Inverter Layout

<!-- Add screenshot(s) here -->
<!-- ![Inverter layout](images/section-3-inverter-layout.png) -->

#### 2. Explore the layout

Inspect the PMOS and NMOS devices and verify the connectivity of the output, supply and ground pins. Record screenshots of relevant layers and any DRC investigation performed.

### Screenshot — Device and Connectivity Checks

<!-- Add screenshot(s) here -->
<!-- ![PMOS and NMOS](images/section-3-transistors.png) -->
<!-- ![Connectivity checks](images/section-3-connectivity.png) -->

#### 3. Extract the SPICE netlist in Magic

Run the following commands in the Magic Tcl console:

```tcl
extract all
ext2spice cthresh 0 rthresh 0
ext2spice
```

### Screenshot — Extraction Commands and SPICE File

<!-- Add screenshot(s) here -->
<!-- ![Extraction commands](images/section-3-extraction.png) -->
<!-- ![Generated SPICE file](images/section-3-spice-file.png) -->

#### 4. Simulate with ngspice

After reviewing and updating the extracted SPICE file as required for the lab:

```bash
ngspice sky130_inv.spice
```

In the ngspice prompt, plot the output waveform using the node names present in your netlist. For the workshop example:

```text
plot y vs time a
```

### Screenshot — Simulation and Waveform

<!-- Add screenshot(s) here -->
<!-- ![ngspice simulation](images/section-3-ngspice.png) -->
<!-- ![Output waveform](images/section-3-waveform.png) -->

#### 5. DRC rule investigation

The workshop also examines a Magic technology-file rule and the corresponding Sky130 periphery documentation.

Reference: [SkyWater Sky130 periphery rules](https://skywater-pdk.readthedocs.io/en/main/rules/periphery.html)

Example lab setup commands:

```bash
cd
wget http://opencircuitdesign.com/open_pdks/archive/drc_tests.tgz
tar xfz drc_tests.tgz
cd drc_tests
ls -al
gvim .magicrc
magic -d XR &
```

### Screenshot — DRC Investigation

<!-- Add screenshot(s) here -->
<!-- ![DRC setup](images/section-3-drc-setup.png) -->
<!-- ![Rule investigation](images/section-3-drc-rule.png) -->

---

## Section 4 — Timing Analysis and Custom-cell Integration

### Theory

A custom standard cell must align with routing tracks, have legal dimensions, and provide compatible LEF and Liberty data. Timing analysis helps identify setup/hold problems and paths affected by cell delay, fanout and interconnect.

### Implementation

#### 1. Open the custom inverter and inspect routing tracks

```bash
cd ~/Desktop/work/tools/openlane_working_dir/openlane/vsdstdcelldesign
magic -T sky130A.tech sky130_inv.mag &
```

In Magic's Tcl console, inspect the grid command and set the lab's routing-track grid as appropriate:

```tcl
help grid
grid 0.46um 0.34um 0.23um 0.17um
```

> Use these grid values only if they match the technology/library files supplied with your workshop environment.

### Screenshot — Track Alignment

<!-- Add screenshot(s) here -->
<!-- ![Track alignment](images/section-4-track-alignment.png) -->

#### 2. Save the layout and generate LEF

In Magic's Tcl console:

```tcl
save sky130_vsdinv.mag
lef write
```

### Screenshot — Final Layout and LEF

<!-- Add screenshot(s) here -->
<!-- ![Final inverter layout](images/section-4-final-layout.png) -->
<!-- ![Generated LEF](images/section-4-lef.png) -->

#### 3. Copy the LEF and library files into the design source directory

```bash
cp sky130_vsdinv.lef ~/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/src/
cp libs/sky130_fd_sc_hd__* ~/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/src/
ls ~/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/src/
```

Confirm that the copied files exist and that the paths match your environment.

### Screenshot — Design Source Files

<!-- Add screenshot here -->
<!-- ![Design source files](images/section-4-source-files.png) -->

#### 4. Configure and run synthesis with the custom cell

Start the OpenLANE flow and prepare a fresh run tag. The tag below is an example; use a unique tag for your own run.

```tcl
prep -design picorv32a -tag <YOUR_RUN_TAG> -overwrite
set lefs [glob $::env(DESIGN_DIR)/src/*.lef]
add_lefs -src $lefs
set ::env(SYNTH_SIZING) 1
set ::env(SYNTH_MAX_FANOUT) 4
echo $::env(SYNTH_DRIVING_CELL)
run_synthesis
```

Continue with floorplanning and placement if synthesis succeeds:

```tcl
run_floorplan
run_placement
```

### Screenshot — Custom-cell Synthesis and Placement

<!-- Add screenshot(s) here -->
<!-- ![Custom-cell synthesis](images/section-4-custom-synthesis.png) -->
<!-- ![Custom-cell placement](images/section-4-custom-placement.png) -->

#### 5. Run pre-layout timing analysis

From the OpenLANE working directory, invoke OpenSTA with the configuration used by the workshop:

```bash
cd ~/Desktop/work/tools/openlane_working_dir/openlane
sta pre_sta.conf
```

### Screenshot — OpenSTA Timing Report

<!-- Add screenshot(s) here -->
<!-- ![OpenSTA invocation](images/section-4-opensta.png) -->
<!-- ![Timing report](images/section-4-timing-report.png) -->

> Timing commands and configuration files may need to be adjusted to the specific flow version and run outputs.

---

## Section 5 — RTL-to-GDSII: PDN, Routing and Timing

### Theory

- **Clock-tree synthesis (CTS):** Builds a clock distribution network and manages clock arrival differences.
- **Power Distribution Network (PDN):** Distributes power and ground through the design.
- **Routing:** Connects placed cells and pins using metal interconnect.
- **Parasitic extraction:** Estimates resistance and capacitance from the routed layout.
- **STA:** Checks timing against clock and input/output constraints.

### Implementation

#### 1. Run the flow through CTS and generate the PDN

In the OpenLANE interactive session, after preparing the design and completing the earlier stages:

```tcl
run_synthesis
run_floorplan
run_placement
```

If the workshop setup requires it and the environment reports a CTS library-variable issue, follow the lab instructions for that specific version. Then run:

```tcl
run_cts
gen_pdn
```

### Screenshot — CTS and PDN

<!-- Add screenshot(s) here -->
<!-- ![CTS run](images/section-5-cts.png) -->
<!-- ![PDN generation](images/section-5-pdn.png) -->

#### 2. Inspect the PDN layout in Magic

Use the actual run tag and DEF path produced by your run. For example:

```bash
cd ~/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/<RUN_TAG>/tmp/floorplan/
```

Open the appropriate PDN DEF using the Sky130 Magic technology file and the merged LEF for that run. The exact DEF filename and relative path can vary by OpenLANE version.

### Screenshot — PDN in Magic

<!-- Add screenshot(s) here -->
<!-- ![PDN layout](images/section-5-pdn-magic.png) -->

#### 3. Perform detailed routing

Use the routing procedure provided by the installed OpenLANE version. In standard flow versions, routing is launched through the flow rather than by assuming a standalone script path. If your workshop specifically provides a TritonRoute script, run it with the exact LEF and DEF paths from your run directory.

### Screenshot — Detailed Routing

<!-- Add screenshot(s) here -->
<!-- ![Routing output](images/section-5-routing.png) -->
<!-- ![Routed layout](images/section-5-routed-layout.png) -->

#### 4. Post-route timing analysis

The following is an example OpenROAD/OpenSTA command sequence. Replace all run paths, netlist names, libraries and SDC paths with files actually generated by your flow.

```tcl
read_lef /openLANE_flow/designs/picorv32a/runs/<RUN_TAG>/tmp/merged.lef
read_def /openLANE_flow/designs/picorv32a/runs/<RUN_TAG>/results/routing/picorv32a.def
write_db pico_route.db
read_db pico_route.db
read_verilog /openLANE_flow/designs/picorv32a/runs/<RUN_TAG>/results/synthesis/picorv32a.synthesis_preroute.v
read_liberty $::env(LIB_SYNTH_COMPLETE)
link_design picorv32a
read_sdc /openLANE_flow/designs/picorv32a/src/my_base.sdc
set_propagated_clock [all_clocks]
read_spef /openLANE_flow/designs/picorv32a/runs/<RUN_TAG>/results/routing/picorv32a.spef
report_checks -path_delay min_max -fields {slew trans net cap input_pins} -format full_clock_expanded -digits 4
```

### Screenshot — Post-route STA

<!-- Add screenshot(s) here -->
<!-- ![Post-route timing analysis](images/section-5-post-route-sta.png) -->
<!-- ![Final timing report](images/section-5-final-timing-report.png) -->

---

## Key Concepts

| Concept | Purpose |
|---|---|
| RTL | Describes the digital hardware behaviour and structure |
| Synthesis | Converts RTL into a gate-level netlist |
| Floorplanning | Defines core/die planning and I/O arrangement |
| Placement | Assigns physical locations to standard cells |
| CTS | Builds the clock distribution network |
| PDN | Distributes power and ground |
| Routing | Connects pins and cells with metal interconnect |
| DRC | Checks layout geometry against manufacturing rules |
| LVS | Checks consistency between layout and schematic/netlist |
| STA | Checks whether timing constraints are met |
| LEF | Abstract physical view used by place-and-route tools |
| Liberty | Timing and power models for cells |
| DEF | Physical design data such as placement and routing |
| SPEF | Extracted parasitic resistance and capacitance data |

## Certificate

<!-- Add your workshop certificate screenshot or link here -->
<!-- ![Workshop Certificate](images/certificate.png) -->

## Acknowledgements

Thanks to the workshop organisers and the open-source EDA community for the learning material and tools.

- [VLSI System Design (VSD)](https://www.vlsisystemdesign.com/)
- [SkyWater SKY130 PDK](https://github.com/google/skywater-pdk)
- [OpenLANE](https://github.com/The-OpenROAD-Project/OpenLane)
- [Reference repository](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd)

## Resources

- [Reference workshop repository](https://github.com/fayizferosh/soc-design-and-planning-nasscom-vsd)
- [Sky130 documentation](https://skywater-pdk.readthedocs.io/en/main/)
- [OpenLANE documentation](https://openlane.readthedocs.io/)

---

**Before publishing:** replace `<RUN_TAG>` and other placeholders, verify commands against your installed workshop version, add screenshots under the matching headings, and update the certificate section if applicable.
