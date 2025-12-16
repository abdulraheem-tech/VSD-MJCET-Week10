
## 📝 **COMPLETE MODIFICATION PLAN**

## Step 1: Made the Dummy_por module commented 
<img width="417" height="232" alt="image" src="https://github.com/user-attachments/assets/507fa0e4-e3dc-4f95-9017-78591453ccdd" />



### Step 2: In `rtl/vsdcaravel.v` (Top Module)

- Declare `resetn` as input

 width="229" height="80" alt="image" src="https://github.com/user-attachments/assets/5c144f4a-7434-44cd-add8-eba003b32c34" />


```verilog
assign porb_h = resetn;
assign porb_l = resetn;
assign por_l = ~resetn;

```

### Step 3: In `rtl\caravel_core,v`

- Declare `porb_l` as output port
- its in the SoC interface

<img width="310" height="244" alt="image" src="https://github.com/user-attachments/assets/d313e45c-1968-4866-a73e-5cdbea535b85" />


- Comment out or remove the dummy_por
<img width="336" height="221" alt="image" src="https://github.com/user-attachments/assets/aa1f9669-1e8f-40cf-a6b4-bf286422185a" />

### Step 4: In `rtl\caravel_netlist.v`

- Complete Comment out ``include "dummy_por.v"`
<img width="923" height="872" alt="image" src="https://github.com/user-attachments/assets/3ec7e6c1-e7c6-428d-970b-acfb8d1b0bd4" />

### Step 5: In `dv/hkspi/hkspi_tb.v`

- Declare `resetn` as register
  <img width="609" height="372" alt="image" src="https://github.com/user-attachments/assets/a09f451f-e03b-44ce-8037-43e27bfcc644" />
  
 Make the `resetn` intialize to `0`
- Increase the delay to `#20000` and `#10000`
- Then After `#20000` delay make the `resetn` as `1`

  <img width="607" height="288" alt="image" src="https://github.com/user-attachments/assets/4913bdb3-fdbc-44b7-bbfa-53aca93b28e6" />

- Instantiate the `resetn` in `vsdcaravel`
- 
- <img width="739" height="585" alt="image" src="https://github.com/user-attachments/assets/ead9847d-da23-497d-94a3-09ab0669f67a" />
#### Note: Change the `hkspi_tb.v` in gls as above

### Step 5: Test
```bash
cd dv/hkspi
make clean && make
./simv
gtkwave hkspi.vcd
```
<img width="1708" height="900" alt="waveform" src="https://github.com/user-attachments/assets/c8074d69-1d5e-496a-a240-51c572716dd7" />

<img width="796" height="908" alt="RTL Passed" src="https://github.com/user-attachments/assets/cff4ad8e-8c31-411f-bc8d-a94ebee7b0c6" />
All 19 SPI register reads succeed! 🎉


### Running Synthesis
- open a terminal and cd to the work folder i.e. [./synthesis/work_folder](./synthesis/work_folder)
- Run synthesis using following command

