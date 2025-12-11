# I. Caravel Functional-vs-GLS Verification Task

# Objective:
Verify that Caravel’s RTL simulation and Gate-Level Simulation (GLS) produce identical functional results for the hkspi test and subsequently for all other DV tests inside efabless/caravel/verilog/dv.

# Tools Allowed:
Icarus Verilog or Verilator, OpenLane + OpenROAD, Magic/KLayout, Sky130 PDK, GitHub Codespaces or local system.

# Setting Up vsdip/vsd-rtl github codespace

## RTL Design & Synthesis on Cloud (GitHub Codespace)

This repository provides a ready-to-use **cloud-based lab** for RTL Design and Synthesis using open-source tools such as **Yosys**, **Icarus Verilog**, and **GTKWave**.
All tools run inside a **GitHub Codespace** with **noVNC desktop access**, requiring no local installation.

---

### Step 1 – Launch Codespace

Click **“Code → Codespaces → Create codespace on main”** to start your workspace on the cloud.
GitHub will automatically build and set up your environment.

<img width="582" height="541" alt="image" src="https://github.com/user-attachments/assets/50177e52-4584-4412-99cb-cabfcd5481f6" />




---

### Step 2 – Codespace Setup and Logs

During setup, the Codespace installs all required tools.
Wait for the setup logs to complete (approximately 7–10 minutes).


After successful configuration, your container environment is ready.

<img width="1010" height="494" alt="image" src="https://github.com/user-attachments/assets/924f2362-2e95-4775-9b23-daec1e8e8ac5" />


---

### Step 3 – Open a Terminal

Use **Terminal → New Terminal** inside VS Code to begin executing synthesis and simulation commands.

<img width="573" height="102" alt="image" src="https://github.com/user-attachments/assets/7668d43e-561b-4fb2-b3e2-740cf6992c7d" />


---

### Step 4 – Verify Tool Installation

Run the following commands to verify tool installation:

```bash
yosys
iverilog
```

Both should display their version information, confirming correct setup.

<img width="1201" height="763" alt="1" src="https://github.com/user-attachments/assets/67ac4697-0c53-4e3a-8ba6-fe9f8bf09c97" />

---

# Caravel hkspi Functional vs GLS Verification

## Objective

In this work, I verified that Caravel’s RTL simulation and gate‑level simulation (GLS) produce identical functional results for the `hkspi` test using the Sky130A PDK and open‑source tools (Icarus Verilog, volare, git, etc.). I documented all commands I used, the errors I faced, and how I resolved them, ending with a register‑by‑register comparison between RTL and GLS that matched. 

***

## Environment Setup

### Creating workspace and cloning Caravel

```bash
mkdir -p caravel_vsd
cd ./caravel_vsd/
git clone https://github.com/efabless/caravel
cd caravel
```

<img width="607" height="261" alt="image" src="https://github.com/user-attachments/assets/6cb4fd10-4ceb-4fd0-b3e1-9601074ce9a2" />



- `mkdir -p caravel_vsd`: I created a working directory `caravel_vsd` to keep all Caravel‑related files organized; `-p` ensures no error if the directory already exists. 
- `cd ./caravel_vsd/`: I moved into the workspace folder so all subsequent operations stay local to this project. 
- `git clone https://github.com/efabless/caravel`: I cloned the official efabless Caravel repository that contains RTL, GL netlists, DV tests, and scripts needed for the hkspi verification task. 
- `cd caravel`: I entered the cloned Caravel repository root to run all project‑specific commands from there. 

***

### Initializing git submodules

```bash
git submodule update --init --recursive
```

- I initialized and updated all git submodules (for example, some IPs and dependencies which are brought in as submodules) to ensure the repository was completely populated. 
- `--recursive` ensured nested submodules, if any, were also fetched, avoiding missing‑directory errors later in the flow. 

***

### Installing and enabling Sky130 PDK with volare

```bash
pip install volare --user --upgrade
export PATH=$PATH:$HOME/.local/bin
export CARAVEL_ROOT=$(pwd)
export PDK_ROOT=$(pwd)/pdk
export PDK=sky130A
volare enable 12df12e2e74145e31c5a13de02f9a1e176b56e67
```

