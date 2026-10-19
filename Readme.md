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
<img width="1223" height="712" alt="Screenshot 2026-10-17 082831" src="https://github.com/user-attachments/assets/214b8480-bf94-4f84-910d-8ce14dc22612" />

<img width="1212" height="802" alt="Screenshot 2026-10-17 084319" src="https://github.com/user-attachments/assets/fb5cb5d2-d61a-4998-8c61-6032ef252081" />


#### 2. Calculate the flop ratio

Use the values from **your own synthesis statistics report**.

\[
\text{Flop Ratio} =
\frac{\text{Number of D Flip-Flop}}{\text{Total Number of Cells}}
\]

\[
\text{DFF Percentage} = \text{Flop Ratio} \times 100\%
\]

- Number of D flip-flops: **1613**
- Total number of cells: **14876**
- Flop ratio: **0.108429685**
- DFF percentage: **10.8429685 %**

### Screenshot — Synthesis Statistics and Calculation


<img width="812" height="575" alt="Screenshot 2026-10-17 085404" src="https://github.com/user-attachments/assets/44a7eb38-d168-472c-a454-8ac68d00e635" />

<img width="842" height="377" alt="Screenshot 2026-10-17 085428" src="https://github.com/user-attachments/assets/830705ae-3922-4e38-ae1e-50c8e57a705f" />

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
<img width="855" height="707" alt="Screenshot 2026-10-18 234801" src="https://github.com/user-attachments/assets/7253027c-b4c4-462d-8102-6ed83332a601" />

<img width="886" height="827" alt="Screenshot 2026-10-18 234858" src="https://github.com/user-attachments/assets/bd042ef7-09d0-4119-9fab-835a3a69b560" />


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
<img width="885" height="792" alt="Screenshot 2026-10-19 000541" src="https://github.com/user-attachments/assets/6681cb21-1d5c-4389-a4f6-109a6d907d2e" />


#### 3. Load the floorplan in Magic

Change to the directory containing the generated floorplan DEF. Adjust the run tag and paths to match your installation.

```bash
cd ~/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/<RUN_TAG>/results/floorplan/
magic -T ~/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech \
  lef read ../../tmp/merged.lef def read picorv32a.floorplan.def &
```

### Screenshot — Floorplan in Magic

<!-- Add screenshot(s) here -->
<img width="1211" height="917" alt="Screenshot 2026-10-19 001449" src="https://github.com/user-attachments/assets/23bb3b8e-f3bd-4dae-9572-237c57fe0e29" />

<img width="1147" height="857" alt="Screenshot 2026-10-19 001843" src="https://github.com/user-attachments/assets/f8330bdf-bdf9-4803-824c-ac079eee48e9" />

<img width="1126" height="850" alt="Screenshot 2026-10-19 002307" src="https://github.com/user-attachments/assets/a4adb45a-05b2-4e81-8487-3843a0d1e95a" />

<img width="1073" height="867" alt="Screenshot 2026-10-19 002527" src="https://github.com/user-attachments/assets/2a9bb98a-1eeb-4e14-93ff-5c2f1f60def9" />

<img width="1137" height="863" alt="Screenshot 2026-10-19 002827" src="https://github.com/user-attachments/assets/cdba092d-562d-4f36-b175-201211d4cf4c" />

<img width="1138" height="861" alt="Screenshot 2026-10-19 003315" src="https://github.com/user-attachments/assets/b52b5f84-1357-4743-ab15-ef7c3b6c580c" />

#### 4. Run placement

In the OpenLANE interactive session:

```tcl
run_placement
```

### Screenshot — Placement Run

<!-- Add screenshot here -->
<img width="887" height="837" alt="Screenshot 2026-10-19 083808" src="https://github.com/user-attachments/assets/a4837457-a972-4140-b679-90ca87e15280" />

<img width="878" height="847" alt="Screenshot 2026-10-19 084034" src="https://github.com/user-attachments/assets/3104734c-de9c-4004-8d69-ab6daf695620" />


#### 5. Load the placement DEF in Magic

```bash
cd ~/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/<RUN_TAG>/results/placement/
magic -T ~/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech \
  lef read ../../tmp/merged.lef def read picorv32a.placement.def &
```

### Screenshot — Placement in Magic

<!-- Add screenshot(s) here -->
<img width="895" height="865" alt="Screenshot 2026-10-19 084309" src="https://github.com/user-attachments/assets/7c908852-b2dc-4fbe-bbb0-94caaf94b03e" />

<img width="888" height="832" alt="Screenshot 2026-10-19 084813" src="https://github.com/user-attachments/assets/e0148771-60dd-4d15-bbf1-7ed49089a6f7" />

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
<img width="792" height="562" alt="Screenshot 2026-10-19 130317" src="https://github.com/user-attachments/assets/bab55454-27aa-4aa1-9dff-e08f6c0eeba6" />


<img width="657" height="721" alt="Screenshot 2026-10-19 130346" src="https://github.com/user-attachments/assets/53b33154-1648-4896-9155-9227930d62a6" />


#### 2. Explore the layout