check synth.tcl file
```
# ========================================================================
# Synopsys DC Synthesis Script for vsdcaravel
# Modified to keep POR and Memory modules as complete RTL blackboxes
# ========================================================================

# ========================================================================
# Load technology libraries
# ========================================================================
read_db "/home/Synopsys/pdk/SCL_PDK_3/SCLPDK_V3.0_KIT/scl180/iopad/cio250/4M1L/liberty/tsl18cio250_min.db"
read_db "/home/Synopsys/pdk/SCL_PDK_3/SCLPDK_V3.0_KIT/scl180/stdcell/fs120/4M1IL/liberty/lib_flow_ff/tsl18fs120_scl_ff.db"

# ========================================================================
# Set library variables
# ========================================================================
set target_library "/home/Synopsys/pdk/SCL_PDK_3/SCLPDK_V3.0_KIT/scl180/iopad/cio250/4M1L/liberty/tsl18cio250_min.db /home/Synopsys/pdk/SCL_PDK_3/SCLPDK_V3.0_KIT/scl180/stdcell/fs120/4M1IL/liberty/lib_flow_ff/tsl18fs120_scl_ff.db"
set link_library "* /home/Synopsys/pdk/SCL_PDK_3/SCLPDK_V3.0_KIT/scl180/iopad/cio250/4M1L/liberty/tsl18cio250_min.db /home/Synopsys/pdk/SCL_PDK_3/SCLPDK_V3.0_KIT/scl180/stdcell/fs120/4M1IL/liberty/lib_flow_ff/tsl18fs120_scl_ff.db"
set_app_var target_library $target_library
set_app_var link_library $link_library

# ========================================================================
# Define directory paths
# ========================================================================
set root_dir "/home/maraheem/raheem/task3/vsdRiscvScl180"
set io_lib "/home/Synopsys/pdk/SCL_PDK_3/SCLPDK_V3.0_KIT/scl180/iopad/cio250/4M1L/verilog/tsl18cio250/zero"
set verilog_files "$root_dir/rtl"
set top_module "vsdcaravel"
set output_file "$root_dir/synthesis/output/vsdcaravel_synthesis.v"
set report_dir "$root_dir/synthesis/report"

# ========================================================================
# Configure Blackbox Handling
# ========================================================================
# Prevent automatic memory inference and template saving
set_app_var hdlin_infer_multibit default_none
set_app_var hdlin_auto_save_templates false
set_app_var compile_ultra_ungroup_dw false

# ========================================================================
# Create Blackbox Stub File for Memory and POR Modules
# ========================================================================
set blackbox_file "$root_dir/synthesis/memory_por_blackbox_stubs.v"
set fp [open $blackbox_file w]
puts $fp "// Blackbox definitions for memory and POR modules"
puts $fp "// Auto-generated by synthesis script"
puts $fp ""

# RAM128 blackbox
puts $fp "(* blackbox *)"
puts $fp "module RAM128(CLK, EN0, VGND, VPWR, A0, Di0, Do0, WE0);"
puts $fp "  input CLK, EN0, VGND, VPWR;"
puts $fp "  input \[6:0\] A0;"
puts $fp "  input \[31:0\] Di0;"
puts $fp "  input \[3:0\] WE0;"
puts $fp "  output \[31:0\] Do0;"
puts $fp "endmodule"
puts $fp ""

# RAM256 blackbox
puts $fp "(* blackbox *)"
puts $fp "module RAM256(VPWR, VGND, CLK, WE0, EN0, A0, Di0, Do0);"
puts $fp "  input CLK, EN0;"
puts $fp "  inout VPWR, VGND;"
puts $fp "  input \[7:0\] A0;"
puts $fp "  input \[31:0\] Di0;"
puts $fp "  input \[3:0\] WE0;"
puts $fp "  output \[31:0\] Do0;"
puts $fp "endmodule"
puts $fp ""

close $fp
puts "INFO: Created blackbox stub file: $blackbox_file"

# ========================================================================
# Read RTL Files
# ========================================================================
# Read defines first
read_file $verilog_files/defines.v

# Read blackbox stubs FIRST (before actual RTL)
puts "INFO: Reading memory and POR blackbox stubs..."
read_file $blackbox_file -format verilog

# ========================================================================
# Read RTL files excluding memory and POR modules
# ========================================================================
puts "INFO: Building RTL file list (excluding RAM128.v, RAM256.v, and dummy_por.v)..."

# Get all verilog files
set all_rtl_files [glob -nocomplain ${verilog_files}/*.v]

# Define files to exclude
set exclude_files [list \
    "${verilog_files}/RAM128.v" \
    "${verilog_files}/RAM256.v" \
    "${verilog_files}/dummy_por.v" \
]

# Build list of files to read
set rtl_to_read [list]
foreach file $all_rtl_files {
    set excluded 0
    foreach excl_file $exclude_files {
        if {[string equal $file $excl_file]} {
            set excluded 1
            puts "INFO: Excluding $file (using blackbox instead)"
            break
        }
    }
    if {!$excluded} {
        lappend rtl_to_read $file
    }
}

puts "INFO: Reading [llength $rtl_to_read] RTL files..."

# Read all RTL files EXCEPT RAM128.v, RAM256.v, and dummy_por.v
read_file $rtl_to_read -define USE_POWER_PINS -format verilog

# ========================================================================
# Elaborate Design
# ========================================================================
puts "INFO: Elaborating design..."
elaborate $top_module

# ========================================================================
# Set Blackbox Attributes for Memory Modules
# ========================================================================
puts "INFO: Setting Blackbox Attributes for Memory Modules..."

# Mark RAM128 as blackbox
if {[sizeof_collection [get_designs -quiet RAM128]] > 0} {
    set_attribute [get_designs RAM128] is_black_box true -quiet
    set_dont_touch [get_designs RAM128]
    puts "INFO: RAM128 marked as blackbox"
}

# Mark RAM256 as blackbox
if {[sizeof_collection [get_designs -quiet RAM256]] > 0} {
    set_attribute [get_designs RAM256] is_black_box true -quiet
    set_dont_touch [get_designs RAM256]
    puts "INFO: RAM256 marked as blackbox"
}

# ========================================================================
# Set POR (Power-On-Reset) Module as Blackbox
# ========================================================================
puts "INFO: Setting POR module as blackbox..."

# Mark dummy_por as blackbox
if {[sizeof_collection [get_designs -quiet dummy_por]] > 0} {
    set_attribute [get_designs dummy_por] is_black_box true -quiet
    set_dont_touch [get_designs dummy_por]
    puts "INFO: dummy_por marked as blackbox"
}

# Handle any other POR-related modules (case insensitive)
foreach_in_collection por_design [get_designs -quiet "*por*"] {
    set design_name [get_object_name $por_design]
    if {![string equal $design_name "dummy_por"]} {
        set_dont_touch $por_design
        set_attribute $por_design is_black_box true -quiet
        puts "INFO: $design_name set as blackbox"
    }
}

# ========================================================================
# Protect blackbox instances from optimization
# ========================================================================
puts "INFO: Protecting blackbox instances from optimization..."

# Protect all instances of RAM128, RAM256, and dummy_por
foreach blackbox_ref {"RAM128" "RAM256" "dummy_por"} {
    set instances [get_cells -quiet -hierarchical -filter "ref_name == $blackbox_ref"]
    if {[sizeof_collection $instances] > 0} {
        set_dont_touch $instances
        set inst_count [sizeof_collection $instances]
        puts "INFO: Protected $inst_count instance(s) of $blackbox_ref"
    }
}

# ========================================================================
# Link Design
# ========================================================================
puts "INFO: Linking design..."
link

# ========================================================================
# Uniquify Design
# ========================================================================
puts "INFO: Uniquifying design..."
uniquify

# ========================================================================
# Read SDC constraints (if exists)
# ========================================================================
if {[file exists "$root_dir/synthesis/vsdcaravel.sdc"]} {
    puts "INFO: Reading timing constraints..."
    read_sdc "$root_dir/synthesis/vsdcaravel.sdc"
}

# ========================================================================
# Compile Design (Basic synthesis)
# ========================================================================
puts "INFO: Starting compilation..."
compile_ultra -incremental

# ========================================================================
# Write Outputs
# ========================================================================
puts "INFO: Writing output files..."

# Write Verilog netlist
write -format verilog -hierarchy -output $output_file
puts "INFO: Netlist written to: $output_file"

# Write DDC format for place-and-route
write -format ddc -hierarchy -output "$root_dir/synthesis/output/vsdcaravel_synthesis.ddc"
puts "INFO: DDC written to: $root_dir/synthesis/output/vsdcaravel_synthesis.ddc"

# Write SDC with actual timing constraints
write_sdc "$root_dir/synthesis/output/vsdcaravel_synthesis.sdc"
puts "INFO: SDC written to: $root_dir/synthesis/output/vsdcaravel_synthesis.sdc"

# ========================================================================
# Generate Reports
# ========================================================================
puts "INFO: Generating reports..."

report_area > "$report_dir/area.rpt"
report_power > "$report_dir/power.rpt"
report_timing -max_paths 10 > "$report_dir/timing.rpt"
report_constraint -all_violators > "$report_dir/constraints.rpt"
report_qor > "$report_dir/qor.rpt"

# Report on blackbox modules
puts "INFO: Generating blackbox module report..."
set bb_report [open "$report_dir/blackbox_modules.rpt" w]
puts $bb_report "========================================"
puts $bb_report "Blackbox Modules Report"
puts $bb_report "========================================"
puts $bb_report ""

foreach bb_module {"RAM128" "RAM256" "dummy_por"} {
    puts $bb_report "Module: $bb_module"
    set instances [get_cells -quiet -hierarchical -filter "ref_name == $bb_module"]
    if {[sizeof_collection $instances] > 0} {
        puts $bb_report "  Status: PRESENT"
        puts $bb_report "  Instances: [sizeof_collection $instances]"
        foreach_in_collection inst $instances {
            puts $bb_report "    - [get_object_name $inst]"
        }
    } else {
        puts $bb_report "  Status: NOT FOUND"
    }
    puts $bb_report ""
}
close $bb_report
puts "INFO: Blackbox report written to: $report_dir/blackbox_modules.rpt"

# ========================================================================
# Summary
# ========================================================================
puts ""
puts "INFO: ========================================"
puts "INFO: Synthesis Complete!"
puts "INFO: ========================================"
puts "INFO: Output netlist: $output_file"
puts "INFO: DDC file: $root_dir/synthesis/output/vsdcaravel_synthesis.ddc"
puts "INFO: SDC file: $root_dir/synthesis/output/vsdcaravel_synthesis.sdc"
puts "INFO: Reports directory: $report_dir"
puts "INFO: Blackbox stub file: $blackbox_file"
puts "INFO: "
puts "INFO: NOTE: The following modules are preserved as blackboxes:"
puts "INFO:   - RAM128 (Memory macro)"
puts "INFO:   - RAM256 (Memory macro)"
puts "INFO:   - dummy_por (Power-On-Reset circuit)"
puts "INFO: These modules will need to be replaced with actual macros during P&R"
puts "INFO: ========================================"

# Exit dc_shell
# dc_shell> exit
```
```

```
dc_shell -f ../synth.tcl | tee status.log
gui_show
```
<img width="1765" height="896" alt="loadingfiles" src="https://github.com/user-attachments/assets/985af94b-64c3-4803-8d0c-4a267a0b53e8" />
<img width="1137" height="469" alt="synthesis completed" src="https://github.com/user-attachments/assets/d5256ac9-1a58-4e95-b37f-a4a48c0f1107" />
<img width="1527" height="856" alt="vsdcaravel" src="https://github.com/user-attachments/assets/b5c72553-3524-4fcd-8296-89f15cc71d8d" />
<img width="1310" height="803" alt="guichipcore" src="https://github.com/user-attachments/assets/4cbb2601-d99c-4f4a-bedf-6ad661d552ce" />

This should update the caravel_snthesis.v file in ./synthesis/output folder, No Dummy_por

<img width="877" height="846" alt="image" src="https://github.com/user-attachments/assets/f1bee3e2-8edf-4926-a307-cf1fa50ec996" />
## Reports
After synthesis report files will be generated in the report directory such as area, power, timing, constraints, qor and blackbox_modules reports.
<img width="761" height="433" alt="image" src="https://github.com/user-attachments/assets/772cc9b5-9c2e-4798-b928-25910ea87cb5" />

### power report
<img width="1680" height="1050" alt="image" src="https://github.com/user-attachments/assets/72f98fcc-b944-466a-ae7b-b880f9cc91d2" />

### Area report

<img width="1018" height="938" alt="image" src="https://github.com/user-attachments/assets/33eb6a31-b610-4458-9190-04ca164e8dec" />

### timing report
<img width="1018" height="938" alt="image" src="https://github.com/user-attachments/assets/8f834229-77a3-4968-9f3e-1d6e79fe5ff7" />
<img width="1018" height="938" alt="image" src="https://github.com/user-attachments/assets/e26caa31-5955-4ad8-8fd1-04bea607555a" />

### qor report

<img width="1018" height="938" alt="image" src="https://github.com/user-attachments/assets/491cafaf-0c6d-4b2c-8648-15be5e1dacf5" />

<img width="1018" height="938" alt="image" src="https://github.com/user-attachments/assets/40bbdef0-449b-4b95-b54e-d67324f1ef6e" />

### Blackbox_modules

<img width="522" height="402" alt="image" src="https://github.com/user-attachments/assets/1713ba22-98d8-40b4-bd4d-581ae3ffc92a" />

# GLS Setup

- As we remvoed dummy_por completely so we don't need to include it again, instead have to declare the new external `resetn` in the `hkspi_tb.v`
- I have used the following Makefile


```
# SPDX-FileCopyrightText: 2020 Efabless Corporation
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
# http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.
#
# SPDX-License-Identifier: Apache-2.0