- `pip install volare --user --upgrade`: I installed (or upgraded) the `volare` PDK management tool in my user environment; it manages Sky130 PDK versions and caches. 
- `export PATH=$PATH:$HOME/.local/bin`: I added the local Python bin directory to `PATH` so that the `volare` executable is found in the shell. 
- `export CARAVEL_ROOT=$(pwd)`: I defined `CARAVEL_ROOT` as the absolute path of the Caravel repo root for use in subsequent commands and include paths. 
- `export PDK_ROOT=$(pwd)/pdk`: I selected `./pdk` under the Caravel root as the installation/cache location for the Sky130 PDK. 
- `export PDK=sky130A`: I specified that the active PDK is `sky130A` so scripts and Makefiles know which PDK flavour to use. 
- `volare enable 12df12e2e74145e31c5a13de02f9a1e176b56e67`: I enabled a specific Sky130A PDK commit/version; volare downloaded the required archives (standard cells, IOs, SRAM, etc.) and made them accessible under `$PDK_ROOT`. 

<img<img width="890" height="491" alt="image" src="https://github.com/user-attachments/assets/3aa42157-d675-48ff-bfb5-a06f7ca377e2" />


***

# RTL AND GLS Simulation of HKSPI using Mixed (RTL + GLS) Technique

## Prerequisites

Open a GitHub Codespace (or Ubuntu terminal), ensure you have `git`, `iverilog`, and `python3` installed:

```bash
sudo apt-get update
sudo apt-get install -y git iverilog python3 python3-pip
```
<img width="862" height="648" alt="image" src="https://github.com/user-attachments/assets/db6f4841-0d21-4d07-a53d-8131924bcfab" />


***

## Step 5: Create workspace and clone Caravel

```bash
mkdir -p ~/caravel_vsd
cd ~/caravel_vsd
git clone https://github.com/efabless/caravel
cd caravel
```
<img width="695" height="334" alt="image" src="https://github.com/user-attachments/assets/7626080a-9e7f-4de2-97b9-1c1f33c33a37" />

***

## Step 6: Initialize git submodules

```bash
git submodule update --init --recursive
```
<img width="692" height="24" alt="image" src="https://github.com/user-attachments/assets/13f03056-b40b-4089-9b0d-54ff2a06d770" />


***

## Step 7: Install volare and enable Sky130A PDK

```bash
pip install volare --user --upgrade
export PATH=$PATH:$HOME/.local/bin
export CARAVEL_ROOT=$(pwd)
export PDK_ROOT=$(pwd)/pdk
export PDK=sky130A
volare enable 12df12e2e74145e31c5a13de02f9a1e176b56e67
```

*This downloads the Sky130A PDK libraries into `$CARAVEL_ROOT/pdk/sky130A/`.*

<img width="1123" height="617" alt="image" src="https://github.com/user-attachments/assets/a16915a9-1337-45b0-a1e6-297eee0d825e" />

<img width="1081" height="714" alt="image" src="https://github.com/user-attachments/assets/ce69be58-cc69-40be-b349-ca1a45024dfd" />


***

## Step 10: Clone the management SoC wrapper

```bash
cd $CARAVEL_ROOT
rm -rf verilog/rtl/mgmt_core_wrapper
git clone https://github.com/efabless/caravel_mgmt_soc_litex verilog/rtl/mgmt_core_wrapper
```

*This brings in the LiteX management core (including VexRiscv) under `verilog/rtl/mgmt_core_wrapper/`.*

<img width="708" height="140" alt="image" src="https://github.com/user-attachments/assets/21cff3f9-bb81-46f7-a58c-04368d6b3662" />



***

## Step 11: Patch `caravel_netlists.v` for GLS

