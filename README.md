# VSD_Advanced_Physical_Design
# Day 1: Inception of open-source EDA, OpenLANE and Sky130 PDK #
## How to talk to computers ##
### Introduction to RISC V ###
* What is an Instruction Set Architecture?

The Instruction Set Architecture (ISA) defines the software specification for a hardware. ISA defines a set of instructions and how they behave, data types, registers, addressing modes, etc. 

RISC V is an open Instruction Set Architecture. It is a well organised ISA divided into categories and extensions. ARM architecture is commonly used for mobile phones and the x86 is used for personal computers.

* How it helps in bridging the gap between Software and Hardware?

Suppose we need to run a C program or any high level language program on a hardware. The main problem is that the hardware cannot understand the high level language and can understand only the machine level language i.e. binary language. It is here where the ISA comes to rescue. The compiler converts the high level language into a RISC V assembly level language. This is then converted into a machine level program which contains binary language. The hardware can understand this language and thus ISA helps in bridging the gap between Software and Hardware.

## SoC design and OpenLANE ##
There are three components of an ASIC design flow. They are RTL IP's, EDA tools (like SPICE, MAGIC, OpenLANE, etc.), PDK. PDK stands for Process Design Kit. It includes process design rules: DRC, LVS, etc., device models, digital standrd cell library, input output libraries.

### Simplified RTL to GDSII Flow ###
* Synthesis: Converts an RTL code written in HDL to a circuit out of components from the standard cell library.
* Floor/Power Planning: 
  * Chip Floor Planning: It is the process of partitioning the chip die between different system building blocks and place the i/o pads.
  * Macro Floor Planning: Here, we define the dimensions, pin locations, rows and routing tracks.
  * Power Planning: Here, the power network (Vdd and Ground) is constructed.
* Placement: Here, we place the cells on the floorplan rows, alligned with the sites. It is usually done in 2 steps:
  * Global Placement: Here, we find the optimal positions of the cells. These positions may not be legal.
  * Detailed Placement: The positions obtained from global placement are altered to obtain legal positions.
* Clock Tree Synthesis: Here, we create a clock distribution network to deliver the clock to all sequential elements with minimum skew.
* Routing: Here, we implement the interconnects using the available metal layers. The PDK defines the thickness, pitch, the minimum width and the vias.
  We use Sky130 PDK which has 6 metal layers. The lowest layer is called local interconnect layer and is made of Titanium nitrate. The following 5 metal layers are Aluminium layers.
* Physical Verification: It includes Design Rule Checking (DRC) and Layout vs Schematic (LVS).
* Timing Verification: Static Timing Analysis

### EDA tool used for the workshop ###

OpenLANE is the open source EDA tool used in the workshop. It is fully automated RTL2GDSII tool. Its main goal is to produce a GDSII with no human intervention, no LVS violations, no DRC violations, no timing violations. striVe is a family of open PDK, open EDA, open RTL.

It is tuned for SkyWater 130 nm open PDK. It can be used to harden the Macros and chips. It has two modes of operation: Autonomous or Interactive.

### OpenLANE ASIC design flow ###

The OpenLANE detailed ASIC design flow along with the tools used is as shown below:




## Getting familiar with open source EDA tools ##

### The Skywater PDK files: ###
As we have already seen, PDK stands for process design kit. The pdks directory has three subdirectories:

* skywater-pdk : It contains all PDK related files which provide information about timing library, tech lef, cell lef, etc.
* open_pdks : It contains a set of files to convert foundry level PDKs to be compatible with opensource EDA tools.
* sky130A : It contains the PDK which has been made compatible with OpenLANE environment.

The sky130A contains libs.ref (contains all the process specification files) and libs.tech (contains files specific to the tools).

### Invoking the OpenLANE tool ###
There are two modes in which the tool works.

* Automatic mode: ./flow.tcl -design <design_name>

* Interactive mode: 1. ./flow.tcl -interactive
                    2. package require openlane 0.9 ( importing the package )
                    3. prep -design <design_name> ( preparing the design)
                    
 invoking screenshot                   

### Preparing the design ###

Before synthesis, we need to prepare the file system, the data design setup stage which will be setting the data for our design. Merging of lef.py happens. The cell level lef file and the technology level lef files are merged.  

This picture shows the runs created after preparing the design:

This picture shows the merged.lef file created after preparing the design:


* config.tcl in runs folder shows which all are the default parameters taken by runs.
* cmds.log keeps the record of all the commands.

### Running the synthesis ###

To run the synthesis, the command run_synthesis must be given.

The below shown screen will appear after a successful synthesis:


### Flop ratio ###

It is the ratio of the number of D flip flops to the total number of cells.

The obtained number of gates after synthesising the design of picorv32a are as shown:



The flop ratio is found to be 0.09432 .

### Buffer ratio ###

It is the ratio of the number of buffers to the total number of cells.

The buffer ratio was found to be 0.1322 .



                