scl_io_PATH = "/home/Synopsys/pdk/SCL_PDK_3/SCLPDK_V3.0_KIT/scl180/iopad/cio250/6M1L/verilog/tsl18cio250/zero"
scl_io_wrapper_PATH = ../rtl/scl180_wrapper
VERILOG_PATH = ../
RTL_PATH = $(VERILOG_PATH)/rtl
GL_PATH = $(VERILOG_PATH)/gl
BEHAVIOURAL_MODELS = ../gls
RISCV_TYPE ?= rv32imc
PDK_PATH = /home/Synopsys/pdk/SCL_PDK_3/SCLPDK_V3.0_KIT/scl180/stdcell/fs120/4M1IL/verilog/vcs_sim_model 
FIRMWARE_PATH = ../gls
GCC_PATH?=/home/maraheem/riscv32-unknown-elf/bin
GCC_PREFIX?=riscv32-unknown-elf

SIM_DEFINES = +define+FUNCTIONAL +define+SIM
SIM?=gl

.SUFFIXES:

PATTERN = hkspi

all: ${PATTERN:=.vcd}
hex: ${PATTERN:=.hex}
vcd: ${PATTERN:=.vcd}

# VCS compilation target
simv: ${PATTERN}_tb.v ${PATTERN}.hex
	 vcs -full64 -debug_access+all \
	 $(SIM_DEFINES) +define+GL \
	 -timescale=1ns/1ps \
	 +v2k -sverilog \
	 -lca -kdb \
	 +incdir+$(VERILOG_PATH) \
	 +incdir+$(VERILOG_PATH)/synthesis/output \
	 +incdir+$(BEHAVIOURAL_MODELS) \
	 +incdir+$(RTL_PATH) \
	 +incdir+$(GL_PATH) \
	 +incdir+$(scl_io_wrapper_PATH) \
	 +incdir+$(scl_io_PATH) \
	 +incdir+$(PDK_PATH) \
	 -y $(scl_io_wrapper_PATH) +libext+.v+.sv \
	 -y $(RTL_PATH) +libext+.v+.sv \
	 -y $(GL_PATH) +libext+.v+.sv \
	 -y $(scl_io_PATH) +libext+.v+.sv \
	 -y $(PDK_PATH) +libext+.v+.sv \
	 $(GL_PATH)/defines.v \
	 $< \
	 -l vcs_compile.log \
	 -o simv