```bash
cd $CARAVEL_ROOT/verilog/rtl
sed -i 's|"gl/digital_pll.v"|"digital_pll.v"|g' caravel_netlists.v
sed -i 's|"gl/gpio_control_block.v"|"gpio_control_block.v"|g' caravel_netlists.v
sed -i 's|"gl/gpio_signal_buffering.v"|"gpio_signal_buffering.v"|g' caravel_netlists.v
sed -i 's|"gl/mgmt_defines.v"|"defines.v"|g' caravel_netlists.v
sed -i 's|"gl/mgmt_core_wrapper.v"|"mgmt_core_wrapper.v"|g' caravel_netlists.v
sed -i 's|`include "housekeeping.v"|// `include "housekeeping.v"|g' caravel_netlists.v
```

*These fix broken `gl/` include paths and prevent duplicate RTL housekeeping from being pulled in during GLS.*

<img width="670" height="139" alt="image" src="https://github.com/user-attachments/assets/d02f8acb-94a4-44c7-b5d7-df416764ae6d" />



***

## Step 12: Create a dummy fill‑cell module

```bash
cd $CARAVEL_ROOT
echo 'module sky130_ef_sc_hd__fill_4(inout VPWR, inout VGND, inout VPB, inout VNB); endmodule' > verilog/dv/dummy_fill.v
```

*This stubs a filler cell used in GL netlists but not functionally needed.*

<img width="906" height="52" alt="image" src="https://github.com/user-attachments/assets/876f7500-3043-4c7f-975f-6ea8b0c17ef1" />


***

## Step 13: Navigate to the hkspi test directory

```bash
cd $CARAVEL_ROOT/verilog/dv/caravel/mgmt_soc/hkspi
```

***

## Step 14: (Optional) Disable VCD dumping to save disk space

```bash
sed -i 's/\$dumpfile/\/\/ \$dumpfile/g' hkspi_tb.v
sed -i 's/\$dumpvars/\/\/ \$dumpvars/g' hkspi_tb.v
```

*Comment out waveform generation for faster, lighter runs.*

<img width="1021" height="62" alt="image" src="https://github.com/user-attachments/assets/06811313-7d38-4d0e-879f-ca9987e1396f" />



***

## Step 15: RTL simulation

### 15.1 Locate VexRiscv

```bash
export VEX_FILE=$(find $CARAVEL_ROOT/verilog/rtl/mgmt_core_wrapper -name "VexRiscv*.v" | head -n 1)
```
<img width="1140" height="40" alt="image" src="https://github.com/user-attachments/assets/512474a3-3899-4b41-ab7e-d777fb0d8005" />


### 15.2 Compile RTL with iverilog

```bash
iverilog -Ttyp \
  -DFUNCTIONAL -DSIM \
  -D USE_POWER_PINS \
  -D UNIT_DELAY=#1 \
  -I $CARAVEL_ROOT/verilog/dv/caravel/mgmt_soc \
  -I $CARAVEL_ROOT/verilog/dv/caravel \
  -I $CARAVEL_ROOT/verilog/rtl \
  -I $CARAVEL_ROOT/verilog \
  -I $CARAVEL_ROOT/verilog/rtl/mgmt_core_wrapper/verilog/rtl \
  -I $PDK_ROOT/sky130A \
  -y $CARAVEL_ROOT/verilog/rtl \
  -y $CARAVEL_ROOT/verilog/rtl/mgmt_core_wrapper/verilog/rtl \
  $PDK_ROOT/sky130A/libs.ref/sky130_fd_sc_hd/verilog/primitives.v \
  $PDK_ROOT/sky130A/libs.ref/sky130_fd_sc_hd/verilog/sky130_fd_sc_hd.v \
  $PDK_ROOT/sky130A/libs.ref/sky130_fd_io/verilog/sky130_fd_io.v \
  $CARAVEL_ROOT/verilog/dv/dummy_fill.v \
  $VEX_FILE \
  hkspi_tb.v -o hkspi_rtl.vvp
