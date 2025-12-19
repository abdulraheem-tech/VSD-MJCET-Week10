# Task-5: SoC Floorplanning Using ICC2
### VSD Caravel-Based RISC-V SoC | SCL-180 Technology
Step A: Decide the single source of truth for pads in SCL180:
---

## 1. objective 

 The objective of this task is to create a correct SoC floorplan using ICC2, meeting exact
die size and IO pad placement targets, and to develop hands-on familiarity with ICC2
floorplanning commands and concepts.*.

## 2. Target Floorplan Requirements
### 1. Die Area (Mandatory)
  Create a die with the exact dimensions:
   Width: 3.588 mm
   Height: 5.188 mm

## Floorplan configuration

### Die size

The die is initialized using die-controlled floorplanning with the following dimensions (in microns):  

- **Die width**: 3.588 mm → 3588 µm. 
- **Die height**: 5.188 mm → 5188 µm. 

In the script:

```tcl
initialize_floorplan \
    -control_type die \
    -boundary {{0 0} {3588 5188}} \
    -core_offset {200 200 200 200}
```

This sets the die boundary from \((0,0)\) to \((3588,5188)\) in the ICC2 coordinate system. 

### Core margin

A uniform **core margin of 200 µm on all four sides** is used via `-core_offset {200 200 200 200}`. This results in a core area of: 

- Core lower-left: \((200, 200)\)  
- Core upper-right: \((3388, 4988)\)  

These values are also echoed in `floorplan_report.txt`:

```tcl
puts "Die Area  : 0 0 3588 5188  (microns)"
puts "Core Area : 200 200 3388 4988  (microns)"
```
 Die area
 Core area (with reasonable margins)


### 2. IO Pad Placement (Mandatory)
 Place IO pads around the die boundary
 Pads must be:
o Evenly distributed
o Properly oriented (top / bottom / left / right)
o Aligned to edges (no floating pads)

- Reset sequencing FSMs
- Power-edge detection in RTL

* Housekeeping logic
* Clocking and peripheral logic
* Wrapper modules

> No functional logic was altered beyond reset handling.

---

##

### 3.2 Files Structure


```text
Task_Floorplan_ICC2/
├── scripts/
│ └── floorplan.tcl
├── reports/

│ └── floorplan_report.txt
├── images/
│ └── floorplan_screenshot.png
└── README.md
```


## 4 IO Pad Placement 
 Place IO pads around the die boundary
 Pads must be:
o Evenly distributed
o Properly oriented (top / bottom / left / right)
For this task, **IO regions are created using hard placement blockages** along all four edges of the die. These blockages reserve space along the boundary where IO pads/ports can be placed and prevent standard-cell placement there. 

### IO regions using placement blockages

The script defines four IO regions as hard blockages:

```tcl
# Bottom IO region
create_placement_blockage \
  -name IO_BOTTOM \
  -type hard \
  -boundary {{0 0} {3588 100}}

# Top IO region
create_placement_blockage \
  -name IO_TOP \
  -type hard \
  -boundary {{0 5088} {3588 5188}}

# Left IO region
create_placement_blockage \
  -name IO_LEFT \
  -type hard \
  -boundary {{0 100} {100 5088}}

# Right IO region
create_placement_blockage \
  -name IO_RIGHT \
  -type hard \
  -boundary {{3488 100} {3588 5088}}
```

- Bottom strip: 100 µm tall along the entire die width.
- Top strip: 100 µm tall along the entire die width.
- Left strip: 100 µm wide along most of die height.
- Right strip: 100 µm wide along most of die height.

These regions provide **continuous IO bands** around the die, enabling even distribution of IO ports and avoiding overlap with core placement. 




<div align="center" >
 <img width="1610" height="1025" alt="image" src="https://github.com/user-attachments/assets/08954d88-4bab-483f-ba2b-9a0b2e455764" />

</div>
```
place_pins -self
```
<div align="center" >
 <img width="1837" height="944" alt="image" src="https://github.com/user-attachments/assets/796fbb06-6bf7-4949-95bb-fba257009ed7" />

</div>

---


```tcl
############################################################
# Task 5 – SoC Floorplanning Using ICC2 (Floorplan Only)
# Design  : vsdcaravel
# Tool    : Synopsys ICC2 2022.12
############################################################

# ---------------------------------------------------------
# Basic Setup
# ---------------------------------------------------------
set DESIGN_NAME      vsdcaravel
set DESIGN_LIBRARY   vsdcaravel_fp_lib

# ---------------------------------------------------------
# Reference Library (includes technology internally)
# ---------------------------------------------------------
# Using unified NDM library provided with ICC2 workshop
set REF_LIB \
"/home/maraheem/home/work/run1/icc2_workshop_collaterals/standaloneFlow/work/raven_wrapperNangate/lib.ndm"

# ---------------------------------------------------------
# Create ICC2 Design Library
# ---------------------------------------------------------
if {[file exists $DESIGN_LIBRARY]} {
    file delete -force $DESIGN_LIBRARY
}

create_lib $DESIGN_LIBRARY \
    -ref_libs $REF_LIB

# ---------------------------------------------------------
# Read Synthesized Netlist
# (Netlist is read only to create design context;
# unresolved cells are acceptable for floorplan-only task)
# ---------------------------------------------------------
read_verilog -top $DESIGN_NAME \ "/home/maraheem/raheem/task5/vsdRiscvScl180/FP/vsdcaravel_synthesis.v"

current_design $DESIGN_NAME



# ---------------------------------------------------------
# Floorplan Definition (MANDATORY)
# Die Size  : 3.588 mm × 5.188 mm
# Core Margin : 200 µm on all sides
#
# NOTE:
# This ICC2 version requires die-controlled initialization
# using -control_type die and -boundary syntax.
# ---------------------------------------------------------
initialize_floorplan \
    -control_type die \
    -boundary {{0 0} {3588 5188}} \
    -core_offset {200 200 200 200}
# ---------------------------------------------------------
# IO Regions using Placement Blockages (Corrected)
# ---------------------------------------------------------

# Bottom IO region (along bottom die edge)
create_placement_blockage \
  -name IO_BOTTOM \
  -type hard \
  -boundary {{0 0} {3588 100}}

# Top IO region (along top die edge)
create_placement_blockage \
  -name IO_TOP \
  -type hard \
  -boundary {{0 5088} {3588 5188}}

# Left IO region (along left die edge)
create_placement_blockage \
  -name IO_LEFT \
  -type hard \
  -boundary {{0 100} {100 5088}}

# Right IO region (along right die edge)
create_placement_blockage \
  -name IO_RIGHT \
  -type hard \
  -boundary {{3488 100} {3588 5088}}


# ---------------------------------------------------------
# Macro Placement
# ---------------------------------------------------------
# NOTE:
# No physical hard macros exist in this design.
# RAM128 and RAM256 are RTL-based memory models that were
# synthesized into logic and optimized away.
# Therefore, no macro placement is performed here.
# ---------------------------------------------------------

# ---------------------------------------------------------
# Reports
# ---------------------------------------------------------
report_floorplan >  "/home/maraheem/raheem/task5/vsdRiscvScl180/FP/floorplan_report.txt"

# ---------------------------------------------------------
# Save and Launch GUI
# ---------------------------------------------------------
save_block -force -label FLOORPLAN_ONLY
save_lib -all
start_gui
```


**Task-5 successfully completed.**