Inspect the PMOS and NMOS devices and verify the connectivity of the output, supply and ground pins. Record screenshots of relevant layers and any DRC investigation performed.

### Screenshot — Device and Connectivity Checks

<!-- Add screenshot(s) here -->

<img width="800" height="845" alt="Screenshot 2026-10-19 130444" src="https://github.com/user-attachments/assets/ce32b407-b8e9-45f7-b660-cab36aedba2b" />


<img width="808" height="875" alt="Screenshot 2026-10-19 130502" src="https://github.com/user-attachments/assets/67f0bfe9-2328-4643-a56c-13b2dbb45e51" />


<img width="807" height="886" alt="Screenshot 2026-10-19 130553" src="https://github.com/user-attachments/assets/112cc9ee-739f-497d-9542-e837bf92eafa" />


<img width="802" height="856" alt="Screenshot 2026-10-19 130631" src="https://github.com/user-attachments/assets/880193fd-70b8-4b21-bb37-b08a4e0c0b92" />


<img width="807" height="877" alt="Screenshot 2026-10-19 130645" src="https://github.com/user-attachments/assets/ce3623db-2dc6-4f72-b6e1-21b3c9115922" />


<img width="917" height="922" alt="Screenshot 2026-10-19 143016" src="https://github.com/user-attachments/assets/4990733c-8413-447b-84f8-323de159b79d" />

#### 3. Extract the SPICE netlist in Magic

Run the following commands in the Magic Tcl console:

```tcl
extract all
ext2spice cthresh 0 rthresh 0
ext2spice
```

### Screenshot — Extraction Commands and SPICE File

<!-- Add screenshot(s) here -->

<img width="868" height="851" alt="Screenshot 2026-10-19 144000" src="https://github.com/user-attachments/assets/62df2989-9ea5-4081-bd82-d92b43717b62" />

<img width="862" height="621" alt="Screenshot 2026-10-19 144129" src="https://github.com/user-attachments/assets/22f30272-8a51-4722-a530-33c22678c47e" />


<img width="857" height="832" alt="Screenshot 2026-10-19 150816" src="https://github.com/user-attachments/assets/1f71aa69-b085-44a5-b4bf-a3b0088d4d64" />

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

<img width="863" height="800" alt="Screenshot 2026-10-19 151255" src="https://github.com/user-attachments/assets/03c1867d-90fe-4a50-827c-098640ac2cfb" />

<img width="1917" height="992" alt="Screenshot 2026-10-19 153002" src="https://github.com/user-attachments/assets/347516f4-2567-4d8e-aa24-b8e2abf75d53" />


<img width="1628" height="682" alt="Screenshot 2026-10-19 153016" src="https://github.com/user-attachments/assets/9ea61a08-6be4-489c-9fb8-bc09e941053c" />


<img width="1801" height="868" alt="Screenshot 2026-10-19 153422" src="https://github.com/user-attachments/assets/47e18521-1790-4fe0-ba18-461ec7b5ce24" />


<img width="1067" height="853" alt="Screenshot 2026-10-19 153243" src="https://github.com/user-attachments/assets/368c59ef-300c-45aa-9f26-5145fc5310aa" />


<img width="1848" height="856" alt="Screenshot 2026-10-19 153635" src="https://github.com/user-attachments/assets/e4e52cc9-4289-4547-8c07-b083cb59c338" />


<img width="532" height="238" alt="Screenshot 2026-10-19 153653" src="https://github.com/user-attachments/assets/7b986694-3b65-4898-ba8c-b75c64aaa1b1" />


<img width="1822" height="856" alt="Screenshot 2026-10-19 153745" src="https://github.com/user-attachments/assets/64335385-c2ac-413a-84ce-e8d7ac495abb" />


<img width="350" height="227" alt="Screenshot 2026-10-19 153752" src="https://github.com/user-attachments/assets/bde41ede-99cd-4440-be1f-ff3728b6f4ba" />


<img width="1785" height="862" alt="Screenshot 2026-10-19 153950" src="https://github.com/user-attachments/assets/03066581-3e9d-45d8-856f-d7fcebf0d005" />


<img width="301" height="96" alt="Screenshot 2026-10-19 154010" src="https://github.com/user-attachments/assets/998f6ea8-548b-4ee0-b94c-1e833c656f20" />


<img width="1801" height="840" alt="Screenshot 2026-10-19 154048" src="https://github.com/user-attachments/assets/9e381885-4031-46f2-b992-ad7f069dca6b" />

<img width="668" height="476" alt="Screenshot 2026-10-19 154258" src="https://github.com/user-attachments/assets/4d3a5c60-768d-4ad6-8dfe-46c500f85dfa" />

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

<img width="1072" height="777" alt="Screenshot 2026-10-19 154855" src="https://github.com/user-attachments/assets/65b05571-98b6-48fd-b9d9-d821c3067bdd" />

<img width="913" height="802" alt="Screenshot 2026-10-19 155021" src="https://github.com/user-attachments/assets/cf7597a8-1d62-41b3-8397-0e4f8555beef" />