```
<img width="888" height="330" alt="image" src="https://github.com/user-attachments/assets/bf536003-d270-4445-85b4-b402e3478935" />


### 15.3 Run RTL simulation

```bash
vvp hkspi_rtl.vvp | tee rtl_hkspi.log
```

*You should see "Test HK SPI (RTL) Passed" in the log.*

<img width="933" height="481" alt="image" src="https://github.com/user-attachments/assets/78e942d7-fd4b-40cb-9e7c-4cc330300c80" />



***

## Step 16: GLS (mixed RTL+GL) simulation

### 16.1 Compile GLS with iverilog

```bash
iverilog -Ttyp \
  -DFUNCTIONAL -DSIM \
  -D USE_POWER_PINS \
  -D UNIT_DELAY=#1 \
  -I $CARAVEL_ROOT/verilog/dv/caravel/mgmt_soc \
  -I $CARAVEL_ROOT/verilog/dv/caravel \
  -I $CARAVEL_ROOT/verilog/rtl \
  -I $CARAVEL_ROOT/verilog \
  -I $CARAVEL_ROOT/verilog/rtl/mgmt_core_wrapper/verilog/rtl \
  -I $PDK_ROOT/sky130A \
  -y $CARAVEL_ROOT/verilog/rtl \
  -y $CARAVEL_ROOT/verilog/rtl/mgmt_core_wrapper/verilog/rtl \
  $CARAVEL_ROOT/verilog/gl/housekeeping.v \
  $PDK_ROOT/sky130A/libs.ref/sky130_fd_sc_hd/verilog/primitives.v \
  $PDK_ROOT/sky130A/libs.ref/sky130_fd_sc_hd/verilog/sky130_fd_sc_hd.v \
  $PDK_ROOT/sky130A/libs.ref/sky130_fd_io/verilog/sky130_fd_io.v \
  $CARAVEL_ROOT/verilog/dv/dummy_fill.v \
  $VEX_FILE \
  hkspi_tb.v -o hkspi_gls.vvp
```
<img width="844" height="341" alt="image" src="https://github.com/user-attachments/assets/f2329b94-4851-440b-9953-5611fa62965f" />

*Here `verilog/gl/housekeeping.v` is the gate‑level netlist; other blocks remain RTL.*

### 16.2 Run GLS simulation

```bash
vvp hkspi_gls.vvp | tee gls_hkspi.log
```
<img width="932" height="459" alt="image" src="https://github.com/user-attachments/assets/f64a3682-47f8-4727-a6c4-91e91a96a1c4" />


***

## Step 17: Compare RTL vs GLS results

### 17.1 Extract register reads

```bash
grep "Read register" rtl_hkspi.log > rtl_reads.txt
grep "Read register" gls_hkspi.log > gls_reads.txt
```
<img width="997" height="35" alt="image" src="https://github.com/user-attachments/assets/aa78ab1c-3e50-43e3-a909-498648dca68e" />


### 17.2 Diff the results

```bash
diff -s rtl_reads.txt gls_reads.txt
```

*If functional behavior matches, you'll see: **"Files rtl_reads.txt and gls_reads.txt are identical"**.*


<img width="983" height="92" alt="image" src="https://github.com/user-attachments/assets/1ee9896d-7209-4d30-99d5-aea5caee9042" />


***

## Step 18: errors

The error shows that `VexRiscv` module is not being found because you didn't include `$VEX_FILE` in your `iverilog` command. Looking at your command, the line breaks make it appear you may have accidentally omitted it.[1]

Here's the corrected command with `$VEX_FILE` properly included:

```bash
export VEX_FILE=$(find $CARAVEL_ROOT/verilog/rtl/mgmt_core_wrapper -name "VexRiscv*.v" | head -n 1)

iverilog -Ttyp \
  -DFUNCTIONAL -DSIM \
  -D USE_POWER_PINS \
  -D UNIT_DELAY=#1 \
  -I $CARAVEL_ROOT/verilog/dv/caravel/mgmt_soc \
  -I $CARAVEL_ROOT/verilog/dv/caravel \
  -I $CARAVEL_ROOT/verilog/rtl \
  -I $CARAVEL_ROOT/verilog \
  -I $CARAVEL_ROOT/verilog/rtl/mgmt_core_wrapper/verilog/rtl \
  -I $PDK_ROOT/sky130A \
  -y $CARAVEL_ROOT/verilog/rtl \
  -y $CARAVEL_ROOT/verilog/rtl/mgmt_core_wrapper/verilog/rtl \
  $PDK_ROOT/sky130A/libs.ref/sky130_fd_sc_hd/verilog/primitives.v \
  $PDK_ROOT/sky130A/libs.ref/sky130_fd_sc_hd/verilog/sky130_fd_sc_hd.v \
  $PDK_ROOT/sky130A/libs.ref/sky130_fd_io/verilog/sky130_fd_io.v \
  $CARAVEL_ROOT/verilog/dv/dummy_fill.v \
  $VEX_FILE \
  hkspi_tb.v -o hkspi_rtl.vvp