# Run simulation and generate VCD
%.vcd: simv
	 ./simv +vcs+dumpvars+${PATTERN}.vcd \
	 -l simulation.log

# Alternative: Generate FSDB waveform (if Verdi is available)
%.fsdb: simv
	 ./simv -ucli -do "dump -file ${PATTERN}.fsdb -type fsdb -add {*}" \
	 -l simulation.log

%.elf: %.c $(FIRMWARE_PATH)/sections.lds $(FIRMWARE_PATH)/start.s
	 ${GCC_PATH}/${GCC_PREFIX}-gcc -march=$(RISCV_TYPE) -mabi=ilp32 -Wl,-Bstatic,-T,$(FIRMWARE_PATH)/sections.lds,--strip-debug -ffreestanding -nostdlib -o $@ $(FIRMWARE_PATH)/start.s $<

%.hex: %.elf
	 ${GCC_PATH}/${GCC_PREFIX}-objcopy -O verilog $< $@ 
 # to fix flash base address
	 sed -i 's/@10000000/@00000000/g' $@

%.bin: %.elf
	 ${GCC_PATH}/${GCC_PREFIX}-objcopy -O binary $< /dev/stdout | tail -c +1048577 > $@

# Interactive debug with DVE
debug: simv
	 ./simv -gui -l simulation.log