<img width="1471" height="845" alt="Screenshot 2026-10-19 163353" src="https://github.com/user-attachments/assets/faec0f08-4be8-4870-a875-f6322dd12de4" />


<img width="896" height="841" alt="Screenshot 2026-10-19 164046" src="https://github.com/user-attachments/assets/7922fe2f-1c5c-4812-8b8b-f2c76f2628af" />


<img width="885" height="835" alt="Screenshot 2026-10-19 165815" src="https://github.com/user-attachments/assets/b0ece9c7-0aeb-4a96-a9f9-f1fde3152842" />


<img width="880" height="787" alt="Screenshot 2026-10-19 170038" src="https://github.com/user-attachments/assets/4ea60ab2-cf6c-4a2a-80ad-ee68103da116" />


<img width="866" height="812" alt="Screenshot 2026-10-19 170304" src="https://github.com/user-attachments/assets/d6f43c33-eccc-4c84-9bf3-9c1c9a49b1d0" />

<img width="900" height="876" alt="Screenshot 2026-10-19 174626" src="https://github.com/user-attachments/assets/e75c5fc8-d433-4fbe-b6dc-0f3d6f5adcb5" />


<img width="1568" height="915" alt="Screenshot 2026-10-19 175041" src="https://github.com/user-attachments/assets/ff18713e-36f3-4472-b6f5-e6e6d2148fa9" />


<img width="892" height="851" alt="Screenshot 2026-10-19 175302" src="https://github.com/user-attachments/assets/4679a4a1-a0fd-456f-82cb-88ba1557087a" />


<img width="827" height="537" alt="Screenshot 2026-10-19 181232" src="https://github.com/user-attachments/assets/36aa0cb6-2808-4e66-af29-6bd006d214d0" />


<img width="897" height="845" alt="Screenshot 2026-10-19 181321" src="https://github.com/user-attachments/assets/7090b7f1-7ac0-4788-b9a0-89d7bf751858" />


<img width="1143" height="780" alt="Screenshot 2026-10-19 181350" src="https://github.com/user-attachments/assets/186c9fec-6006-4139-b465-b9427c0f11bc" />


<img width="532" height="460" alt="Screenshot 2026-10-19 181503" src="https://github.com/user-attachments/assets/e4067f64-0e75-496f-8034-bcd8eb33a00e" />

<img width="873" height="793" alt="Screenshot 2026-10-19 181746" src="https://github.com/user-attachments/assets/64e30040-d7f3-4476-be5f-46b62e9d47e8" />

<img width="893" height="792" alt="Screenshot 2026-10-19 182026" src="https://github.com/user-attachments/assets/641746dc-e3fb-414f-a37e-17781c656cdc" />

<img width="607" height="70" alt="Screenshot 2026-10-19 182208" src="https://github.com/user-attachments/assets/523da3b6-7c3b-4c89-b2c3-887693fb001a" />


<img width="880" height="818" alt="Screenshot 2026-10-19 182351" src="https://github.com/user-attachments/assets/b8546286-95b6-49b8-9166-c86df2a44a3d" />


<img width="891" height="872" alt="Screenshot 2026-10-19 182859" src="https://github.com/user-attachments/assets/eb8367a0-40c5-4a94-8d90-8a8dd019679b" />

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

<img width="891" height="872" alt="Screenshot 2026-10-19 182859" src="https://github.com/user-attachments/assets/d141ca74-3d8c-4bae-bab6-bd7a5935e1f9" />


<img width="1177" height="835" alt="Screenshot 2026-10-19 183137" src="https://github.com/user-attachments/assets/91326daa-06bb-494c-85d1-b2f80add0a91" />


<img width="1005" height="840" alt="Screenshot 2026-10-19 184027" src="https://github.com/user-attachments/assets/4f6930fc-71c3-4518-84c9-ea8435c77325" />


<img width="1207" height="860" alt="Screenshot 2026-10-19 184313" src="https://github.com/user-attachments/assets/e37fdf3e-8835-4e6a-a228-66d9e7d1b1a6" />


<img width="947" height="715" alt="Screenshot 2026-10-19 184445" src="https://github.com/user-attachments/assets/033517fc-5bd5-4944-9858-977d2cff193b" />


<img width="1187" height="856" alt="Screenshot 2026-10-19 184527" src="https://github.com/user-attachments/assets/77ac488d-1239-4fb7-8371-52886aea54d5" />

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

## Acknowledgements

Thanks to the workshop organisers and the open-source EDA community for the learning material and tools.

- [VLSI System Design (VSD)](https://www.vlsisystemdesign.com/)
- [SkyWater SKY130 PDK](https://github.com/google/skywater-pdk)
- [OpenLANE](https://github.com/The-OpenROAD-Project/OpenLane)

## Resources

- [Sky130 documentation](https://skywater-pdk.readthedocs.io/en/main/)
- [OpenLANE documentation](https://openlane.readthedocs.io/)

---

**Before publishing:** replace `<RUN_TAG>` and other placeholders, verify commands against your installed workshop version, add screenshots under the matching headings, and update the certificate section if applicable.