```


## What's happening

- `mgmt_core.v` instantiates the `VexRiscv` CPU module at line 8426
- The `VexRiscv` module definition is in a separate file (`VexRiscv*.v`) inside the cloned `mgmt_core_wrapper` directory
- Without explicitly listing `$VEX_FILE` in the compile command, iverilog cannot find the module definition

## Verify VexRiscv file exists

Before running, double-check the file was found:

```bash
echo $VEX_FILE
ls -lh $VEX_FILE
```

You should see a path like:  
`/home/vscode/caravel_vsd/caravel/verilog/rtl/mgmt_core_wrapper/verilog/rtl/VexRiscv.v`

If `$VEX_FILE` is empty, it means the `mgmt_core_wrapper` clone in Step 4 didn't complete properly. Re-run:

```bash
cd $CARAVEL_ROOT
rm -rf verilog/rtl/mgmt_core_wrapper
git clone https://github.com/efabless/caravel_mgmt_soc_litex verilog/rtl/mgmt_core_wrapper
```

Then try the export and compile again.

# WHY GLS Passed is not showing

You're getting "Test HK SPI **(RTL) Passed**" in your GLS simulation because the testbench message is hardcoded in `hkspi_tb.v` and doesn't change based on compilation defines.

## Why this happens

The monitor statement in `hkspi_tb.v` likely looks something like:

```verilog
$display("Monitor: Test HK SPI (RTL) Passed");
```

This string is fixed at the Verilog source level—it doesn't check whether you compiled with `-DGL` or used gate-level netlists.

## II How to confirm you're actually running GLS

Even though the message says "(RTL)", you **are** running a mixed RTL+GL simulation if:

1. **Your compile command included `$CARAVEL_ROOT/verilog/gl/housekeeping.v`** (the gate-level housekeeping netlist)
2. **The patched `caravel_netlists.v` commented out RTL housekeeping** to avoid duplicate modules

The functional behavior is what matters—the housekeeping SPI path is exercised through the GL netlist, backed by Sky130 standard cells.

## Verify you compiled GLS correctly

Check your compile command included the GL file:

```bash
grep "housekeeping.v" hkspi_gls.vvp
```

Or re-examine your iverilog command—it should have:

```bash
$CARAVEL_ROOT/verilog/gl/housekeeping.v \
```

not:

```bash
$CARAVEL_ROOT/verilog/rtl/housekeeping.v \
```


## Optional: Customize the testbench message

If you want the log to say "GLS Passed" for clarity, edit `hkspi_tb.v` before compiling GLS:

```bash
cp hkspi_tb.v hkspi_tb_gls.v
sed -i 's/Test HK SPI (RTL) Passed/Test HK SPI (GLS) Passed/g' hkspi_tb_gls.v
```

Then compile GLS using `hkspi_tb_gls.v` instead of `hkspi_tb.v`:

```bash
iverilog -Ttyp \
  -DFUNCTIONAL -DSIM \
  -D USE_POWER_PINS \
  -D UNIT_DELAY=#1 \
  -I $CARAVEL_ROOT/verilog/dv/caravel/mgmt_soc \
  -I $CARAVEL_ROOT/verilog/dv/caravel \
  -I $CARAVEL_ROOT/verilog/rtl \
  -I $CARAVEL_ROOT/verilog \
  -I $CARAVEL_ROOT/verilog/rtl/mgmt_core_wrapper/verilog/rtl \
  -I $PDK_ROOT/sky130A \
  -y $CARAVEL_ROOT/verilog/rtl \
  -y $CARAVEL_ROOT/verilog/rtl/mgmt_core_wrapper/verilog/rtl \
  $CARAVEL_ROOT/verilog/gl/housekeeping.v \
  $PDK_ROOT/sky130A/libs.ref/sky130_fd_sc_hd/verilog/primitives.v \
  $PDK_ROOT/sky130A/libs.ref/sky130_fd_sc_hd/verilog/sky130_fd_sc_hd.v \
  $PDK_ROOT/sky130A/libs.ref/sky130_fd_io/verilog/sky130_fd_io.v \
  $CARAVEL_ROOT/verilog/dv/dummy_fill.v \
  $VEX_FILE \
  hkspi_tb_gls.v -o hkspi_gls.vvp
