# Digital VLSI SoC Design and Planning

![OS](https://img.shields.io/badge/OS-linux-orange)
![EDA Tools](https://img.shields.io/badge/EDA%20Tools-OpenLANE%2C%20Yosys%2C%20ABC%2C%20OpenROAD%2C%20TritonRoute%2C%20OpenSTA%2C%20Magic%2C%20Netgen%2C%20ngspice-navy)
![Languages](https://img.shields.io/badge/languages-Verilog%2C%20Bash%2C%20TCL-crimson)

> Two-week digital VLSI SoC design and planning workshop covering the complete RTL to GDSII flow, organised by VSD in collaboration with NASSCOM (Advanced Physical Design using OpenLANE / Sky130).

This repository documents my work for every section of the workshop: commands, screenshots and results.

## Table of Contents

- [Section 1 - Inception of open-source EDA, OpenLANE and Sky130 PDK](#section-1---inception-of-open-source-eda-openlane-and-sky130-pdk)
- [Section 2 - Good floorplan vs bad floorplan and introduction to library cells](#section-2---good-floorplan-vs-bad-floorplan-and-introduction-to-library-cells)
- [Section 3 - Design library cell using Magic layout and ngspice characterization](#section-3---design-library-cell-using-magic-layout-and-ngspice-characterization)
- [Section 4 - Pre-layout timing analysis and importance of good clock tree](#section-4---pre-layout-timing-analysis-and-importance-of-good-clock-tree)
- [Section 5 - Final steps for RTL to GDSII using TritonRoute and OpenSTA](#section-5---final-steps-for-rtl-to-gdsii-using-tritonroute-and-opensta)
- [Certificate](#certificate)
- [Acknowledgements](#acknowledgements)

---

## Section 1 - Inception of open-source EDA, OpenLANE and Sky130 PDK

**Dates:** DD/MM/YYYY - DD/MM/YYYY

### Theory

<details>
<summary><b>Expand or Collapse</b></summary>

<!-- Paste your lecture transcript and slide screenshots for Section 1 here -->

</details>

### Implementation

**Tasks**

1. Run `picorv32a` design synthesis using the OpenLANE flow and generate the outputs.
2. Calculate the flop ratio.

All Section 1 logs, reports and results are in this run folder:

[Section 1 Run - RUN_FOLDER_NAME](openlane/designs/picorv32a/runs/RUN_FOLDER_NAME)

#### 1. Run `picorv32a` synthesis using the OpenLANE flow

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

# Exit the OpenLANE flow, then the docker sub-system
exit
exit
```

Screenshots of the commands:

<img width="1216" height="437" alt="Screenshot 2026-10-17 083117" src="https://github.com/user-attachments/assets/45d383f8-f92b-41ed-8b18-13edb3113080" />


<img width="1225" height="527" alt="Screenshot 2026-10-17 083133" src="https://github.com/user-attachments/assets/5f1064ef-1fe6-44a7-9535-80132a370956" />

<img width="1212" height="802" alt="Screenshot 2026-10-17 084319" src="https://github.com/user-attachments/assets/c4dea539-8b89-42fa-931a-8aa13363a11d" />

#### 2. Calculate the flop ratio

Screenshots of the synthesis statistics report with the required values highlighted:

<img width="812" height="575" alt="Screenshot 2026-10-17 085404" src="https://github.com/user-attachments/assets/a883076e-6fb1-4ea8-98c4-c280b1d9c036" />

<img width="842" height="377" alt="Screenshot 2026-10-17 085428" src="https://github.com/user-attachments/assets/511fd51a-158e-4fa7-8720-7639cdfd5a95" />

$$Flop\ Ratio = \frac{Number\ of\ D\ Flip\ Flop}{Total\ Number\ of\ Cells}$$

$$Flop\ Ratio = \frac{16113}{14876} = 0.108429685$$

Percentage of DFFs = 0.108429685 × 100 = ** 10.8429685%**

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

All Section 2 logs, reports and results are in this run folder:

[Section 2 Run - RUN_FOLDER_NAME](openlane/designs/picorv32a/runs/RUN_FOLDER_NAME)

#### 1. Run floorplan using the OpenLANE flow

```bash
cd Desktop/work/tools/openlane_working_dir/openlane
docker
```

```tcl
./flow.tcl -interactive
package require openlane 0.9
prep -design picorv32a

# Synthesis must be done before floorplan
run_synthesis

# Run floorplan
run_floorplan
```

Screenshots of the floorplan run:

<img width="855" height="707" alt="Screenshot 2026-10-18 234801" src="https://github.com/user-attachments/assets/0ccde893-1c74-42ae-b859-2df9cd9342a0" />

<img width="886" height="827" alt="Screenshot 2026-10-18 234858" src="https://github.com/user-attachments/assets/ef68d575-d2d7-46ba-86b9-f65b0f59120f" />

#### 2. Calculate the die area in microns

Screenshot of the floorplan DEF contents (look at the `DIEAREA` line):

<img width="885" height="792" alt="Screenshot 2026-10-19 000541" src="https://github.com/user-attachments/assets/bb4dee9b-721d-41fd-881c-311bcb2d9d4a" />

In the DEF file, 1000 unit distance = 1 micron.

$$Die\ width\ (\mu microns) = \frac{660685}{1000} = 660.685$$

$$Die\ height\ (\mu microns) = \frac{671405}{1000} = 671.405$$

$$Die\ area = 660.685 \times 671.405 = 443587.212425\ \mu microns^2$$

> Replace the placeholders with the values from your own floorplan DEF.

#### 3. Load the floorplan DEF in Magic

Run this in a second terminal:

```bash
cd Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/RUN_FOLDER_NAME/results/floorplan/

magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.floorplan.def &
```

Screenshot of the floorplan in Magic:

<img width="1211" height="917" alt="Screenshot 2026-10-19 001449" src="https://github.com/user-attachments/assets/e198749c-80f1-4cd1-bd10-954bafeddacd" />

Equidistant placement of ports:

<img width="1147" height="857" alt="Screenshot 2026-10-19 001843" src="https://github.com/user-attachments/assets/825b628d-0545-40fa-8c69-1b18fc23ef7b" />

Port layer as set through `config.tcl`:

<img width="1126" height="850" alt="Screenshot 2026-10-19 002307" src="https://github.com/user-attachments/assets/b2607ee4-69fe-442b-961c-db3f97b9f920" />

<img width="1073" height="867" alt="Screenshot 2026-10-19 002527" src="https://github.com/user-attachments/assets/55dfa409-a238-4d8a-bb9e-47ea7f18cdda" />

Decap cells and tap cells:

<img width="1153" height="871" alt="Screenshot 2026-10-19 002748" src="https://github.com/user-attachments/assets/503add04-9b1b-43b4-9f95-115c7c5eb926" />

Diagonally equidistant tap cells:

<img width="1137" height="863" alt="Screenshot 2026-10-19 002827" src="https://github.com/user-attachments/assets/d7d68e7f-f35a-4e6c-809c-27c5067d62e9" />

Unplaced standard cells at the origin:

<img width="1138" height="861" alt="Screenshot 2026-10-19 003315" src="https://github.com/user-attachments/assets/0642113f-9b1a-45c6-a4a7-aa811434e7c3" />

#### 4. Run congestion-aware placement

```tcl
# Congestion-aware placement is the default
run_placement
```

Screenshots of the placement run:

<img width="887" height="837" alt="Screenshot 2026-10-19 083808" src="https://github.com/user-attachments/assets/f7e9556b-e895-4614-b5fa-9f8aee96a265" />

<img width="878" height="847" alt="Screenshot 2026-10-19 084034" src="https://github.com/user-attachments/assets/1ac50f5b-0503-4a7a-9709-2383183f75c5" />

#### 5. Load the placement DEF in Magic

```bash
cd Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/RUN_FOLDER_NAME/results/placement/

magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.placement.def &
```

Screenshot of the placement in Magic:

<img width="895" height="865" alt="Screenshot 2026-10-19 084309" src="https://github.com/user-attachments/assets/90a07920-435b-415f-8801-e9f44e74367c" />

Standard cells legally placed:

<img width="888" height="832" alt="Screenshot 2026-10-19 084813" src="https://github.com/user-attachments/assets/e2c4fccf-83b3-45ee-aeb3-54fa4ae71f17" />

```tcl
# Exit the OpenLANE flow, then the docker sub-system
exit
exit
```

---

## Section 3 - Design library cell using Magic layout and ngspice characterization

**Dates:** DD/MM/YYYY - DD/MM/YYYY

### Implementation

**Tasks**

1. Clone the custom inverter standard cell design from [vsdstdcelldesign](https://github.com/nickson-jose/vsdstdcelldesign).
2. Load the custom inverter layout in Magic and explore it.
3. Extract SPICE from the inverter layout in Magic.
4. Edit the SPICE model file for simulation.
5. Run post-layout ngspice simulations and characterize the inverter.
6. Find and fix the DRC problems in the old Magic tech file for the SkyWater process.

Files for tasks 1 to 5: [vsdstdcelldesign](openlane/vsdstdcelldesign)

Files for task 6: [drc_tests](drc_tests)

#### 1. Clone the custom inverter design

```bash
cd Desktop/work/tools/openlane_working_dir/openlane

git clone https://github.com/nickson-jose/vsdstdcelldesign
cd vsdstdcelldesign

# Copy the Magic tech file here for easy access
cp /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech .

# Check that everything is present
ls

# Open the custom inverter layout in Magic
magic -T sky130A.tech sky130_inv.mag &
```

Screenshot of the commands run:

<img width="792" height="562" alt="Screenshot 2026-10-19 130317" src="https://github.com/user-attachments/assets/89e49b66-243c-474e-bf75-7200d56dca69" />

#### 2. Load the inverter layout in Magic and explore it

Screenshot of the inverter layout:

<img width="657" height="721" alt="Screenshot 2026-10-19 130346" src="https://github.com/user-attachments/assets/561bb9a7-ec11-4c2c-8a0a-56055c0821db" />

NMOS and PMOS identified:

<img width="800" height="845" alt="Screenshot 2026-10-19 130444" src="https://github.com/user-attachments/assets/74fd5024-72b3-4847-b32f-d2b55bdb88b4" />

<img width="808" height="875" alt="Screenshot 2026-10-19 130502" src="https://github.com/user-attachments/assets/0a7a95a3-f256-4ec2-a94d-90ff01e1ac23" />

Output Y connected to the PMOS and NMOS drains:

<img width="807" height="886" alt="Screenshot 2026-10-19 130553" src="https://github.com/user-attachments/assets/6ef5493d-ea8a-4145-a8c9-cef5d1583850" />

PMOS source connected to VDD (VPWR here):

<img width="802" height="856" alt="Screenshot 2026-10-19 130631" src="https://github.com/user-attachments/assets/cd6df46e-3f92-44be-ba6f-c202be63c60e" />

NMOS source connected to VSS (VGND here):

<img width="807" height="877" alt="Screenshot 2026-10-19 130645" src="https://github.com/user-attachments/assets/f239292a-4c6d-462f-bd4a-481ab837b813" />

Removing part of the layout to trigger a DRC error:

<img width="917" height="922" alt="Screenshot 2026-10-19 143016" src="https://github.com/user-attachments/assets/ed5641cd-7c50-4a20-87bb-80ff0e70468b" />

#### 3. Extract SPICE from the layout

Commands to run in the Magic console (tkcon window):

```tcl
# Generate the extraction file
extract all

# Prepare the netlist export, then export it
ext2spice cthresh 0 rthresh 0
ext2spice
```

Screenshot of the tkcon window after the commands:

<img width="868" height="851" alt="Screenshot 2026-10-19 144000" src="https://github.com/user-attachments/assets/2ac8b678-2458-41eb-9727-054d45df620d" />

Screenshot of the generated SPICE file:

<img width="862" height="621" alt="Screenshot 2026-10-19 144129" src="https://github.com/user-attachments/assets/f047ac3c-29be-4ceb-be6e-3a84f2511de6" />

#### 4. Edit the SPICE file for simulation

Measuring the unit distance in the layout grid:

<img width="860" height="858" alt="Screenshot 2026-10-19 150045" src="https://github.com/user-attachments/assets/28341171-29ea-4726-9694-93209a45cb2e" />

Final edited SPICE file, ready for ngspice:

<img width="857" height="832" alt="Screenshot 2026-10-19 150816" src="https://github.com/user-attachments/assets/b2c92b45-1735-4d55-b0ac-58e924a967ec" />

#### 5. Post-layout ngspice simulation

Commands for the simulation:

```bash
ngspice sky130_inv.spice
```

```
# In the ngspice prompt
plot y vs time a
```

Screenshots of the ngspice run:

<img width="863" height="800" alt="Screenshot 2026-10-19 151255" src="https://github.com/user-attachments/assets/89cad228-7f18-420d-a0bd-f4c6db69ad9e" />

Screenshot of the generated plot:

<img width="862" height="875" alt="Screenshot 2026-10-19 151309" src="https://github.com/user-attachments/assets/e9b5a4aa-e936-4c43-9594-d7249217ef8d" />

**Rise transition time** (time from 20% to 80% of the output swing)

**20% of output = 660mV**

**80% of output = 2.64V**

20% point:

<img width="1917" height="992" alt="Screenshot 2026-10-19 153002" src="https://github.com/user-attachments/assets/9bf4e474-4e48-4ef6-aa36-32792232f474" />

<img width="1628" height="682" alt="Screenshot 2026-10-19 153016" src="https://github.com/user-attachments/assets/2859fee0-c716-496b-a316-1bf0e6fb104a" />

80% point:

<img width="1801" height="868" alt="Screenshot 2026-10-19 153422" src="https://github.com/user-attachments/assets/2fc45bc2-d56c-4399-8992-647ea02cb81b" />

<img width="1067" height="853" alt="Screenshot 2026-10-19 153243" src="https://github.com/user-attachments/assets/6c05760a-9c89-411f-b360-3b68f8aa52f8" />

Rise transition time = 2.24667 - 2.18242 = **0.06425**

**Fall transition time** (time from 80% to 20% of the output swing)

**80% of output = 2.64V**

**20% of output = 660mV**

80% point:

<img width="1822" height="856" alt="Screenshot 2026-10-19 153745" src="https://github.com/user-attachments/assets/65ac7e3d-3aea-4714-ba64-60327f44241e" />

<img width="350" height="227" alt="Screenshot 2026-10-19 153752" src="https://github.com/user-attachments/assets/e56d2a11-d8fb-476a-ace5-082ae1aa0a85" />

20% point:

<img width="1848" height="856" alt="Screenshot 2026-10-19 153635" src="https://github.com/user-attachments/assets/53b5c07e-1b48-40b2-9ae2-a2ba011b23bc" />

<img width="532" height="238" alt="Screenshot 2026-10-19 153653" src="https://github.com/user-attachments/assets/efc0adf3-fde9-4a8c-a520-2b5d75087920" />

Fall transition time = 4.09515 - 4.05288 = **0.04227**

**Rise cell delay** (output rising 50% minus input falling 50%)

**50% of 3.3V = 1.65V**

<img width="1785" height="862" alt="Screenshot 2026-10-19 153950" src="https://github.com/user-attachments/assets/9abc6f93-2a38-412d-8c9f-74c44dfd6f2e" />
<img width="301" height="96" alt="Screenshot 2026-10-19 154010" src="https://github.com/user-attachments/assets/27a175d5-e91c-4e3f-9f06-cb3179295b37" />

Rise cell delay = 2.21152 - 2.14992 = **0.0616**

**Fall cell delay** (output falling 50% minus input rising 50%)

<img width="1801" height="840" alt="Screenshot 2026-10-19 154048" src="https://github.com/user-attachments/assets/5a053ecb-0bda-46ea-ae9d-fa14b7d719f8" />
<img width="668" height="476" alt="Screenshot 2026-10-19 154258" src="https://github.com/user-attachments/assets/07eb8498-0e29-4836-910a-7a6a27d19d69" />

Fall cell delay = 4.07786 - 4.05 = **0.02786**

#### 6. Find and fix DRC problems in the old Magic tech file

Commands run:

```bash
# Download and unpack the DRC test layouts
wget http://opencircuitdesign.com/open_pdks/archive/drc_tests.tgz
tar xfz drc_tests.tgz
cd drc_tests

# Open Magic without the default start-up script
magic -d XR &
```

Screenshots of the commands run:

<img width="1072" height="777" alt="Screenshot 2026-10-19 154855" src="https://github.com/user-attachments/assets/7c1a0e90-ac2d-480a-9c76-5182fa226232" />

Screenshot of the `.magicrc` file:

<img width="913" height="802" alt="Screenshot 2026-10-19 155021" src="https://github.com/user-attachments/assets/38fb8d51-adff-4130-bdeb-76139487d4e1" />

##### Fix 1: `poly.9` (simple rule)

Screenshot of the poly rules in the tech file:

<img width="1471" height="845" alt="Screenshot 2026-10-19 163353" src="https://github.com/user-attachments/assets/d880b294-ead6-444f-888e-7a16acc9a788" />

The rule is wrongly implemented, so no DRC error appears even when the spacing is below the limit:

<img width="896" height="841" alt="Screenshot 2026-10-19 164046" src="https://github.com/user-attachments/assets/e6ac586d-75dc-48f5-8e0d-dd16aa54a819" />

<img width="876" height="835" alt="Screenshot 2026-10-19 174527" src="https://github.com/user-attachments/assets/414aa0af-a0fa-4914-97b7-754c792b09ba" />

New lines added to `sky130A.tech`:

<img width="885" height="835" alt="Screenshot 2026-10-19 165815" src="https://github.com/user-attachments/assets/a93c4ed0-d817-4086-b097-32b61a6e45ae" />

<img width="880" height="787" alt="Screenshot 2026-10-19 170038" src="https://github.com/user-attachments/assets/30d12a2c-f430-4551-bf2f-96d19632c540" />

Commands to run in the tkcon window:

```tcl
tech load sky130A.tech
drc style drc(full)
drc check
drc why
```

Magic window with the rule now working:

<img width="866" height="812" alt="Screenshot 2026-10-19 170304" src="https://github.com/user-attachments/assets/96b42a50-b6ed-4694-9a7c-fc4dd3c1329f" />

<img width="900" height="876" alt="Screenshot 2026-10-19 174626" src="https://github.com/user-attachments/assets/ba95dad8-5bbe-43d3-b76b-fffb64420b56" />

##### Fix 2: `difftap.2` (simple rule)

Screenshot of the difftap rules in the tech file:

<img width="1568" height="915" alt="Screenshot 2026-10-19 175041" src="https://github.com/user-attachments/assets/f41d5348-a60e-424b-a13f-27792158a111" />

The rule is wrongly implemented, so no DRC error appears even when the spacing is below the limit:

<img width="892" height="851" alt="Screenshot 2026-10-19 175302" src="https://github.com/user-attachments/assets/fcffacb6-7ced-44ed-b0ae-e3300a0e7df5" />

New lines added to `sky130A.tech`:

<img width="897" height="845" alt="Screenshot 2026-10-19 181321" src="https://github.com/user-attachments/assets/5d8ea739-d954-4cd3-b532-0f19a8847cd5" />

Commands to run in the tkcon window:

```tcl
tech load sky130A.tech
drc style drc(full)
drc check
drc why
```

Magic window with the rule now working:

<img width="897" height="845" alt="Screenshot 2026-10-19 181321" src="https://github.com/user-attachments/assets/5d45a9c5-8ba9-4734-9b0e-e6ab80cc7812" />

##### Fix 3: `nwell.4` (complex rule)

Screenshot of the nwell rules in the tech file:

<img width="1143" height="780" alt="Screenshot 2026-10-19 181350" src="https://github.com/user-attachments/assets/30a1e3d7-f64a-40a7-9c6c-e541fa501715" />

The rule is wrongly implemented, so no DRC error appears even when the nwell has no tap:

<img width="532" height="460" alt="Screenshot 2026-10-19 181503" src="https://github.com/user-attachments/assets/ed71648c-1505-48dd-920a-56c9f66d9a6e" />

New lines added to `sky130A.tech`:

<img width="873" height="793" alt="Screenshot 2026-10-19 181746" src="https://github.com/user-attachments/assets/76ac8607-8a45-496f-8525-acd651deaac4" />

<img width="607" height="70" alt="Screenshot 2026-10-19 182208" src="https://github.com/user-attachments/assets/87b2afe6-4227-46a9-b2e7-3946498be3fc" />

Commands to run in the tkcon window:

```tcl
tech load sky130A.tech
drc style drc(full)
drc check
drc why
```

Magic window with the rule now working:

<img width="880" height="818" alt="Screenshot 2026-10-19 182351" src="https://github.com/user-attachments/assets/c9a7480d-c9b4-42f6-ac64-c4662e6a30a5" />

---

## Section 4 - Pre-layout timing analysis and importance of good clock tree

**Dates:** DD/MM/YYYY - DD/MM/YYYY

### Implementation

**Tasks**

1. Fix small DRC errors and verify the design is ready to be inserted into the flow.
2. Save the finalized layout with a custom name and open it.
3. Generate the LEF from the layout.
4. Copy the new LEF and the required library files into the `picorv32a` design `src` directory.
5. Edit `config.tcl` to use the new libraries and add the extra LEF to the OpenLANE flow.
6. Run OpenLANE synthesis with the newly inserted custom inverter cell.
7. Reduce or remove the violations introduced by the custom cell by modifying design parameters.
8. Run floorplan and placement and verify the cell is accepted in the PnR flow.
9. Do post-synthesis timing analysis with OpenSTA.
10. Make timing ECO fixes to remove all violations.
11. Replace the old netlist with the ECO netlist and run floorplan, placement and CTS.
12. Do post-CTS timing analysis with OpenROAD.
13. Explore the post-CTS analysis after removing a cell from `CTS_CLK_BUFFER_LIST`.

Files for tasks 1 to 3: [vsdstdcelldesign](openlane/vsdstdcelldesign)

Files for tasks 4 and 5: [picorv32a src](openlane/designs/picorv32a/src)

Logs, reports and results for the run-based tasks: [Section 4 Run - RUN_FOLDER_NAME](openlane/designs/picorv32a/runs/RUN_FOLDER_NAME)

#### 1. Fix small DRC errors and verify the cell is ready for the flow

Conditions to verify before inserting a custom cell into the flow:

1. The input and output ports must lie on the intersection of the vertical and horizontal tracks.
2. The cell width must be an odd multiple of the horizontal track pitch.
3. The cell height must be an even multiple of the vertical track pitch.

Commands to open the custom inverter layout:

```bash
cd Desktop/work/tools/openlane_working_dir/openlane/vsdstdcelldesign
magic -T sky130A.tech sky130_inv.mag &
```

Screenshot of `tracks.info` from `sky130_fd_sc_hd`:

<img width="891" height="872" alt="Screenshot 2026-10-19 182859" src="https://github.com/user-attachments/assets/21ae9a0f-389a-4c25-aedb-25d28c29704b" />

Commands for the tkcon window to set the grid to the `li1` tracks:

```tcl
# Get syntax for grid command
help grid

# Set grid values accordingly
grid 0.46um 0.34um 0.23um 0.17um
```

Screenshot of the commands run:

<img width="886" height="826" alt="Screenshot 2026-10-19 183004" src="https://github.com/user-attachments/assets/89a6f472-f623-4817-931f-f5b340aac971" />

Condition 1 verified:

<img width="1177" height="835" alt="Screenshot 2026-10-19 183137" src="https://github.com/user-attachments/assets/765f4d93-88da-4e2c-97de-9b9ca175fd50" />

Condition 2 verified:

**horizontal track pitch = 0.46um**
<img width="1005" height="840" alt="Screenshot 2026-10-19 184027" src="https://github.com/user-attachments/assets/d129c524-dfe4-498b-9461-f1d4fffceb70" />
**width of standard cell = 1.38um = 0.46 * 3**


Condition 3 verified:

**vertical track pitch = 0.34um**
<img width="1207" height="860" alt="Screenshot 2026-10-19 184313" src="https://github.com/user-attachments/assets/be0c8eb2-1dca-4c6d-a2cf-59b27cc09e22" />
**height of standard cell = 2.72um = 0.34 * 8*

#### 2. Save the layout with a custom name

Command for the tkcon window:

```tcl
save sky130_vsdinv.mag
```

Command to open the saved layout:

```bash
magic -T sky130A.tech sky130_vsdinv.mag &
```

Screenshot of the newly saved layout:

<img width="947" height="715" alt="Screenshot 2026-10-19 184445" src="https://github.com/user-attachments/assets/31dde440-35b6-4afd-8dce-73bf0bbdf93c" />

#### 3. Generate the LEF from the layout

Command for the tkcon window:

```tcl
lef write
```

Screenshot of the command run:

<img width="1187" height="856" alt="Screenshot 2026-10-19 184527" src="https://github.com/user-attachments/assets/50acaa7c-4dd4-448b-8c3c-dbc0fc1e012d" />

Screenshot of the newly created LEF file:

<img width="1182" height="877" alt="Screenshot 2026-10-19 184715" src="https://github.com/user-attachments/assets/f2460e85-4d42-46a8-aa7d-23b26551a477" />

#### 4. Copy the LEF and library files into the design `src` directory

```bash
cp sky130_vsdinv.lef ~/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/src/
cp libs/sky130_fd_sc_hd__fast.lib ~/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/src/
cp libs/sky130_fd_sc_hd__slow.lib ~/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/src/
cp libs/sky130_fd_sc_hd__typical.lib ~/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/src/
```

Screenshot of the commands run:

<img width="1187" height="216" alt="Screenshot 2026-10-19 184848" src="https://github.com/user-attachments/assets/666778c2-e2f7-4f3c-bc42-a88da4eb7b85" />

#### 5. Edit `config.tcl`

Lines added to `config.tcl` to include the custom cell in the flow:

```tcl
set ::env(LIB_SYNTH) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__typical.lib"
set ::env(LIB_FASTEST) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__fast.lib"
set ::env(LIB_SLOWEST) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__slow.lib"
set ::env(LIB_TYPICAL) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__typical.lib"
set ::env(EXTRA_LEFS) [glob $::env(OPENLANE_ROOT)/designs/$::env(DESIGN_NAME)/src/*.lef]
```

Screenshot of the edited `config.tcl`:

<img width="896" height="852" alt="Screenshot 2026-10-19 185606" src="https://github.com/user-attachments/assets/ea33b39f-691e-4dbc-831a-a0b23157f028" />

#### 6. Run synthesis with the custom inverter cell

```tcl
# Inside the OpenLANE docker
./flow.tcl -interactive
package require openlane 0.9
prep -design picorv32a

# Include the new LEF files and run synthesis
set lefs [glob $::env(DESIGN_DIR)/src/*.lef]
add_lefs -src $lefs
run_synthesis
```

Screenshots of the commands run:

<img width="871" height="316" alt="Screenshot 2026-10-19 190202" src="https://github.com/user-attachments/assets/fb1f92ad-a96f-415e-b497-60a479662f97" />
<img width="897" height="637" alt="Screenshot 2026-10-19 190219" src="https://github.com/user-attachments/assets/3df9363f-9000-46dc-aa78-d25d0019a4e4" />
<img width="887" height="830" alt="Screenshot 2026-10-19 190429" src="https://github.com/user-attachments/assets/ad29d372-bdce-490b-b27b-68312aa3961f" />

#### 7. Reduce the violations introduced by the custom cell

Design values noted before changing any parameters:

<img width="885" height="828" alt="Screenshot 2026-10-19 190447" src="https://github.com/user-attachments/assets/2119e0c6-0f27-43e7-9064-2f1de332c525" />
<img width="887" height="881" alt="Screenshot 2026-10-19 190458" src="https://github.com/user-attachments/assets/1b95a091-cd96-4e06-9fc7-432148eed6cc" />

Commands to view and change the parameters and re-run synthesis:

```tcl
echo $::env(SYNTH_STRATEGY)
echo $::env(SYNTH_BUFFERING)
echo $::env(SYNTH_SIZING)

set ::env(SYNTH_STRATEGY) "DELAY 3"
set ::env(SYNTH_SIZING) 1

run_synthesis
```

Screenshot of `merged.lef` in the `tmp` directory with the custom inverter as a macro:

<img width="815" height="630" alt="Screenshot 2026-10-19 191649" src="https://github.com/user-attachments/assets/e47bb11c-780d-4333-818b-b8a43eac8a55" />

Screenshots of the commands run:

<img width="877" height="647" alt="Screenshot 2026-10-19 191125" src="https://github.com/user-attachments/assets/e1345c71-1cb3-4384-a724-a0b0efe38eb1" />
<img width="830" height="343" alt="Screenshot 2026-10-19 191306" src="https://github.com/user-attachments/assets/eab6028e-e415-4d14-9d69-9cee512c2820" />
<img width="871" height="805" alt="Screenshot 2026-10-19 191709" src="https://github.com/user-attachments/assets/132f56d8-52cf-4491-944e-00d39c3eaebf" />

Comparison with the earlier values (area, worst negative slack):

<img width="867" height="802" alt="Screenshot 2026-10-19 191722" src="https://github.com/user-attachments/assets/309a8edf-f0c0-48b8-8c4a-9d731f19e435" />
<img width="870" height="801" alt="Screenshot 2026-10-19 191734" src="https://github.com/user-attachments/assets/deb5c199-d022-4d8d-86d6-7f7544d72212" />

| Metric | Before | After |
|---|---|---|
| Chip area | AREA_BEFORE | AREA_AFTER |
| Worst negative slack | WNS_BEFORE | WNS_AFTER |
| Total negative slack | TNS_BEFORE | TNS_AFTER |

#### 8. Floorplan and placement with the custom cell

```tcl
run_floorplan
```

Screenshots of the floorplan run:

<img width="880" height="801" alt="Screenshot 2026-10-19 191835" src="https://github.com/user-attachments/assets/fbd26d2b-05f8-4fc6-9275-c2c32e07d78e" />

Since we are facing unexpected un-explainable error while using *run_floorplan* command, we can instead use the following set of commands available based on information from *Desktop/work/tools/openlane_working_dir/openlane/scripts/tcl_commands/floorplan.tcl* and also based on Floorplan Commands section in *Desktop/work/tools/openlane_working_dir/openlane/docs/source/OpenLANE_commands.md*

```tcl
# Follwing commands are alltogather sourced in "run_floorplan" command
init_floorplan
place_io
tap_decap_or
```
<img width="880" height="453" alt="Screenshot 2026-10-19 191901" src="https://github.com/user-attachments/assets/fab059cc-c1e7-48ba-bfbd-e44ec348c17f" />
<img width="882" height="511" alt="Screenshot 2026-10-19 191928" src="https://github.com/user-attachments/assets/96d35e68-85c0-4030-b155-5d777fb88c34" />
<img width="903" height="607" alt="Screenshot 2026-10-19 191944" src="https://github.com/user-attachments/assets/b4a30184-0f78-4de9-83c0-b09b7d45af0a" />

Now run placement:

```tcl
run_placement
```

<img width="905" height="807" alt="Screenshot 2026-10-19 192243" src="https://github.com/user-attachments/assets/883ff590-a719-4a54-a45f-6df3f811a8b6" />

Load the placement DEF in Magic from another terminal:

```bash
cd Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/RUN_FOLDER_NAME/results/placement/

magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.placement.def &
```

Screenshot of the placement in Magic:

<img width="886" height="868" alt="Screenshot 2026-10-19 192354" src="https://github.com/user-attachments/assets/1a2d9f03-974f-413c-b17a-06d9d623a647" />

Custom inverter placed in the design, correctly abutted:

<img width="900" height="853" alt="Screenshot 2026-10-19 192730" src="https://github.com/user-attachments/assets/9903347d-70ff-4536-b157-d60ee40f41bd" />

Command for the tkcon window to see the internal layers of cells:

```tcl
expand
```

Power pins abutting the neighbouring library cells:

<img width="883" height="866" alt="Screenshot 2026-10-19 192835" src="https://github.com/user-attachments/assets/b2b23027-7e40-45e0-a49f-d622e7c67939" />
<img width="892" height="855" alt="Screenshot 2026-10-19 192851" src="https://github.com/user-attachments/assets/d595779d-2088-4be1-ac4a-23b3fd068185" />

#### 9. Post-synthesis timing analysis with OpenSTA

Commands used to run synthesis with the new LEF (see step 6). Screenshot of the final commands:

<img width="900" height="797" alt="Screenshot 2026-10-19 193359" src="https://github.com/user-attachments/assets/ff211e73-12d2-45d5-9c51-3e82773b3d4a" />

The `pre_sta.conf` file created in the `openlane` directory for STA:

<img width="645" height="347" alt="Screenshot 2026-10-19 193548" src="https://github.com/user-attachments/assets/4546f8e4-de4a-4000-89ed-6b761c219fb0" />
<img width="801" height="787" alt="Screenshot 2026-10-19 193910" src="https://github.com/user-attachments/assets/ba0f85e4-1100-475d-bf35-42bd9c9fd16b" />

Commands to run STA in another terminal:

```bash
cd Desktop/work/tools/openlane_working_dir/openlane
sta pre_sta.conf
```

Screenshots of the commands run:

<img width="895" height="690" alt="Screenshot 2026-10-19 194411" src="https://github.com/user-attachments/assets/e2a51f3d-4916-4ef5-95c3-497a7fb03f98" />
<img width="907" height="777" alt="Screenshot 2026-10-19 194430" src="https://github.com/user-attachments/assets/89d7d919-003b-4d21-9989-ba76f90146df" />
<img width="903" height="782" alt="Screenshot 2026-10-19 194441" src="https://github.com/user-attachments/assets/bd61b532-679d-48bd-abe0-877f81385f42" />

High fanout causes extra delay, so a fanout limit is added and synthesis is run again:

```tcl
set ::env(SYNTH_MAX_FANOUT) 4
run_synthesis
```

Screenshot of the final commands:

<img width="902" height="795" alt="Screenshot 2026-10-19 194800" src="https://github.com/user-attachments/assets/b0b330e6-0e9e-4649-b42f-d1ba13f679b9" />

Re-run STA in the other terminal:

```bash
sta pre_sta.conf
```

<img width="907" height="790" alt="Screenshot 2026-10-19 194834" src="https://github.com/user-attachments/assets/6900c971-d58e-4370-8eb5-bb5c1b7341a3" />
<img width="902" height="710" alt="Screenshot 2026-10-19 194858" src="https://github.com/user-attachments/assets/c5a65f04-2c61-4c74-9563-9c429064c329" />
<img width="941" height="795" alt="Screenshot 2026-10-19 194905" src="https://github.com/user-attachments/assets/604e2ac7-fd29-4157-9159-82c4da9d0b72" />

#### 10. Timing ECO fixes

OR gate of drive strength 2 is driving 4 fanouts

<img width="907" height="783" alt="Screenshot 2026-10-19 195023" src="https://github.com/user-attachments/assets/95946445-094b-4d16-ae1b-4f02557b24e3" />

Commands to perform analysis and optimize timing by replacing with OR gate of drive strength 4

```tcl
# Reports all the connections to a net
report_net -connections _11672_

# Checking command syntax
help replace_cell

# Replacing cell
replace_cell _14510_ sky130_fd_sc_hd__or3_4

# Generating custom timing report
report_checks -fields {net cap slew input_pins} -digits 4
```
Result - slack reduced
<img width="892" height="807" alt="Screenshot 2026-10-19 195217" src="https://github.com/user-attachments/assets/cfb27dbb-9693-4d7e-a77f-c26a7b67c1a1" />

<img width="931" height="738" alt="Screenshot 2026-10-19 195229" src="https://github.com/user-attachments/assets/4453c01c-ea36-4db4-8db1-8c27cfc3e2cb" />

<img width="897" height="552" alt="Screenshot 2026-10-19 195245" src="https://github.com/user-attachments/assets/67e31913-18e7-4cfe-a1a2-c99ceb113e56" />

<img width="886" height="801" alt="Screenshot 2026-10-19 195256" src="https://github.com/user-attachments/assets/b3cd5823-3c5d-4feb-ab70-4b6fa35a290e" />


OR gate of drive strength 2 is driving 4 fanouts

<img width="878" height="810" alt="Screenshot 2026-10-19 195347" src="https://github.com/user-attachments/assets/134fba9e-781c-441f-aa37-524da7873a4c" />

Commands to perform analysis and optimize timing by replacing with OR gate of drive strength 4

```tcl
# Reports all the connections to a net
report_net -connections _11675_

# Checking command syntax
help replace_cell

# Replacing cell
replace_cell _14510_ sky130_fd_sc_hd__or3_4

# Generating custom timing report
report_checks -fields {net cap slew input_pins} -digits 4
```
Result - slack reduced
<img width="903" height="777" alt="Screenshot 2026-10-19 195426" src="https://github.com/user-attachments/assets/c6b7c4ad-17e3-4558-a166-fb2e6d8f1bee" />

<img width="892" height="697" alt="Screenshot 2026-10-19 195446" src="https://github.com/user-attachments/assets/c3f0c83f-1d0e-4a07-95de-931bd22c603c" />

<img width="872" height="797" alt="Screenshot 2026-10-19 195506" src="https://github.com/user-attachments/assets/169e0b90-866a-47be-88bf-46f75ad5acb4" />

OR gate of drive strength 2 driving OA gate has more delay

<img width="885" height="586" alt="Screenshot 2026-10-19 195531" src="https://github.com/user-attachments/assets/69321462-3550-4153-8878-b5b62c50922a" />

Commands to perform analysis and optimize timing by replacing with OR gate of drive strength 4

```tcl
# Reports all the connections to a net
report_net -connections _11643_

# Checking command syntax
help replace_cell

# Replacing cell
replace_cell _14510_ sky130_fd_sc_hd__or3_4

# Generating custom timing report
report_checks -fields {net cap slew input_pins} -digits 4
```
Result - slack reduced
<img width="922" height="777" alt="Screenshot 2026-10-19 195616" src="https://github.com/user-attachments/assets/69fecf46-6760-472c-90dc-443c17fc1945" />

<img width="915" height="793" alt="Screenshot 2026-10-19 195627" src="https://github.com/user-attachments/assets/ebbd1ccc-96d5-4a17-b86d-74a1598ed540" />

OR gate of drive strength 2 driving OA gate has more delay

<img width="876" height="745" alt="Screenshot 2026-10-19 195638" src="https://github.com/user-attachments/assets/c9ff3847-b2fa-4df9-8c35-00184b195f34" />

Commands to perform analysis and optimize timing by replacing with OR gate of drive strength 4

```tcl
# Reports all the connections to a net
report_net -connections _11668_

# Checking command syntax
help replace_cell

# Replacing cell
replace_cell _14510_ sky130_fd_sc_hd__or3_4

# Generating custom timing report
report_checks -fields {net cap slew input_pins} -digits 4
```
Result - slack reduced

<img width="901" height="775" alt="Screenshot 2026-10-19 195716" src="https://github.com/user-attachments/assets/4208ce3d-75c5-4726-bd19-52f7f58c9937" />

<img width="900" height="795" alt="Screenshot 2026-10-19 195725" src="https://github.com/user-attachments/assets/b65ccd5e-b20e-4106-a3ce-93f4b1a4f10c" />


Commands to verify the instance was replaced:

```tcl
report_checks -from INSTANCE_PIN -to ENDPOINT -through INSTANCE_NAME
```

Screenshot of the replaced instance:

<img width="901" height="787" alt="Screenshot 2026-10-19 195811" src="https://github.com/user-attachments/assets/804a67ec-e582-41d9-aa8c-8669fe8026f7" />

*We started ECO fixes at wns -23.9000 and now we stand at wns -22.6173 we reduced around 1.2827 ns of violation*

#### 11. Use the ECO netlist and run floorplan, placement and CTS

Commands to back up the old netlist:

```bash
cd Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/RUN_FOLDER_NAME/results/synthesis/
cp picorv32a.synthesis.v picorv32a.synthesis_old.v
```

<img width="907" height="235" alt="Screenshot 2026-10-19 200027" src="https://github.com/user-attachments/assets/39ebfe1c-05bf-4f56-8857-8efacdd7d0db" />

Command to write the new netlist (inside OpenSTA):

```tcl
write_verilog /path/to/picorv32a.synthesis.v
```

<img width="905" height="45" alt="image" src="https://github.com/user-attachments/assets/68581301-3153-4014-a28a-efd10448c174" />
<img width="900" height="81" alt="Screenshot 2026-10-19 200419" src="https://github.com/user-attachments/assets/4b41ea2d-b12b-488c-a60d-c4ef2156b6eb" />

Verified that the netlist is overwritten by checking that instance _14506_ is replaced with sky130_fd_sc_hd__or4_4

<img width="796" height="737" alt="Screenshot 2026-10-19 200548" src="https://github.com/user-attachments/assets/cc65ced8-d140-4635-afcf-cc9c3f54b216" />

Since we confirmed that netlist is replaced and will be loaded in PnR but since we want to follow up on the earlier 0 violation design we are continuing with the clean design to further stages

Commands load the design and run necessary stages
Commands to load the design and run the stages:

```tcl
prep -design picorv32a -tag 19-10_13-31 -overwrite
set lefs [glob $::env(DESIGN_DIR)/src/*.lef]
add_lefs -src $lefs

init_floorplan
place_io
tap_decap_or

run_placement
run_cts
```

Screenshots of the commands run:

<img width="897" height="626" alt="Screenshot 2026-10-19 201319" src="https://github.com/user-attachments/assets/db201ccd-c5d4-47bb-adb5-bb1b4c767e5a" />
<img width="702" height="187" alt="Screenshot 2026-10-19 201357" src="https://github.com/user-attachments/assets/fb3da3fd-18a5-445a-81e7-ef04cadf2a8f" />
<img width="907" height="750" alt="Screenshot 2026-10-19 201535" src="https://github.com/user-attachments/assets/3bac5619-6934-431d-b058-dbc2fe9e241f" />
<img width="917" height="792" alt="Screenshot 2026-10-19 201605" src="https://github.com/user-attachments/assets/3aacb707-52a2-485b-a4c7-3f7167ece306" />
<img width="907" height="782" alt="Screenshot 2026-10-19 204355" src="https://github.com/user-attachments/assets/1aa3941e-d0eb-43e5-b1d0-0dc35a4a1d08" />

#### 12. Post-CTS OpenROAD timing analysis

Commands to run in the OpenLANE flow to use the OpenSTA built into OpenROAD:

```tcl
openroad

read_lef /openLANE_flow/designs/picorv32a/runs/RUN_FOLDER_NAME/tmp/merged.lef
read_def /openLANE_flow/designs/picorv32a/runs/RUN_FOLDER_NAME/results/cts/picorv32a.cts.def
write_db pico_cts.db
read_db pico_cts.db
read_verilog /openLANE_flow/designs/picorv32a/runs/RUN_FOLDER_NAME/results/synthesis/picorv32a.synthesis_cts.v
read_liberty $::env(LIB_SYNTH_COMPLETE)
link_design picorv32a
read_sdc /openLANE_flow/designs/picorv32a/src/my_base.sdc
set_propagated_clock [all_clocks]

report_checks -path_delay min_max -format full_clock_expanded -digits 4
```

Screenshots of the commands run and the timing report:

<img width="908" height="511" alt="Screenshot 2026-10-19 204742" src="https://github.com/user-attachments/assets/b953b2a0-2a0c-4320-8468-cae1afd8f53f" />
<img width="895" height="773" alt="Screenshot 2026-10-19 204955" src="https://github.com/user-attachments/assets/6fea97eb-621e-47af-ad46-6bf859aefc5d" />
<img width="891" height="792" alt="Screenshot 2026-10-19 205018" src="https://github.com/user-attachments/assets/9d17bcc3-89cd-4f37-a447-f74cab9a1e64" />
<img width="873" height="787" alt="Screenshot 2026-10-19 205026" src="https://github.com/user-attachments/assets/6af36450-9a23-4335-a802-8560f70f41e9" />

#### 13. Post-CTS analysis after changing `CTS_CLK_BUFFER_LIST`

Commands to run in the OpenLANE flow after removing `sky130_fd_sc_hd__clkbuf_1` from the buffer list:

```tcl
echo $::env(CTS_CLK_BUFFER_LIST)
set ::env(CTS_CLK_BUFFER_LIST) [lreplace $::env(CTS_CLK_BUFFER_LIST) 0 0]
echo $::env(CTS_CLK_BUFFER_LIST)

# Go back to the placement DEF and re-run CTS
set ::env(CURRENT_DEF) /openLANE_flow/designs/picorv32a/runs/RUN_FOLDER_NAME/results/placement/picorv32a.placement.def
run_cts

# Repeat the OpenROAD timing analysis from step 12
```

Screenshots of the commands run and the timing report:

<img width="868" height="736" alt="Screenshot 2026-10-19 212947" src="https://github.com/user-attachments/assets/f52027d3-7de8-44f0-bc3e-32864d2013cd" />
<img width="897" height="801" alt="Screenshot 2026-10-19 215616" src="https://github.com/user-attachments/assets/520d95ec-6fac-4e36-a9d7-b1b0d7ddc9f4" />
<img width="867" height="336" alt="Screenshot 2026-10-19 215735" src="https://github.com/user-attachments/assets/6dfa2fd7-65d0-4d8e-b90f-6d3c4cc953df" />
<img width="872" height="193" alt="Screenshot 2026-10-19 215821" src="https://github.com/user-attachments/assets/20b41271-e5e8-49a2-8941-8c64157a62c1" />

---

## Section 5 - Final steps for RTL to GDSII using TritonRoute and OpenSTA

**Dates:** DD/MM/YYYY - DD/MM/YYYY

### Implementation

**Tasks**

1. Generate the Power Distribution Network (PDN) and explore the PDN layout.
2. Perform detailed routing using TritonRoute.
3. Extract post-route parasitics using the SPEF extractor.
4. Run post-route OpenSTA timing analysis with the extracted parasitics.

All Section 5 logs, reports and results are in this run folder:

[Section 5 Run - RUN_FOLDER_NAME](openlane/designs/picorv32a/runs/RUN_FOLDER_NAME)

#### 1. Generate the PDN and explore the layout

Commands to run all the stages needed up to this point:

```tcl
cd Desktop/work/tools/openlane_working_dir/openlane
docker

./flow.tcl -interactive
package require openlane 0.9
prep -design picorv32a

set lefs [glob $::env(DESIGN_DIR)/src/*.lef]
add_lefs -src $lefs

run_synthesis
run_floorplan
run_placement
run_cts

# Check which DEF the flow is currently using
echo $::env(CURRENT_DEF)

# Generate the power distribution network
gen_pdn
```

Screenshots of the PDN run:

<img width="896" height="796" alt="Screenshot 2026-10-19 220346" src="https://github.com/user-attachments/assets/132e9dba-74b8-4c91-84e2-084db0e279ae" />
<img width="886" height="791" alt="Screenshot 2026-10-19 220357" src="https://github.com/user-attachments/assets/ab586915-4ec5-4e61-9f98-02edc1eddeb2" />

Commands to load the PDN DEF in Magic from another terminal:

```bash
cd Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/RUN_FOLDER_NAME/tmp/floorplan/

magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../merged.lef def read 14-pdn.def &
```

Screenshots of the PDN DEF:

<img width="893" height="860" alt="Screenshot 2026-10-19 220548" src="https://github.com/user-attachments/assets/6b8e35b1-0535-452d-a60a-b5b5049423d8" />
<img width="888" height="852" alt="Screenshot 2026-10-19 220701" src="https://github.com/user-attachments/assets/5db220ed-24df-4144-bf85-f9168250a572" />
<img width="1148" height="842" alt="Screenshot 2026-10-19 220814" src="https://github.com/user-attachments/assets/68b7ead7-1eee-4c2d-961f-0b17583c07c9" />

#### 2. Detailed routing with TritonRoute

Command to run routing:

```tcl
echo $::env(ROUTING_STRATEGY)
run_routing
```

Screenshots of the routing run:

<img width="990" height="795" alt="Screenshot 2026-10-19 221642" src="https://github.com/user-attachments/assets/0342c867-4319-412c-a880-1d410ed2976a" />
<img width="996" height="850" alt="Screenshot 2026-10-19 221743" src="https://github.com/user-attachments/assets/8ff1cdaf-7f82-49a5-be73-2c89c8bba605" />
<img width="987" height="802" alt="Screenshot 2026-10-19 221753" src="https://github.com/user-attachments/assets/03ff716d-9484-4b74-a204-5757e4955887" />

Commands to load the routed DEF in Magic from another terminal:

```bash
cd Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/RUN_FOLDER_NAME/results/routing/

magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.def &
```

Screenshots of the routed DEF:

<img width="991" height="855" alt="Screenshot 2026-10-19 221911" src="https://github.com/user-attachments/assets/2bc46eed-d4b6-4d6d-8d07-2cdc4d9e4afa" />
<img width="997" height="855" alt="Screenshot 2026-10-19 221953" src="https://github.com/user-attachments/assets/0b7f3017-a940-4cde-97f8-57af8874b7e9" />
<img width="980" height="832" alt="Screenshot 2026-10-19 222051" src="https://github.com/user-attachments/assets/e979770b-c379-4d3c-bb75-d06636929c0c" />
<img width="985" height="857" alt="Screenshot 2026-10-19 222202" src="https://github.com/user-attachments/assets/f6f9a089-d060-438f-959d-5fc401df28aa" />

Screenshot of the fast route guide in `openlane/designs/picorv32a/runs/RUN_FOLDER_NAME/tmp/routing`:

<img width="975" height="847" alt="Screenshot 2026-10-19 222413" src="https://github.com/user-attachments/assets/625c2def-14d4-4c49-a947-78794b5ad597" />

Routing results:

| Check | Result |
|---|---|
| DRC violations after detailed routing | DRC_COUNT |
| Antenna violations | ANTENNA_COUNT |
| Routing runtime | RUNTIME |

#### 3. Post-route parasitic extraction (SPEF)

Commands for SPEF extraction with the external tool:

```bash
cd Desktop/work/tools/SPEF_EXTRACTOR
python3 main.py /path/to/merged.lef /path/to/picorv32a.def
```

Screenshot of the extracted SPEF file:

![Section 5 - SPEF extraction](images/section5/spef.png)

#### 4. Post-route OpenSTA timing analysis

Commands to run in the OpenLANE flow to do the timing analysis with the OpenSTA built into OpenROAD:

```tcl
openroad

read_lef /openLANE_flow/designs/picorv32a/runs/RUN_FOLDER_NAME/tmp/merged.lef
read_def /openLANE_flow/designs/picorv32a/runs/RUN_FOLDER_NAME/results/routing/picorv32a.def
write_db pico_route.db
read_db pico_route.db
read_verilog /openLANE_flow/designs/picorv32a/runs/RUN_FOLDER_NAME/results/synthesis/picorv32a.synthesis_preroute.v
read_liberty $::env(LIB_SYNTH_COMPLETE)
link_design picorv32a
read_sdc /openLANE_flow/designs/picorv32a/src/my_base.sdc
set_propagated_clock [all_clocks]
read_spef /openLANE_flow/designs/picorv32a/runs/RUN_FOLDER_NAME/results/routing/picorv32a.spef

report_checks -path_delay min_max -format full_clock_expanded -digits 4
```

Screenshots of the commands run and the timing report:

![Section 5 - post-route timing](images/section5/post_route_1.png)
![Section 5 - post-route timing](images/section5/post_route_2.png)
![Section 5 - post-route timing](images/section5/post_route_3.png)
![Section 5 - post-route timing](images/section5/post_route_4.png)

---

## Certificate

![Certificate](images/certificate.png)

## Acknowledgements

- [VLSI System Design (VSD)](https://www.vlsisystemdesign.com/) and NASSCOM for the workshop.
- The OpenLANE, SkyWater PDK, Magic, OpenSTA and ngspice open-source communities.
