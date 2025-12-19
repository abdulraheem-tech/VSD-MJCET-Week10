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
### You must explicitly set:
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


```
place_pins -self
```

<div align="center" >
 <img width="1837" height="944" alt="image" src="https://github.com/user-attachments/assets/796fbb06-6bf7-4949-95bb-fba257009ed7" />

</div>

<div align="center" >
 <img width="1610" height="1025" alt="image" src="https://github.com/user-attachments/assets/08954d88-4bab-483f-ba2b-9a0b2e455764" />

</div>
---





**Task-5 successfully completed.**