```


## Bottom line

The message is cosmetic; the actual simulation **is** using GL housekeeping if you followed the compile steps correctly. The register read values matching between RTL and GLS logs proves functional equivalence, which is the real verification objective.[2][4][1]

## III.  Understanding the Commands

### What is iverilog?

Icarus Verilog (`iverilog`) is an open-source Verilog compiler that converts Verilog source code into an executable simulation format.

### What is vvp?

`vvp` (Verilog Simulation Runtime) executes the compiled `.vvp` file produced by iverilog.

### What is hkspi?

The housekeeping SPI (hkspi) is a 4-pin SPI interface in Caravel that allows external access to configuration registers, CPU control, and system monitoring.

### What does the hkspi test verify?

The test verifies:
- SPI communication protocol (Mode 0)
- Register read/write operations through housekeeping SPI
- Proper data transfer between host and management SoC
- Register values match expected configuration

***

## Quick Reference - Complete Workflow

Copy and paste these commands for a complete RTL simulation (assuming setup is done):

```bash
# Set environment
export CARAVEL_ROOT=$(pwd | sed 's|/verilog/dv/caravel/mgmt_soc/hkspi||')
export PDK_ROOT=$CARAVEL_ROOT/pdk
export PDK=sky130A
cd $CARAVEL_ROOT/verilog/dv/caravel/mgmt_soc/hkspi

# Set VexRiscv path
export VEX_FILE=$(find $CARAVEL_ROOT/verilog/rtl/mgmt_core_wrapper -name "VexRiscv*.v" | head -n 1)

# Compile
iverilog -Ttyp -DFUNCTIONAL -DSIM -D USE_POWER_PINS -D UNIT_DELAY=#1 \
  -I $CARAVEL_ROOT/verilog/dv/caravel/mgmt_soc \
  -I $CARAVEL_ROOT/verilog/dv/caravel \
  -I $CARAVEL_ROOT/verilog/rtl \
  -I $CARAVEL_ROOT/verilog \
  -I $CARAVEL_ROOT/verilog/rtl/mgmt_core_wrapper/verilog/rtl \
  -I $PDK_ROOT/sky130A \
  -y $CARAVEL_ROOT/verilog/rtl \
  -y $CARAVEL_ROOT/verilog/rtl/mgmt_core_wrapper/verilog/rtl \
  $PDK_ROOT/sky130A/libs.ref/sky130_fd_sc_hd/verilog/primitives.v \
  $PDK_ROOT/sky130A/libs.ref/sky130_fd_sc_hd/verilog/sky130_fd_sc_hd.v \
  $PDK_ROOT/sky130A/libs.ref/sky130_fd_io/verilog/sky130_fd_io.v \
  $CARAVEL_ROOT/verilog/dv/dummy_fill.v \
  $VEX_FILE \
  hkspi_tb.v -o hkspi.vvp

# Run simulation
vvp hkspi.vvp | tee rtl_hkspi.log

# Verify
grep "Monitor: Test HK SPI (RTL) Passed" rtl_hkspi.log
```

***

## Troubleshooting Checklist

If simulation fails, verify:

- [ ] You're in the correct directory (`pwd` shows `.../hkspi`)
- [ ] `hkspi.hex` exists in current directory (`ls hkspi.hex`)
- [ ] `$CARAVEL_ROOT` is set correctly (`echo $CARAVEL_ROOT`)
- [ ] `$VEX_FILE` points to VexRiscv file (`echo $VEX_FILE`)
- [ ] PDK files exist (`ls $PDK_ROOT/sky130A/libs.ref/`)
- [ ] mgmt_core_wrapper was cloned (`ls $CARAVEL_ROOT/verilog/rtl/mgmt_core_wrapper`)
- [ ] `caravel_netlists.v` was fixed (check with `grep "gl/" $CARAVEL_ROOT/verilog/rtl/caravel_netlists.v`)
- [ ] Compilation completed without errors

***

## Next Steps

After successful RTL simulation:

1. **Compare with GLS (Gate-Level Simulation)** to verify functional equivalence
2. **Run other Caravel DV tests** using similar methodology
3. **Modify test parameters** in `hkspi_tb.v` for custom verification
4. **Generate waveforms** by uncommenting `$dumpfile`/`$dumpvars` in testbench

***
# IV Challengens

1. Space Issue in the laptop
2. Done in the codespcae online
3. Taking so much time on each step
4. Completed the task on online codespace.
