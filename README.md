# RISC-V SoC Tapeout Program Documentation

This repository contains documentation and relevant materials for the **RISC-V SoC Tapeout Program**, a collaborative initiative between IIT Gandhinagar, VLSI System Design (VSD), and other prominent organizations in the field of semiconductor design.

Program Overview
The RISC-V SoC Tapeout Program aims to provide hands-on experience to participants in designing, verifying, and testing RISC-V based System-on-Chip (SoC). This program is part of Phase 2 of the collaboration between IIT Gandhinagar and VSD.

Repository Contents

- Documentation related to the program
- Design files and resources
- Relevant tools and scripts

## 🙏 **Acknowledgment**

I am thankful to [**Kunal Ghosh**](https://github.com/kunalg123), Team **[VLSI System Design (VSD)](https://vsdiat.vlsisystemdesign.com/)**  and ***IIT Gandhinagar*** for the opportunity to participate in the ongoing **RISC-V SoC Tapeout Program**.  

I also acknowledge the support of **RISC-V International**, **India Semiconductor Mission (ISM)**, **VLSI Society of India (VSI)**, [**Efabless**](https://github.com/efabless) and IIT Gandhinagar for making this initiative possible. Their contributions and guidance have been instrumental in shaping this program.
<div align="left">


# 11-12-2025. 
# Objective:
RISC V Tape Out Program Thanks for VSD Kunal Gosh, Sameer bhai and IIT Gandhinagar started 11 am with 

<img width="861" height="457" alt="image" src="https://github.com/user-attachments/assets/75a8d745-de36-42ef-bc51-797930635c62" />

<img width="1059" height="606" alt="image" src="https://github.com/user-attachments/assets/aa6c5c97-e6f8-41f7-b257-df03b6ab5dfb" />

# 12-12-2025. 
# Objective:
Got Credential of login start working on RISC V Tape Out Program on opensource tools jumped into commercial tools of synopsys

<img width="560" height="193" alt="image" src="https://github.com/user-attachments/assets/09d31916-74e6-415a-86ae-e0ae32ca3b26" />

# 13-12-2025. 
# Objective:
Challenge: 1 with Synthesis with blackbox modules RAM128, RAM256 and POR 
Challenge: 2 GLS

Results  got with normal synthesis but without topographical-based synthesis
# 14-12-2025. 
# Objective:
Identified with soultion with compile_ultra -incremental The incremental compile also supports adaptive retiming with the compile_ultra -incremental -retime command.

Running the compile_ultra -incremental command on a topographical netlist results in placement-based optimization only. This compile should not be thought of as an incremental mapping. 
Finally the soultion for GLS with adding all the rtl files in gl folder
# 15-12-2025. 
# Objective:
POR Power On Resent Module usage analysis; done brain stroming come across 3 approaches:
### 1. Approache :
1. approaches identify all the signals replaced  (9 files to modify,~30 changes required, MODULES WITH REAL DEPENDENCIES:
 housekeeping.v:           6 locations (Flash, SPI, state machines)
 caravel_clocking.v:       1 CRITICAL (master reset AND gate) )
### Results:
  Failed
### 2. Approache :
2. Approach Just replacing the signal of PoR (porb_h,porb_l,por) with resetb
### Results:
  passed
### 3. Approache :
3. Approach keep extrernal input reset pin to the Core
### Results:
   Passed
10:08 am started without break Time-up for the closing the lab it was 8:55pm
# 16-12-2025. 
# Objective:
Started with new challenge brainstroming with document verifing with design again with task!!!till 2:00 pm
Started working on Documnetation.
below feedback i got from designer (may be all of you are actually doing it already). So will wait

Step A: Decide the single source of truth for pads in SCL180:
1) Either keep a clean SCL180-only pad wrapper module, or
2) Parameterize the pad wrapper and explicitly tie enables per SCL180 documentation.

Step B: Patch mprj_io.v (or replace it) and re-run:
1) VCS RTL sim
2) DC_TOPO synth
3) VCS GLS

Step C: Add the required “PAD reasoning” doc showing:
1) Reset pad is usable without POR gating
2) Any required enables are safe to tie high / derived from stable supplies
3) Why SKY130 needed POR but SCL180 does not (your requested justification section)
Started debugging
# 17-12-2025. 
# Objective: 
Restarted the debugging and then done changes in pads.v only scl180 used completely removed the sky130, updated the files

# 18-12-2025. 
Test (RTL) Failed for goio, irq, storage, and mjpr_ctrl ...........Failed

# 19-12-2025. 
The objective of this task is to create a correct SoC floorplan using ICC2, meeting exact die size and IO pad placement targets, and to develop hands-on familiarity with ICC2 floorplanning commands and concepts.
Done 
•	Set die area to 3.588 mm × 5.188 mm
•	Define a reasonable core offset
•	Place IO pads around all four sides


