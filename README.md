# VSD_Advanced_Physical_Design
# Day 1: Inception of open-source EDA, OpenLANE and Sky130 PDK #
## How to talk to computers ##
### Introduction to RISC V ###
* What is an Instruction Set Architecture?

The Instruction Set Architecture (ISA) defines the software specification for a hardware. ISA defines a set of instructions and how they behave, data types, registers, addressing modes, etc. 

RISC V is an open Instruction Set Architecture. It is a well organised ISA divided into categories and extensions. ARM architecture is commonly used for mobile phones and the x86 is used for personal computers.

* How it helps in bridging the gap between Software and Hardware?

Suppose we need to run a C program or any high level language program on a hardware. The main problem is that the hardware cannot understand the high level language and can understand only the machine level language i.e. binary language. It is here where the ISA comes to rescue. The compiler converts the high level language into a RISC V assembly level language. This is then converted into a machine level program which contains binary language. The hardware can understand this language and thus ISA helps in bridging the gap between Software and Hardware.

![RISC V](https://github.com/SameerSDurgoji/VSD_Advanced_Physical_Design/blob/main/Day%201/Screenshot_6.png)

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
![Open Lane flow](https://github.com/SameerSDurgoji/VSD_Advanced_Physical_Design/blob/main/Day%201/openlane%20flow.png)



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
                    
![invoking screenshot](https://github.com/SameerSDurgoji/VSD_Advanced_Physical_Design/blob/main/Day%201/invoking%20openlane.png)                   

### Preparing the design ###

Before synthesis, we need to prepare the file system, the data design setup stage which will be setting the data for our design. Merging of lef.py happens. The cell level lef file and the technology level lef files are merged.  

This picture shows the runs and the merged.lef file created after preparing the design:
![runs](https://github.com/SameerSDurgoji/VSD_Advanced_Physical_Design/blob/main/Day%201/runs%20and%20the%20merged.lef%20created%20after%20preparing_LI.jpg)

* config.tcl in runs folder shows which all are the default parameters taken by runs.
* cmds.log keeps the record of all the commands.

### Running the synthesis ###

To run the synthesis, the command run_synthesis must be given.

The below shown screen will appear after a successful synthesis:
![synthesis](https://github.com/SameerSDurgoji/VSD_Advanced_Physical_Design/blob/main/Day%201/synthesis%20successful.png)


### Flop ratio ###

It is the ratio of the number of D flip flops to the total number of cells.

The obtained number of gates after synthesising the design of picorv32a are as shown:
![flop ratio](https://github.com/SameerSDurgoji/VSD_Advanced_Physical_Design/blob/main/Day%201/Flop%20Ratio%20question_LI.jpg)


The flop ratio is found to be 0.09432 .

### Buffer ratio ###

It is the ratio of the number of buffers to the total number of cells.

The buffer ratio was found to be 0.1322 .


# Day 2:  Understand importance of good floorplan vs bad floorplan and introduction to library cells #

## Chip Floor Planning Considerations ##
The following are the considerations of a Chip Floorplan:
* Utilization Factor
* Aspect Ratio
* Location of preplaced cells
* Decoupling capacitors
* Power planning
* Pin placement
* Logical cell placement blockage

### Utilization Factor ###

First step in the physical design flow is to define the height and width of die and core.

Utilization Factor is the ratio of area occupied by the netlist to the total area of the core.

A utilization factor of 1 signifies that the entire area of the core is occupied by the netlist. However, practically we wont have a utilization factor of 1.

### Aspect Ratio ###

Aspect ratio is the ratio of the height of the core to width of the core.

Aspect ratio signifies whether the core is rectangle or square. An aspect ratio of 1 signifies that the core is a square.

### Location of the preplaced cells ###

Preplaced cells are the Macros or IPs which is a piece of complex logic and is being used multiple times. The arrangement of these IPs is included in floorplanning. These blocks have user defined locations and hence are placed in chip before automated placement and routing. These preplaced cells will be placed depending on the design scenario.

### Decoupling Capacitors ###

Due to the resistance and the inductance of the power supply wire, the power supplies Vdd and Vss would be dropped by some value to Vdd' and Vss' near the cells. If Vdd' goes below noise margin, the logic 1 at the output of the cell will not be detected as logic 1.

Solution: Adding the decoupling capacitors parallel with the circuit. Everytime the circuit switches, it draws current from the Capacitor and the RL network is used to replenish the charge into The capacitor. Hence it decouples the circuit from the supply.

### Power Planning ###

There is a chance of noise getting introduced in the circuit if the voltage droop and ground bounce exceed the noise margin. This problem is due to a single power supply. So multiple power supplies are needed so that if any logic demands current, it takes current from the nearest power supply. 

The image explaining this is shown below:
![Power Planning](https://github.com/SameerSDurgoji/VSD_Advanced_Physical_Design/blob/main/Day%202%20lab/power%20planning%204.png)


### Pin Placement ###

The connectivity information between the gates is coded using VHDL or verilog language and is called as a netlist. The area between the die and the core is used for placing the I/O pins.

### Logical Cell Placement Blockage ###

The area between the die and core is used to fill pin information and is blocked for the placement and routing tool as it is reserved for pin placement.

### Running the floorplan on OpenLANE tool ###

To run the floorplan, the command run_floorplan must be given. 
                

Below is a screenshot of the floorplan.tcl file and the ioPlacer.log file :
![floorplan.tcl](https://github.com/SameerSDurgoji/VSD_Advanced_Physical_Design/blob/main/Day%202%20lab/floorplan.tcl%20file%20and%20the%20ioPlacer.log%20file%20in%20runs.png)


We find the area occupied by the cell in the file named picorv32a.floorplan.def . The screenshot of that file is as shown:
![picorv32a.ploorplan.def](https://github.com/SameerSDurgoji/VSD_Advanced_Physical_Design/blob/main/Day%202%20lab/picorv32a.floorplan.def%20file.png)


Given below are the different variables and their default values in floorplanning. It can be found in the README.md file in the configuration directory.
![Readme.md](https://github.com/SameerSDurgoji/VSD_Advanced_Physical_Design/blob/main/Day%202%20lab/Different%20variables%20and%20their%20default%20values%20in%20floorplanning.png)



Here we can find that the Lower Left coordinates of the Die are: (0 0) ; the Upper Right coordinates of the die are: (1057235 806405).

To open the layout in magic, the following command must be typed:

magic -T <tech file location> lef read <lef file location> def read <def file location>

The screenshot of the layout opened in MAGIC tool is as shown:
![layout in magic](https://github.com/SameerSDurgoji/VSD_Advanced_Physical_Design/blob/main/Day%202%20lab/Layout%20opened%20in%20Magic.png)



Here we can observe that the input pins are placed equidistant to each other. The decoupling capacitor cells are arranged in the boundary of the core. Also, the standard cells are kept in the lower left corner.

![Zoom in](https://github.com/SameerSDurgoji/VSD_Advanced_Physical_Design/blob/main/Day%202%20lab/ip%20pins%2C%20decap%20cells.png)

## Placement ##

We now have the floorplan, netlist and the cell library. The next step is to place the netlist in the floorplan using the library.

Next the placement has to be optimized. This is the stage where we estimate the wire lengths and based on that, insert repeaters. To maintain the signal integrity, repeaters are used. Repeaters are basically buffers that will recondition the original signal, make a new signal replecatind the original one. But inturn this will consume area.

### Running the placement on OpenLANE tool ###

There are two stages of placement namely global placement and detailed placement.

The main goal in placement is to reduce the HPWL. HPWL is the half parametric wire length.

The command run_placement has to be given.

After a successful placement, we can open the layout in the MAGIC tool using the command:

magic -T <tech file location> lef read <lef file location> def read <def file location>
 
The screenshot of the layout after placement is as shown below:
![placement layout](https://github.com/SameerSDurgoji/VSD_Advanced_Physical_Design/blob/main/Day%202%20lab/Placement_Magic.png)



## Cell Design Flow ##

A standard cell library has the cells with different sizes, different functionality and also with different threshold voltage. It contains the information about the width, height, delay, etc of each cell in it.

The cell design flow is as follows:

* Inputs: Process Design Kits (PDKs), DRC and LVS rules, SPICE Models, Library and user defined specs.
* Design Steps: Circuit design, Layout Design, Characterization.
* Outputs: CDL (Circuit Description Language), GDSII, LEF, extracted spice netlist (.cir), timing, noise, power .libs, functions.

### Characterization Flow ###

The characterization flow is as follows:

1. Read the model files.
2. Read the extracted spice netlist.
3. Recognize the behaviour of the circuit.
4. Read the subcircuit files.
5. Attach the necessary power supplies.
6. Apply the stimulus.
7. Provide necessary output capacitance.
8. Provide necessary simulation commands.

These are to be fed as a configuration file to the characterization software called GUNA. The software will generate timing, noise, power .libs, functions.

There are 3 types of Characterization:
* Timing Characterization
* Power Characterization
* Noise Characterization

### Timing Charcterization: ###

1. slew_low_rise_thr          20%
2. slew_high_rise_thr         80%
3. slew_low_fall_thr          20%
4. slew_high_fall_thr         80%
5. in_rise_thr                50%
6. in_fall_thr                50%
7. out_rise_thr               50%
8. out_fall_thr               50%

# Day 3: Design library cell using Magic Layout and ngspice characterization #

### IO Placer Modes ###
There are totally 4 strategies of the pin placement supported by IO placer.

Initially, it was set to 1 where the pins were equidistant to each other.We can find the default setting in the floorplan.tcl file. The snapshots of it are as follows:
![strategy1](https://github.com/SameerSDurgoji/VSD_Advanced_Physical_Design/blob/main/Day%203%20lab/io%20strategy%201.png)
![mode1](https://github.com/SameerSDurgoji/VSD_Advanced_Physical_Design/blob/main/Day%203%20lab/MODE%201.png)



To change the mode on fly, the following command has to be typed set ::env(FP_IO_MODE) <Mode_number>. On changing the mode number to 2, the following result was obtained:
![stategy2](https://github.com/SameerSDurgoji/VSD_Advanced_Physical_Design/blob/main/Day%203%20lab/io%20strategy%202.png)
![mode2](https://github.com/SameerSDurgoji/VSD_Advanced_Physical_Design/blob/main/Day%203%20lab/MODE%202.png)




It was observed that the pins were stacked.

### Cloning the vsdstdcelldesign repo from Github to a local machine ###

This will copy all the .lib and .mag files from the Git repo to the local machine.

The snapshot of the cloning process is shown below:
![cloning](https://github.com/SameerSDurgoji/VSD_Advanced_Physical_Design/blob/main/Day%203%20lab/cloning.png)




## SPICE deck creation for a CMOS Inverter ##

First step to charaterize a gate is to create a SPICE deck. A SPICE deck contains information about the following :

* Component connctivity
* Component values (the dimensions of MOSFETs, input gate voltage, supply voltage, capacitor values, etc..)
* Location and names of the nodes.

### Switching Threshold, Vm ###

Vm is the point where Vin = Vout. At this point, both the NMOSFET and the PMOSFET will be in the saturation region.

## Inception of Layout - CMOS fabrication process ##

### 16 Mask CMOS process ###

1. Selection of the substrate:

A suitable substrate is chosen with appropriate resistance, doping level and orientation. It should be kept in mind that the substrate doping level should be less than well doping. Eg. P type substrate, high resistivity(5-50 ohms), doping level(10^15 per sq.cm), orientation(100).

2. Creating active region for transistors:

On the P substrate, 40nm of SiO2 and 80nm of Si3N4, 1um of photoresist is deposited. **Mask 1** is used to cover the part of photoresist below which active region is to be created. After exposing to UV light, the mask will be removed. The Si3N4 will be etched out and then the photoresist is removed. This is the placed in oxidation furnace and field oxide is grown.

3. N well and P well formation:
4. Formation of gate:
5. Lightly Doped Drain formation:
6. Source & Drain formation:
7. Steps to form Contacts and Interconnects:
8. Higher level metal formation:

## Lab Exercises ##

### Opening the inverter layout on the MAGIC tool ###

To open the inverter layout on the MAGIC tool, the command magic -T <tech_file> <mag_file> has to be given.
![opening in magic](https://github.com/SameerSDurgoji/VSD_Advanced_Physical_Design/blob/main/Day%203%20lab/opening%20inv%20layout%20in%20magic.png)

Following are the snapshots of the layout:
![inv_layout](https://github.com/SameerSDurgoji/VSD_Advanced_Physical_Design/blob/main/Day%203%20lab/inv%20layout%20opened%20in%20magic.png)


### Extraction of .spice file and running it ###

To extract the .spice file from the MAGIC tool, including all the parasitic capacitances, the following codes has to be given in the MAGIC console:
![extraction](https://github.com/SameerSDurgoji/VSD_Advanced_Physical_Design/blob/main/Day%203%20lab/extraction%20of%20.spice%20file.png)

The extracted spice file is opened and necessary things are added so as to run the file in the Ng SPICE tool. The model files are included. The model name is updated. The power supply are added. A pulse input is given at the gate. The snapshot of the updated spice file is shown below:
![edited spice](https://github.com/SameerSDurgoji/VSD_Advanced_Physical_Design/blob/main/Day%203%20lab/final%20spice%20deck.png)


Below is the snapshot of the PMOSFET model file:

![PMOS model](https://github.com/SameerSDurgoji/VSD_Advanced_Physical_Design/blob/main/Day%203%20lab/pmos%20model%20file.png)


Below is the snapshot of the NMOSFET model file:

![NMOS model](https://github.com/SameerSDurgoji/VSD_Advanced_Physical_Design/blob/main/Day%203%20lab/nmos%20model%20file.png)


To run it on the Ng SPICE tool, the following command is to be given:

ngspice <file_name.spice>

![running spice](https://github.com/SameerSDurgoji/VSD_Advanced_Physical_Design/blob/main/Day%203%20lab/running%20the%20spice%20deck.png)


The transient response is obtained by running ' plot y vs time a '. The transient response is obtained as follows:
![transient](https://github.com/SameerSDurgoji/VSD_Advanced_Physical_Design/blob/main/Day%203%20lab/obtained%20transient%20response.png)





The rise tranistion time is calculated by finding the difference between the time taken to reach 80% of the output from the 20% of the output. The snapshot below shows the time at the two positions. It was calculated to be

![transition](https://github.com/SameerSDurgoji/VSD_Advanced_Physical_Design/blob/main/Day%203%20lab/transition%20time.png)


The cell rise propagation delay is calculated as the difference of the time taken to reach 50% of the input to 50% of the output. The snapshot below shows the time at two positions. It was calculated to be 

![propagation](https://github.com/SameerSDurgoji/VSD_Advanced_Physical_Design/blob/main/Day%203%20lab/propagation%20delay.png)


# Day 4: Pre-layout timing analysis and importance of good clock tree #

### About Tracks ###

Tracks are used in the process of routing. We can find a track.info file in the address : pdks/sky130A/libs.tech/ sky130_fd_sc_hd/ .

A screenshot of the contents of the file is as shown below:

![track.info file](https://github.com/SameerSDurgoji/VSD_Advanced_Physical_Design/blob/main/Day%204%20lab/track.info%20file.png)

This file contains information about the horizontal and vertical offsets and pitches of each metal layer.

Some guidelines while making the standard cells :

* The input and output ports must lie on the intersection of the vertical and horizontal tracks.
* The width of the standard cells must be odd multiples of the track pitch.
* The height must be odd multiples of track vertical pitch.

In the below picture, we can see that the ports are located at the intersection of the tracks.

![ports at intersection](https://github.com/SameerSDurgoji/VSD_Advanced_Physical_Design/blob/main/Day%204%20lab/track%20file%20and%20the%20port%20on%20intersection.png)


### Extracting the lef file ###

For PnR, we will not be needing all the details of the logic of the cell; we would be requiring only their boundary, the power and GND rails, the input and output port information. Here, the LEF file comes into the picture which would contain only the required information.

So, the next step would be to extract LEF file out of the mag file.

For extracting the lef file, the following command must be given in the magic console: lef write

![extracting lef file](https://github.com/SameerSDurgoji/VSD_Advanced_Physical_Design/blob/main/Day%204%20lab/extracting%20the%20lef%20file.png)


This is the screenshot of the contents of the lef file.

![contents of lef](https://github.com/SameerSDurgoji/VSD_Advanced_Physical_Design/blob/main/Day%204%20lab/contents%20of%20lef%20file.png)

## Delay Tables ##

Delay table is the table of the delays of a gate for various input slew and the output load.

For a 2 level clock buffering, the following were observed:
* At every level, each node must drive the same load.
* At a given level, the buffers must be identical.
Only then the total skew at the clock end ponts would be zero.

## Including our inverter in picorv32a design and running synthesis ##

The config.tcl file had to be modified as shown: 

![modified config.tcl](https://github.com/SameerSDurgoji/VSD_Advanced_Physical_Design/blob/main/Day%204%20lab/modified%20config.tcl%20file.png)

Then, we had to invoke OpenLANE tool and prepare the design picorv32a. After this, we had to include the lef file of our custom made inverter sky130_vsdinv in the merged.lef file. So, the following comments had to be given:

set lefs [glob $::env(DESIGN_DIR)/src/*.lef]

add_lefs -src $lefs

We can see the lef file included in the merged.lef :

![lef file in merged.lef file](https://github.com/SameerSDurgoji/VSD_Advanced_Physical_Design/blob/main/Day%204%20lab/lef%20file%20of%20vsdinv%20created%20in%20the%20merged.lef%20file(1).png)

The following is the screenshot of the terminal after running synthesis:

![synthesis result](https://github.com/SameerSDurgoji/VSD_Advanced_Physical_Design/blob/main/Day%204%20lab/synthesis%20successful.png)


It was noticed that the slack was very high. So, we tried to vary the synthesis strategies in order to reduce it.

The following were the observations after changing the strategies:

![changing strategy](https://github.com/SameerSDurgoji/VSD_Advanced_Physical_Design/blob/main/Day%204%20lab/results%20of%20changing%20the%20synthesis%20strategy.png)

## Floorplan and Placement ##

Floorplan was done using the same command run_floorplan. Then placement was performed using the command run_placement.

After placement was completed, the layout of the placement result was opened in MAGIC and our custom cell was present in it as shown below:

![vsdinv in layout](https://github.com/SameerSDurgoji/VSD_Advanced_Physical_Design/blob/main/Day%204%20lab/vsdinv%20present%20in%20the%20placement.png)

![vsdinv in layout 1](https://github.com/SameerSDurgoji/VSD_Advanced_Physical_Design/blob/main/Day%204%20lab/vsdinv%20present%20in%20the%20placement%20after%20expand%20command.png)

We can see that the cells in the layout overlap each other. This overlap was observed because of the abutment of the cells due to the power lines.

## STA of the obtained design ##

The my_base.sdc file was added in the src folder of picorv32a. Its content is as shown below:

![my_base.sdc file](https://github.com/SameerSDurgoji/VSD_Advanced_Physical_Design/blob/main/Day%204%20lab/mybase.sdc%20file.png)

Then a .conf file was created in the openLANE_flow folder. Its contents are as shown below:

![sta.conf file](https://github.com/SameerSDurgoji/VSD_Advanced_Physical_Design/blob/main/Day%204%20lab/sta.conf%20file.png)

This file was used to run STA in OpenSTA tool. To run STA, the command: sta pre_sta.conf was to be given in openLANE_flow. Below are the results obtained after running STA of this pre_sta.conf file:

![sta of conf file](https://github.com/SameerSDurgoji/VSD_Advanced_Physical_Design/blob/main/Day%204%20lab/sta%20of%20pre_sta.conf%20file.png)

Here we again observe that the slack violation is too high. It can be seen that the delay of each cell depends on the input delay and the output load capacitance (which depends on its FANOUT). So, the maximum fanout was set to 4 using the command: set (SYNTH_MAX_FANOUT) 4. The results obtained is as shown below:

![changing max fanout](https://github.com/SameerSDurgoji/VSD_Advanced_Physical_Design/blob/main/Day%204%20lab/sta%20of%20pre_sta.conf%20file%20after%20changing%20max%20fanout.png)

Here, we observe that the slack though improved is still high. It was seen that the below shown buffer was causing a high delay. 

![the buff causing delay](https://github.com/SameerSDurgoji/VSD_Advanced_Physical_Design/blob/main/Day%204%20lab/this%20buff%20adding%200.72%20delay.png)

So, we upsized it to sky130_fd_sc_hd__buf_4.

![replacing the buff](https://github.com/SameerSDurgoji/VSD_Advanced_Physical_Design/blob/main/Day%204%20lab/replacing%20the%20buffer%20with%20a%20larger%20one.png)

The result obtained was as follows:

![improvement in slack](https://github.com/SameerSDurgoji/VSD_Advanced_Physical_Design/blob/main/Day%204%20lab/improvement%20in%20the%20slack.png)

![improvement in delay of buff](https://github.com/SameerSDurgoji/VSD_Advanced_Physical_Design/blob/main/Day%204%20lab/improvement%20in%20the%20delay%20of%20the%20replaced%20buf.png)

The slack again has improved but is still large. So, we further find the buffers which are causing this delay in the updated worst delay path. After a series of improvements, we arrived at a slack of -0.69 ns.


![final slack](https://github.com/SameerSDurgoji/VSD_Advanced_Physical_Design/blob/main/Day%204%20lab/final%20slack.png)

Thus, iteratively we had to improve the delay of the worst delay path.



## Including the results of STA in the OpenLANE flow ##

This STA was done outside the OpenLANE flow, hence in order to include its results in the flow, we had to replace the existing picorv32a.synthesis.v file in the results of synthesis with the one generated by this STA. Hence, to write the verilog file, the following command was given: write_verilog <address of the picorv32a.synthesis.v file>.

This replaced the existing verilog file under the results of synthesis. This would be used for the further steps in the flow after synthesis. The floorplan and the placement were done as usual. After the placement, the core area was observed as follows:

![core area after placement](https://github.com/SameerSDurgoji/VSD_Advanced_Physical_Design/blob/main/Day%204%20lab/core%20area%20after%20placement%20after%20sta.png)

## Running Clock Tree Synthesis ##

After this the CTS was performed using the command: run_cts. The tool TritonCTS was used. The following were the parameters of cts that could be varied:

![parameters of cts](https://github.com/SameerSDurgoji/VSD_Advanced_Physical_Design/blob/main/Day%204%20lab/parameters%20of%20cts.png)

The results of CTS were as follows:

![results of cts](https://github.com/SameerSDurgoji/VSD_Advanced_Physical_Design/blob/main/Day%204%20lab/end%20of%20cts.png)

At the end of CTS, the clock buffers would be added to the netlist and the updated verilog file named picorv32a.synthesis_cts.v was created in the synthesis results of the picorv32a design. 

## STA in OpenLANE ##

STA in OpenLANE is performed using the tool Openroad. Openroad is invoked in the OpenLANE flow by typing openroad. In this a db file had to be created, and the STA had to be performed. The screenshot after creating the db file is as shown below:

![db file created](https://github.com/SameerSDurgoji/VSD_Advanced_Physical_Design/blob/main/Day%204%20lab/db%20file%20created.png)

The result of STA are shown below:

![result of sta 1](https://github.com/SameerSDurgoji/VSD_Advanced_Physical_Design/blob/main/Day%204%20lab/final%20results%20of%20sta%20in%20openroad%20in%20openlane.png)


![result of sta 2](https://github.com/SameerSDurgoji/VSD_Advanced_Physical_Design/blob/main/Day%204%20lab/final%20results%20of%20sta%20in%20openroad%20in%20openlane(hold%20slack)%20the%20other%20is%20of%20typical%20column.png)


This was the end of Day 4.