# Coverage report generation (optional)
coverage: simv
	 ./simv -cm line+cond+fsm+tgl -cm_dir coverage.vdb
	 urg -dir coverage.vdb -report urgReport

check-env:
ifeq (,$(wildcard $(GCC_PATH)/$(GCC_PREFIX)-gcc ))
	 $(error $(GCC_PATH)/$(GCC_PREFIX)-gcc is not found, please export GCC_PATH and GCC_PREFIX before running make)
endif

clean:
	 rm -f *.elf *.hex *.bin *.vcd *.fsdb *.log simv
	 rm -rf csrc simv.daidir DVEfiles ucli.key *.vpd urgReport coverage.vdb AN.DB

.PHONY: clean hex vcd fsdb all debug coverage check-env# SPDX-FileCopyrightText: 2020 Efabless Corporation

```


The following commands
- make clean
- Then copy the hkspi.hex file from dv/hkspi
- the compile
- Then run `make`
<img width="1801" height="490" alt="GLcommad" src="https://github.com/user-attachments/assets/379d7835-7b8a-4e90-b60a-895e86cffb9e" />

<img width="975" height="697" alt="GLSrecompiling" src="https://github.com/user-attachments/assets/d717ba04-752b-4354-aea6-a9960f675f08" />

<img width="926" height="690" alt="GLS passed" src="https://github.com/user-attachments/assets/ead78e69-8530-45e1-99e8-0a499a97a1c9" />
















