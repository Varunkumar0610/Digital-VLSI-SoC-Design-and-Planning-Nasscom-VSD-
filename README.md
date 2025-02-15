![image](https://github.com/user-attachments/assets/d6a9f776-218e-44f1-8144-c63680169cf2)


# Nasscom-VSD Digital VLSI SoC Design and Planning

![Static Badge](https://img.shields.io/badge/OS-linux-orange)
![Static Badge](https://img.shields.io/badge/EDA%20Tools-OpenLANE--Flow%2C_Yosys%2C_abc%2C_OpenROAD%2C_TritonRoute%2C_OpenSTA%2C_magic%2C_netgen%2C_GUNA-navy)
![Static Badge](https://img.shields.io/badge/languages-verilog%2C_bash%2C_TCL-crimson)
<!---
Comments
-->

> 2 Week digital VLSI SoC design and planning workshop with complete RTL2GDSII flow organised by VSD in collaboration with NASSCOM

## Table of Contents
- [Section - 1 Introduction of Open-Source EDA, OpenLane and Sky130 PDK](#Section---1-Introduction-of-Open-Source-EDA-OpenLane-and-Sky130-PDK)
- [Section - 2 Good Floorplan vs bad Floorplan and Introduction to library cells](#Section---2-Good-Floorplan-vs-bad-Floorplan-and-Introduction-to-library-cells)
- [Section - 3 Design Library Cell using magic layout and ngspice charcterization](#Section---3-Design-Library-Cell-using-magic-layout-and-ngspice-charcterization)
- [Section - 4 Pre Lay-out Timing Analysis and Importance of Good clock Tree](#Section---4-Pre-Lay-out-Timing-Analysis-and-Importance-of-Good-clock-Tree)
- [Section - 5 Final Steps for RTL2GDS Using TritonROUTE and openSTA](#Section---5-Final-Steps-for-RTL2GDS-Using-TritonROUTE-and-openSTA)
  


## Overview Of QFN-48 Chip (PicoRV32 - A Size-Optimized RISC-V CPU)
VSD Squadron Board: The VSD Board is shown below. Our focus is on the enclosed region containing the "Microprocessor (PicoRV32A-Cpu)," which will be designed using the RTL to GDS flow, progressing from the abstract design level to the fabrication stage.

![image](https://github.com/user-attachments/assets/844ea7fc-f4f4-4353-ae5d-d75a4507d075)

# Section - 1 Introduction of Open-Source EDA, OpenLane and Sky130 PDK

### Theory

<details>
  <summary>
Expand or Collapse
  </summary>

#### Introduction to IC Design components and terminologies
![image](https://github.com/user-attachments/assets/27c6db30-a2c8-4ec5-b9ff-82bdabe66d2f)
![image](https://github.com/user-attachments/assets/7fbe0214-8bf7-491a-b26d-3e698c1d2615)

#### Chip

* Now, taking a look inside the chip, all the signals from the external world to the chip and vice versa is passed through ***PADS***. The area bound by the pads is ***CORE*** where all the digital logic of the chip is placed. Both the core and pads make up the ***DIE*** which is the basic manufacturing unit in regards to semiconductor chips.

![image](https://github.com/user-attachments/assets/eb006c7f-93c8-4348-8cb8-8fa51160735c)

* ***FOUNDRY*** is the place where the semiconductor chips are manufactured and ***FOUNDRY IP's*** are Intellectual Properties based on a specific foundry and these IP's require a specific level of intelligence to be produced whereas, repeatable digital logic blocks are called ***MACROS***.

![image](https://github.com/user-attachments/assets/e6118285-f7f7-4d0d-8b2e-f2684f6a3eac)

#### ISA (Intruction Set Architecture)

* A C program which has to be run on a specific hardware layout which is the interior of a chip in your laptop, there is certain flow to be followed.
* Initially, this particular C program is compiled in it's assembly language program which is nothing but ***RISC-V ISA (Reduced Instruction Set Compting - V Intruction Set Architecture)***.
* Following this, the assembly language program is then converted to machine language program which is the binary language logic 0 and 1 which is understood by the hardware of the computer.
* Directly after this, we've to implement this RISC-V specification using some ***RTL (a Hardware Description Language)***. Finally, from the RTL to ***Layout*** it is a standard PnR or RTL to GDSII flow.

![image](https://github.com/user-attachments/assets/85b9292f-ec1c-427f-a7f2-3e17def8b1fd)

* For an application software to be run on a hardware there are several processes taking place. To begin with, the apps enters into a block called system software and it converts the application program to binary language. There are various layers in system software in which the major layers or components are OS (Operating System), Compiler and Assembler.
* At first the OS outputs are small function in C, C++, VB or Java language which are taken by the respective compiler and converted into instructions and the syntax of these instructions varies with the hardware architecture on which the system is implemented.
* Then, the job of the assembler is to take these instructions and convert it into it's binary format which is basically called as a machine language program. Finally, this binary language is fed to the hardware and it understands the specific functions it has to perform based on the binary code it receives.

![image](https://github.com/user-attachments/assets/e7af04bd-ba36-4f0a-bb04-f83067cd0b3a)

* For example, if we take a stopwatch app on RISC-V core, then the output of the OS could be a small C function which enters into the compiler and we get output RISC-V instructions following this, the output of the assembler will be the binary code which enters into your chip layout.

![image](https://github.com/user-attachments/assets/732a552f-c0a7-485f-ba44-f955aeec5c3f)

* For the above stopwatch the following are the input and output of the compiler and assembler.

![image](https://github.com/user-attachments/assets/9c47e8ed-ee6a-42d7-86d6-ecdd365d208b)

* The output of the compiler are instructions and the output of the assembler is the binary pattern. Now, we need some RTL (a Hardware Description Language) which understands and implements the particular instructions. Then, this RTL is synthesised into a netlist in form of gates which is fabricated into the chip through a physical design implementation.

![image](https://github.com/user-attachments/assets/ef7153c6-0319-4e5a-9a47-8411c22ad74a)

* There are mainly 3 different parts in this course. They are:
1. RISC-V ISA
2. RTL and synthesis of RISC-V based CPU core - picorv32
3. Physical design implementation of picorv32

![image](https://github.com/user-attachments/assets/0e283349-9aa3-460c-91f7-9f790f3e3c06)

#### Open-source Implementation

* For open-source ASIC design implemantation, we require the following enablers to be readily available as open-source versions. They are:-
1. RTL Designs
2. EDA Tools
3. PDK Data

* Initially in the early ages, the design and fabrication of IC's were tightly coupled and were only practiced by very few companies like TI, Intel, etc.
* In 1979, Lynn Conway and Carver Mead came up with an idea to saperate the design from the fabrication and to do this they inroduced structured design methodologies based on the λ-based design rules and published the first VLSI book "Introduction to VLSI System" which started the VLSI education.
* This methodology resulted in the emergence of the design only companies or ***"Fabless Companies"*** and fabrication only companies that we usually refer to as ***"Pure Play Fabs"***.
* The inteface between the designers and the fab by now became a set of data files and documents, that are reffered to as the ***"Process Design Kits (PDKs)"***.
* The PDK include but not limited to Device Models, Technology Information, Design Rules, Digital Standard Cell Libraries, I/O Libraries and many more.
* Since, the PDK contained variety of informations, and so they were distributed only under NDAs (Non-Disclosure Agreements) which made it in-accessible to the public.
* Recently, Google worked out an agreement with skywater to open-source the PDK for the 130nm process by skywater Technology, as a result on 30 June 2020 Google released the first ever open-source PDK.

![image](https://github.com/user-attachments/assets/75cc3dcd-4b5c-4c38-8042-887fe4d82130)

* ASIC design is a complex step that involves tons of steps, various methodologies and respective EDA tools which are all required for successful ASIC implementation which is achieved though an ASIC flow which is nothing but a piece of software that pulls different tools togather to carry out the design process.

![image](https://github.com/user-attachments/assets/e781ba43-c2b0-4684-9ab9-c6422cdb15c8)

#### OpenLANE Open-source ASIC Design Implementation Flow

#### Typical Flow in OpenLane:
1. **Synthesis:**- Converting RTL code to a gate-level netlist.

 ![image](https://github.com/user-attachments/assets/2c619ac7-5117-48aa-92f1-516a3f191dc1)

2. **Floorplanning:**- Defining the layout of the chip, including the placement of blocks and the arrangement of IOs.
 ![image](https://github.com/user-attachments/assets/3581a3b6-24aa-49e8-8632-5a3e8de32f74)

3. **Placement:**- Placing the synthesized gates on the chip floorplan.
 ![image](https://github.com/user-attachments/assets/4d947db7-68e4-40ae-ad60-fabf697abd43)

4. **Clock Tree Synthesis (CTS):**- Designing a clock distribution network to meet timing requirements.

   
 ![image](https://github.com/user-attachments/assets/64904d1c-1c2e-4820-ba7c-e2d86367aa7e)

5. **Routing:**- Creating the metal connections between gates and blocks.
 ![image](https://github.com/user-attachments/assets/2dd57219-bae8-4afd-b4f8-54d695ed71f0)

6. **Signoff:**- Performing final checks, including static timing analysis, design rule checks (DRC), and layout versus schematic (LVS) checks.

* The main objective of the ASIC Design Flow is to take the design from the RTL (Register Transfer Level) all the way to the GDSII, which is the format used for the final fabrication layout.

![image](https://github.com/user-attachments/assets/826e8a9b-2b08-40a7-bc1d-767b632147e5)

* Synthesis is the process of convertion or translation of design RTL into circuits made out of Standard Cell Libraries (SCL) the resultant circuit is described in HDL and is usually reffered to as the Gate-Level Netlist.
* Gate-Level Netlist is functionally equivalent to the RTL.

![image](https://github.com/user-attachments/assets/8771de52-e119-4a06-82be-6a53162c3040)

* The fundemental building blocks which are the standard cells have regular layouts.
* Each cell has different views/models which are utilised by different EDA tools like liberty view with electrical models of the cells, HDL behavioral models, SPICE or CDL views of the cells, Layout view which include GDSII view which is the detailed view and LEF view which is the abstract view.

![image](https://github.com/user-attachments/assets/fe7149d5-a27a-4dae-98f7-dd2ef681ad43)

* Chip Floor Planning

![image](https://github.com/user-attachments/assets/51cc2616-17f0-4e78-9770-ed16d619b2f5)

* Macro Floor Planning

![image](https://github.com/user-attachments/assets/70008d3b-d752-41e3-9634-ccc5b25ce889)

* Power Planning typically uses upper metal layers for power distribution since thay are thicker than lower metal layers and so have lower resistance and PP is done to avoid electron migration and IR drops.

![image](https://github.com/user-attachments/assets/2800bd74-a178-4119-b9f3-8da76ccca9bf)


* Placement

![image](https://github.com/user-attachments/assets/9a7e10cf-5814-49e7-8e0d-829625775e88)

* Global placement provide approximate locations for all cells based on connectivity but in this stage the cells may be overlapped on each other and in detailed placement the positions obtained from global placements are minimally altered to make it legal (non-overlapping and in site-rows)

![image](https://github.com/user-attachments/assets/ba914ca6-010c-4294-b070-efa7cf3a6ea0)

* Clock Tree Synthesis

![image](https://github.com/user-attachments/assets/4ce59c66-f38f-425c-bdb4-30efb8761b1b)

* Clock skew is the time difference in arrival of clock at different components.
* Routing

![image](https://github.com/user-attachments/assets/89f6f1fa-4cc5-4914-a425-6ce57219bb7d)

* skywater PDK has 6 routing layers in which the lowest layer is called the local interconnect layer which is a Titanium Nitride layer the following 5 layers are all Aluminium layers.

![image](https://github.com/user-attachments/assets/2b5f6dcc-054a-4974-a94a-3e326c9fa933)

* Global and Detailed Routing

![image](https://github.com/user-attachments/assets/9351ea3b-a8f8-4e87-afb8-702d1fb97251)

* Once done with the routing the final layout can be generated which undergoes various Sign-Off checks.
* Design Rules Checking (DRC) which verifies that the final layout honours all design fabrication rules.
* Layout Vs Schematic (LVS) which verifies that the final layout functionality matches the gate-level netlist that we started with.
* Static Timing Analysis (STA) to verify that the design runs at the designated clock frequency.

![image](https://github.com/user-attachments/assets/4fc50520-ba98-4bf6-80af-65367e7153af)

</details>

### Implementation

Section 1 tasks:- 
1. Run 'picorv32a' design synthesis using OpenLANE flow and generate necessary outputs.
2. Calculate the flop ratio.

```math
Flop\ Ratio = \frac{Number\ of\ D\ Flip\ Flops}{Total\ Number\ of\ Cells}
```
```math
Percentage\ of\ DFF's = Flop\ Ratio * 100
```

#### 1. Run 'picorv32a' design synthesis using OpenLANE flow and generate necessary outputs.

Commands to invoke the OpenLANE flow and perform synthesis

```bash
# Change directory to openlane flow directory
cd Desktop/work/tools/openlane_working_dir/openlane

# alias docker='docker run -it -v $(pwd):/openLANE_flow -v $PDK_ROOT:$PDK_ROOT -e PDK_ROOT=$PDK_ROOT -u $(id -u $USER):$(id -g $USER) efabless/openlane:v0.21'
# Since we have aliased the long command to 'docker' we can invoke the OpenLANE flow docker sub-system by just running this command
docker
```
```tcl
# Now that we have entered the OpenLANE flow contained docker sub-system we can invoke the OpenLANE flow in the Interactive mode using the following command
./flow.tcl -interactive

# Now that OpenLANE flow is open we have to input the required packages for proper functionality of the OpenLANE flow
package require openlane 0.9

# Now the OpenLANE flow is ready to run any design and initially we have to prep the design creating some necessary files and directories for running a specific design which in our case is 'picorv32a'
prep -design picorv32a

# Now that the design is prepped and ready, we can run synthesis using following command
run_synthesis

# Exit from OpenLANE flow
exit

# Exit from OpenLANE flow docker sub-system
exit
```

Screenshots of running each commands

![image](https://github.com/user-attachments/assets/ca0b78fd-819e-4be3-baa6-d43ba0f6d0d9)
![image](https://github.com/user-attachments/assets/aedb2668-7537-441c-8453-62ca13bd9281)
![image](https://github.com/user-attachments/assets/44cce33a-07ee-490e-9311-3186b978edeb)

#### 2. Calculate the flop ratio.

Screenshots of synthesis statistics report file with required values highlighted

![Screenshot from 2024-10-07 16-23-44](https://github.com/user-attachments/assets/325a932d-66bd-4de5-9614-88a47e794c19)
![Screenshot from 2024-10-07 16-24-05](https://github.com/user-attachments/assets/6ea028a4-b292-47cb-975e-8e741d54790e)

Calculation of Flop Ratio and DFF % from synthesis statistics report file

```math
Flop\ Ratio = \frac{1613}{14876} = 0.108429685
```
```math
Percentage\ of\ DFF's = 0.108429685 * 100 = 10.84296854\ \%
```

# Section - 2 Good Floorplan vs bad Floorplan and Introduction to library cells

## Utilization factor and Aspect ratio of a chip or die:

The utilization factor and aspect ratio are critical metrics in chip or die design and fabrication:

**Utilization Factor:**
- **Definition:**  The utilization factor, often referred to as area utilization, is a measure of how efficiently the available area on a chip or die is used by the actual logic, memory, and other functional components. It is calculated as the ratio of the area occupied by the functional elements to the total available area of the chip.
Formula:
Utilization Factor = (Area Occupied by Functional Components/Total Chip Area)×100 %

- Importance: A higher utilization factor indicates that more of the chip's area is used for functional purposes, which is generally desirable for cost efficiency. However, it needs to be balanced with considerations like routing space, power distribution, and thermal management.

**Aspect Ratio:**
- **Definition:** The aspect ratio of a chip or die refers to the ratio of its width to its height. It is an important parameter in the physical design and packaging of semiconductor devices.
Formula:
Aspect Ratio = Height of the Chip/Width of the Chip

 
- Importance: The aspect ratio affects the chip's mechanical stability, ease of packaging, and manufacturability. Ideally, the aspect ratio should be close to 1 (i.e., the chip is nearly square), as this minimizes stress and simplifies the packaging process. However, depending on the application and design constraints, the aspect ratio may vary.


![image](https://github.com/user-attachments/assets/1dd42934-d636-4ace-99e5-ca0fbe7536b2)

**Concept of pre-placed cells and de-coupling capacitors:**
- **Pre-placed Cells:**
Pre-placed cells are components or functional units within an IC or on a PCB that are placed at specific locations before the main placement and routing process begins. These cells are typically used for power management, signal integrity, or other critical functions that require precise positioning to meet design constraints. Pre-placed cells can include decoupling capacitors, power pads, voltage regulators, or other components that are essential for the proper functioning of the circuit.

- **Decoupling Capacitors:**
Decoupling capacitors are small-value capacitors placed between the power supply rails (Vcc and Gnd) of an integrated circuit or on a PCB to provide a local source of energy that can supply transient current demands of the active devices. They help to "decouple" the local power supply from the rest of the circuit, ensuring that rapid changes in current demanded by one component do not affect the supply voltage of other components.

![image](https://github.com/user-attachments/assets/e150ba66-28c5-4678-abab-5ddfd327764a)

**Run Floorplan**
```bash
run_floorplan
```
![image](https://github.com/user-attachments/assets/042a8615-1520-44f7-aa4a-85c6e5f5beb1)

![image](https://github.com/user-attachments/assets/7b72fd66-3ecb-47dd-918d-e5d7e01b96e8)

**Picorv32a floorplan def file:**


![Screenshot from 2024-10-25 11-30-35](https://github.com/user-attachments/assets/b1197928-80ae-4aa8-adb8-843938943560)

![image](https://github.com/user-attachments/assets/8830c6b8-f95f-4662-81de-d73e5f4a277c)

According to floorplan def
```math
1000\ Unit\ Distance = 1\ Micron
```
```math
Die\ width\ in\ unit\ distance = 660685 - 0 = 660685
```
```math
Die\ height\ in\ unit\ distance = 671405 - 0 = 671405
```
```math
Distance\ in\ microns = \frac{Value\ in\ Unit\ Distance}{1000}
```
```math
Die\ width\ in\ microns = \frac{660685}{1000} = 660.685\ Microns
```
```math
Die\ height\ in\ microns = \frac{671405}{1000} = 671.405\ Microns
```
```math
Area\ of\ die\ in\ microns = 660.685 * 671.405 = 443587.212425\ Square\ Microns
```

#### Load generated floorplan def in magic tool and explore the floorplan.

Commands to load floorplan def in magic in another terminal

```bash
# Change directory to path containing generated floorplan def
cd Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/25-10_04-48/results/floorplan/

# Command to load the floorplan def in magic tool
magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.floorplan.def &
```

Screenshots of floorplan def in magic

![image](https://github.com/user-attachments/assets/eda02393-4a7b-4d77-96f1-f50bda007f74)

Equidistant placement of ports

![image](https://github.com/user-attachments/assets/c5ad6d2d-1d1d-4637-a279-b66c4788823c)

Port layer as set through config.tcl

![image](https://github.com/user-attachments/assets/82c6539a-e8df-4cc9-bfa1-cacb187e037b)
![image](https://github.com/user-attachments/assets/cc2fec69-cfed-42dd-b604-80aa311e460d)

Decap Cells and Tap Cells

![image](https://github.com/user-attachments/assets/8e557f9f-4fe6-4b90-bade-f976d22f6f9e)

Diogonally equidistant Tap cells

![image](https://github.com/user-attachments/assets/baee36ed-c151-45e6-a19e-fe9abe128b52)

Unplaced standard cells at the origin

![image](https://github.com/user-attachments/assets/ccca9bbf-4d49-4ef6-ab27-a49f3b64ccbb)

#### Run 'picorv32a' design congestion aware placement using OpenLANE flow and generate necessary outputs.

Command to run placement

```tcl
# Congestion aware placement by default
run_placement
```

Screenshots of placement run

![image](https://github.com/user-attachments/assets/d6371139-e405-4f6b-bf50-8a4b290d1e1d)
![image](https://github.com/user-attachments/assets/2587dd6d-f728-481a-b028-39711526e8fb)

#### Load generated placement def in magic tool and explore the placement.

Commands to load placement def in magic in another terminal

```bash
# Change directory to path containing generated placement def
cd Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/25-10_04-48/results/placement/

# Command to load the placement def in magic tool
magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.placement.def &
```

Screenshots of floorplan def in magic

![image](https://github.com/user-attachments/assets/d3d93873-21de-4067-8c18-b63098296f61)

Standard cells legally placed 

![image](https://github.com/user-attachments/assets/41aa67c0-7571-4dcf-86a5-5e68ed096464)

Commands to exit from current run

```tcl
# Exit from OpenLANE flow
exit

# Exit from OpenLANE flow docker sub-system
exit
```
## Placement in VLSI Design
Placement plays a crucial role in VLSI (Very Large Scale Integration) design. It involves determining the physical locations of standard cells or logic elements within a chip or block. Let's break it down:

- **Global Placement:**

  - Global placement assigns general locations to movable objects (cells).
  - Some overlaps between placed objects are allowed during this stage. 
  - The goal is to achieve a rough layout that satisfies area constraints.

- **Detailed Placement:**

  - Detailed placement refines the object locations obtained from global placement.
  - It enforces non-overlapping constraints and ensures that cells are placed on legal cell sites.
  - The quality of detailed placement significantly impacts subsequent routing stages.

``` magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.placement.def & ```

![image](https://github.com/user-attachments/assets/6cac8896-8b3d-400a-b7ed-b185e15080ad)
![image](https://github.com/user-attachments/assets/d9d9c1fe-0e6e-4fc6-8d40-31f600e8804f)
![image](https://github.com/user-attachments/assets/5e293784-1d45-4416-963f-c9248a45630e)
![image](https://github.com/user-attachments/assets/f06324d8-e432-40a1-86c0-275d8e205932)

## CELL DESIGN AND CHARACETRIZATION FLOWS

Library is a place where we get information about every cell. It has differents cells with different size, functionality,threshold voltages. There is a typical cell design flow steps.

    Inputs : PDKS(process design kit) : DRC & LVS, SPICE Models, library & user-defined specs.
    Design Steps :Circuit design, Layout design (Art of layout Euler's path and stick diagram), Extraction of parasitics, Characterization (timing, noise, power).
    Outputs: CDL (circuit description language), LEF, GDSII, extracted SPICE netlist (.cir), timing, noise and power .lib files
## Standard Cell Characterization Flow
A typical standard cell characterization flow that is followed in the industry includes the following steps:

- Read in the models and tech files
- Read extracted spice Netlist
- Recognise behavior of the cells
- Read the subcircuits
- Attach power sources
- Apply stimulus to characterization setup
- Provide neccesary output capacitance loads
- Provide neccesary simulation commands
Now all these 8 steps are fed in together as a configuration file to a characterization software called GUNA. This software generates timing, noise, power models. These .libs are classified as Timing 
 characterization, power characterization and noise characterization.





| Timing defintion   | value| 
|----------|----------|
| `slew_low_rise_thr`|`20% value`|
|`slew_high_rise_thr`| `80% value`   |
| `slew_low_fall_thr`| `20% value`   |
|`slew_high_fall_thr`| `80% value`   |
| `in_rise_thr`      | `50% value`   |
| `in_fall_thr`      | `50% value`   |
| `out_rise_thr`     | `50% value`   |
| `out_fall_thr`     | `50% value`   |



## Propagation Delay and Transition Time
**Propagation delay** is the time it takes for a signal to travel from the input to the output of a circuit. It's typically measured as the time difference between when the input signal reaches 50% of its final value and when the output signal reaches 50% of its final value.

If the threshold values used to measure this delay are not chosen carefully, it can result in negative delay values, which are not physically meaningful. However, even with well-chosen thresholds, the delay might still appear positive or negative due to variations in the slew rate, which is how quickly the signal transitions from one value to another.

![image](https://github.com/user-attachments/assets/f1c39625-2bf0-4afd-b492-5dd4608aa694)


``` bash
Propagation delay = time(out_thr) - time(in_thr)
```

**Transition Time**
Transition time is the time it takes for a signal to move between its low and high states (or vice versa). It’s typically measured between the points where the signal reaches 10% and 90% of its final value, or sometimes between 20% and 80%. This metric is crucial for understanding the speed of signal changes in a circuit.


``` bash
Rise transition time = time(slew_high_rise_thr) - time (slew_low_rise_thr)

Low transition time = time(slew_high_fall_thr) - time (slew_low_fall_thr)
```

# Section - 3 Design Library Cell using magic layout and ngspice charcterization

## VTC Spice Simulation

Voltage Transfer Characteristic (VTC) is a key concept in electronic circuit analysis and design, especially in analog and mixed-signal integrated circuits. A VTC SPICE simulation refers to the process of using a SPICE (Simulation Program with Integrated Circuit Emphasis) simulator to analyze and plot the VTC curve of a circuit.

In a VTC simulation, the input voltage of the circuit is gradually swept over a specified range, while the corresponding output voltage is measured. The plot of output voltage versus input voltage generated from this data provides valuable insights into the circuit’s linearity, gain, and various operating regions. This is especially useful for evaluating the performance of amplifiers, logic gates, and signal processing circuits.

Here’s a step-by-step guide for performing a VTC SPICE simulation:

- Define the circuit in SPICE using its netlist.
- Set up a DC sweep simulation for the input voltage.
- Specify the range over which the input voltage will vary.
- Run the simulation to record the output voltage for each input value.
- Plot the VTC curve (output voltage vs. input voltage).
- Analyze the plot to evaluate the circuit’s behavior, including linearity, gain, and transition regions.

**Circuit Design:** The circuit to be analyzed is designed in a schematic capture tool that is compatible with the SPICE simulator being used.

**Simulation Setup:** The SPICE simulation is set up with the appropriate analysis type, which could be a DC sweep analysis if the goal is to plot the VTC over a range of DC input voltages.

**nput Signal Sweep:** The input voltage source is configured to sweep across the desired range of voltages. This could be from the negative supply rail to the positive supply rail, or any other relevant range.

**Output Measurement:**  The output node of the circuit is specified, and the simulator is instructed to record the voltage at this node for each input voltage step.

**Simulation Run:** The simulation is run, and the SPICE engine calculates the circuit's response to each input voltage.

**Data Analysis:**  The resulting data is plotted as a graph with input voltage on the x-axis and output voltage on the y-axis. This plot is the VTC of the circuit.

**Interpretation:**  The VTC is analyzed to determine the circuit's performance characteristics, such as gain, input offset voltage, output swing, and the presence of any non-linearities or distortion.

![image](https://github.com/user-attachments/assets/07b5ec33-5ff2-4917-af51-da83625ac6ef)



### Concept on Switching Threshold

The concept of the switching threshold is crucial in the context of digital circuits, particularly in logic gates and transistors. The switching threshold refers to the input voltage level at which the circuit transitions from one state to another, typically from a low (logic 0) to a high (logic 1) state or vice versa.

In a digital circuit, the switching threshold is designed to be at a specific voltage level to ensure reliable operation and to minimize the effects of noise and signal degradation. The exact value of the switching threshold can vary depending on the technology and design of the circuit, but it is typically set near the midpoint of the supply voltage range.

For example, in a CMOS (Complementary Metal-Oxide-Semiconductor) inverter, which is a fundamental building block of digital logic, the switching threshold is ideally at the midpoint of the supply voltage (Vdd/2). This ensures that the inverter has equal noise margins for both high and low input levels.

The switching threshold is influenced by several factors, including:

   **1. Device Characteristics:** The physical properties of the transistors used in the circuit, such as their threshold voltage (Vth), affect the overall switching threshold of the circuit.

  **2. Supply Voltage:**  The operating voltage of the circuit can impact the switching threshold. As the supply voltage changes, the switching threshold may also shift.

   **3. Temperature:** Temperature variations can cause changes in the electrical characteristics of the transistors, which can in turn affect the switching threshold.

   **4. Process Variations:** Manufacturing variations can lead to differences in the electrical properties of transistors, which can influence the switching threshold of the circuit.

   **5. Load Capacitance:** The capacitive load connected to the output of the circuit can affect the switching threshold due to the dynamic behavior of the circuit during switching.

![image](https://github.com/user-attachments/assets/31e3b8d8-189c-40b3-bfdb-f3ec66e79c79)





## Static And Dynamic Simulation of CMOS Inverter

### Static Simulation
Static simulation, also known as DC analysis, involves analyzing the circuit's behavior at DC (Direct Current) steady-state conditions. This type of simulation is used to determine the following characteristics:

   Voltage Transfer Characteristic (VTC): The VTC plot shows the relationship between the input voltage (Vin) and the output voltage (Vout) of the inverter. It helps in understanding the transition between the logic levels (0 and 1) and the noise margins.

**Switching Threshold (V_th):** The input voltage at which the inverter switches from one logic state to another. Ideally, for a symmetrical VTC, the switching threshold is at Vdd/2, where Vdd is the supply voltage.

**Noise Margins:** The maximum noise voltage that can be tolerated at the input while the output remains in the correct logic state.

**Static Power Dissipation:** The power consumed by the inverter when it is in a stable state (not switching). This is typically very low in CMOS circuits due to the low leakage currents.

### Dynamic Simulation
Dynamic simulation, also known as transient analysis, involves analyzing the circuit's behavior over time as it responds to time-varying input signals. This type of simulation is used to determine the following characteristics:

**Propagation Delay (t_p):** The time it takes for the output to change in response to a change in the input. It is typically measured as the time from the 50% point of the input transition to the 50% point of the output transition.

**Rise Time (t_r) and Fall Time (t_f):** The times it takes for the output to transition from 10% to 90% (rise time) and from 90% to 10% (fall time) of the output voltage swing.

**Power Consumption:** The dynamic power consumed by the inverter during switching, which is a function of the switching frequency, load capacitance, and supply voltage.

**Transient Response:**  The overall response of the inverter to input signals, including overshoot, undershoot, and ringing.
    
![image](https://github.com/user-attachments/assets/5e041e54-2c0f-4896-b943-a777bf059e09)



## Layout and CMOS Fabrication Process

The CMOS (Complementary Metal-Oxide-Semiconductor) fabrication process is a complex series of steps used to manufacture integrated circuits. CMOS technology is widely used in modern electronics due to its low power consumption and high-density integration capabilities. The fabrication process involves several key steps, including substrate preparation, doping, oxidation, photolithography, etching, and metallization. Here is an overview of the CMOS fabrication process:
**1. Substrate Preparation**

Starting Material: The process begins with a pure silicon wafer, which serves as the substrate.
Cleaning: The wafer is thoroughly cleaned to remove any impurities.

**2. Oxidation**

Thermal Oxidation: A layer of silicon dioxide (SiO2) is grown on the surface of the silicon wafer. This oxide layer acts as an insulator and also serves as a mask for subsequent doping processes.

**3. Photolithography**

Photoresist Application: A light-sensitive polymer called photoresist is applied to the wafer surface.
Exposure and Development: The wafer is exposed to ultraviolet (UV) light through a mask that contains the desired pattern. The exposed photoresist is then developed, leaving a patterned layer on the wafer.

**4. Etching**

Wet or Dry Etching: The areas of the wafer not protected by the photoresist are etched away using either wet chemicals or dry plasma etching.

**5. Doping**

Ion Implantation: Selected areas of the wafer are doped with impurities (dopants) such as boron or phosphorus to create n-type or p-type regions. This process alters the electrical properties of the silicon, creating the necessary semiconductor characteristics.

**6. Isolation**

Local Oxidation of Silicon (LOCOS): An oxide layer is grown in selected areas to isolate different components of the circuit.
Trench Isolation: Alternatively, trenches can be etched and filled with oxide to achieve isolation.

**7. Transistor Formation**

**- Gate Oxide Growth:**  A thin layer of oxide is grown on the surface where the transistors will be formed.

**- Polysilicon Deposition:** A layer of polysilicon is deposited and patterned to form the gates of the transistors.

**- Spacer Formation:** Spacers are created on the sides of the gate to control the subsequent doping process.

**- Source/Drain Implantation:** Dopants are implanted to form the source and drain regions of the transistors.

**8. Silicidation**

Silicide Formation: A silicide layer (e.g., tungsten silicide) is formed on top of the polysilicon gate and the source/drain regions to reduce resistance.

**9. Interlayer Dielectric and Planarization**

Dielectric Deposition: A layer of insulating material (e.g., silicon dioxide) is deposited over the entire wafer.
Chemical Mechanical Polishing (CMP): The wafer surface is planarized using CMP to ensure a smooth surface for subsequent metallization.

**10. Metallization**

Metal Layers: Multiple layers of metal (e.g., aluminum or copper) are deposited and patterned to form the interconnections between transistors and other components.
Contact and Via Formation: Contacts and vias are etched and filled with metal to connect the transistors to the metal layers.

**11. Passivation**

Passivation Layer: A final layer of insulating material is deposited to protect the circuit from external contaminants.

**12. Testing and Packaging**

Wafer Probing: The wafer is tested to identify any defective chips.

Dicing: The wafer is cut into individual chips (dice).

Packaging: The chips are mounted in packages, which are then sealed to protect the delicate circuitry.

Final Testing: The packaged chips are tested again to ensure they meet the required specifications.

![Assigning-the-names-of-NMOS-and-PMOS](https://github.com/user-attachments/assets/936498d3-3e2e-41d5-8ee4-da89fd7c5e7c)

![image](https://github.com/user-attachments/assets/cc4cf6af-32ed-4764-ac14-336ce3fc92c8)

### LABs Exercise

#### 1. IoPlacer revision & adding the "set::env" command

![Screenshot from 2024-10-16 11-59-40](https://github.com/user-attachments/assets/ba67673b-4e31-4fff-8638-7626cb58e62a)


here the the i/o pins are congested and overlapped:
![Screenshot from 2024-10-16 12-01-52](https://github.com/user-attachments/assets/8eec0525-0623-4416-99c8-7dcd5c6e135a)

#### 1. Clone custom inverter standard cell design from github repository

```bash
# Change directory to openlane
cd Desktop/work/tools/openlane_working_dir/openlane

# Clone the repository with custom inverter design
git clone https://github.com/nickson-jose/vsdstdcelldesign

# Change into repository directory
cd vsdstdcelldesign

# Copy magic tech file to the repo directory for easy access
cp /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech .

# Check contents whether everything is present
ls

# Command to open custom inverter layout in magic
magic -T sky130A.tech sky130_inv.mag &
```

Screenshot of commands run

![image](https://github.com/user-attachments/assets/ea9177d6-88a4-448e-9f96-d85f5ab68509)

#### 2. Load the custom inverter layout in magic and explore.

Screenshot of custom inverter layout in magic

![image](https://github.com/user-attachments/assets/14dcf0cf-ee07-4427-bd26-68a82ceabe35)

NMOS and PMOS identified

![image](https://github.com/user-attachments/assets/7f704bce-91b4-4778-a3ba-5420012cdb2c)
![image](https://github.com/user-attachments/assets/b4060577-28f0-474e-9c7d-1d45ee08310d)

Output Y connectivity to PMOS and NMOS drain verified

![image](https://github.com/user-attachments/assets/3c3f9045-7df6-433f-830f-17bf5f281c29)

#### 3. Spice extraction of inverter in magic.

Commands for spice extraction of the custom inverter layout to be used in tkcon window of magic

```tcl
# Check current directory
pwd

# Extraction command to extract to .ext format
extract all

# Before converting ext to spice this command enable the parasitic extraction also
ext2spice cthresh 0 rthresh 0

# Converting to ext to spice
ext2spice
```

#### 4. spice tecgnology file, simulation and output graph


![image](https://github.com/user-attachments/assets/8cde7b4e-a65d-469b-a0ca-32aa56679a7c)


![image](https://github.com/user-attachments/assets/e83aca2a-edd4-4611-a076-da3400d4a5dd)


updated tech file:

![image](https://github.com/user-attachments/assets/88803377-45ca-47af-a96b-dd7d91d106c0)

#### 5. Parameters Calculation
  ##### 5.a) rise transition of of output: 20% of VDD to 80% of VDD
```math
rise\_transition = (80 % of 3.3v) - (20 % of 3.3v)
                = (x-coordinate\ pos. of\ 2.64v - x-coordinate\ pos. of\ 0.66v)
                = (6.24247*e-19) - (6.61804*e-19)
                = 0.06163\ ns
```

  ##### 5.b) fall transition of of output: 80% of VDD to 20% of VDD
```math
fall\_transition = (20 % of 3.3v) - (80% of 3.3v)
                = (x-coordinate\ pos. of\ 0.66v - x-coordinate\ pos. of\ 2.64v)
                = (8.09409*e-19) - (8.05156*e-19)
                = 0.04253\ ns
```
                
 ![image](https://github.com/user-attachments/assets/2d02dfa8-f8e6-4098-995b-71cf2decd805)

                
  ##### 5.c) cell rise delay: (50% of o/p rise) - (50% of i/p fall)
```math
 cell\_rise\_delay= (x-coordinate\ pos. of\ 1.65v - x-coordinate\ pos. of\ 1.65v)
                = (6.2089*e-19) - (6.15*e-19)
                = 0.0589\ ns
```
                
 ![image](https://github.com/user-attachments/assets/85d0076f-04ce-46a8-ad8e-1d8b4c4ba54b)

  ##### 5.d) cell fall delay: (50% of o/p fall) - (50% of i/p rise)
```math
 cell\_fall\_delay= (x-coordinate\ pos. of\ 1.65v - x-coordinate\ pos. of\ 1.65v)
                = (8.07657*e-19) - (8.05*e-19)
                = 0.02657\ ns
```
                
 ![image](https://github.com/user-attachments/assets/4e986827-07df-4ec4-8c1b-2dfdc67ac4cb)

#### 6. Introduction to Magic tool and DRC

![image](https://github.com/user-attachments/assets/13b20e46-d226-479b-abc2-b7fe6069cc75)

![image](https://github.com/user-attachments/assets/e06146c8-ef52-463e-a1b1-0b3b5e3fa549)

![image](https://github.com/user-attachments/assets/04462b54-555f-49df-a06a-8af57fb3e3eb)

![image](https://github.com/user-attachments/assets/682edf39-66b4-4b88-979c-c6773de13974)

![image](https://github.com/user-attachments/assets/fd4101a9-ae66-40a4-8bd7-fd9652321248)

![image](https://github.com/user-attachments/assets/db8b8714-ea87-4595-9a06-d01ca4ae3e0e)

![image](https://github.com/user-attachments/assets/5b07a766-cc21-4e37-9aac-cf8e018fffb3)

# Section - 4 Pre Lay-out Timing Analysis and Importance of Good clock Tree

### 1-Delay Table

In physical design, a delay table is a data structure used to model the delay characteristics of standard cells or interconnects in a digital circuit.

#### Purpose
**Estimate Delays:**  Delay tables help in estimating the propagation delay through gates or along wires, which is crucial for timing analysis.

#### Delay Values  
The actual propagation delays, typically provided for different combinations of input slew and output load.

#### Usage
**Static Timing Analysis (STA):** Delay tables feed information into STA tools to evaluate the timing performance of a design.

**Cell Libraries:** Standard cell libraries include delay tables for each cell type, allowing EDA tools to accurately model and simulate circuit behavior.
#### Types
**Linear Delay Models:** Simplified models that use linear equations.

**Non-linear Delay Models:** More complex and accurate, capturing variations due to non-linear effects.
#### Importance
Accuracy: Provides accurate timing information, crucial for ensuring that the design meets its timing constraints.

Optimization: Helps in identifying critical paths and optimizing them to improve performance.

![image](https://github.com/user-attachments/assets/7cf04801-00cb-46b9-8548-fcdeb56edfb0)



### 2-Setup time and Hold time of Flop

#### Set Up Time Analysis
Setup time is the minimum time period before the clock edge during which the data input must be stable.

#### Purpose:
Ensures that the data is correctly sampled by the flip-flop on the active clock edge.

#### Analysis Steps:
Identify Critical Paths: Trace the longest path from one flip-flop to the next, including combinational logic.

**Calculate Data Path Delay**: Sum up the delays of all elements (gates, interconnects) in the data path.

**Compare with Setup Time:** Ensure that the data path delay plus setup time is less than the clock period.

Data Path Delay + Setup Time < Clock Period

Adjust if Necessary: If the condition isn’t met, optimize the design by reducing delays, increasing clock period, or modifying the path.

#### Hold Time Analysis
Hold time is the minimum time period after the clock edge during which the data input must remain stable.

#### Purpose:
Prevents the new data from being captured too early, ensuring the current data is held long enough.

#### Analysis Steps:
**Identify Critical Paths:** Examine paths where new data might overwrite current data too soon.

**Calculate Data Path Delay:** Consider the minimum delay from the clock edge to the data input.

**Compare with Hold Time:** Ensure that the data path delay is greater than the hold time.

Data Path Delay>Hold Time

**Adjust if Necessary:** If the condition isn’t met, add buffers or delay elements to increase path delay.

#### Importance
**Setup Time Violations:** Can lead to incorrect data being captured, causing functional errors.

**Hold Time Violations:** Can result in data corruption as new data overwrites old data prematurely.

![image](https://github.com/user-attachments/assets/60865da8-5bff-418e-a727-82c8c7ed644e)




### 3-Clock tree routing and buffering

Clock tree routing and buffering are crucial steps in the physical design phase of integrated circuit (IC) design. These steps ensure that the clock signal is distributed efficiently and uniformly across the entire chip to all sequential elements (like flip-flops) with minimal skew and latency.

#### a. Clock Tree Synthesis (CTS):
**Purpose:** The goal of clock tree synthesis is to distribute the clock signal from a single clock source (usually a Phase-Locked Loop (PLL) or a clock generator) to all the sequential elements in the design (like flip-flops) with minimal skew and balanced delays.

#### Challenges:
**Clock Skew:** The difference in arrival times of the clock signal at different sequential elements. Skew can lead to timing violations, so minimizing it is a primary goal.

**Clock Latency:** The delay from the clock source to a flip-flop or other sequential element. Latency needs to be controlled to meet timing requirements.

#### Power Consumption: 

Clock networks are often the most power-consuming part of the chip. Optimizing for lower power while maintaining performance is crucial.

#### b. Clock Tree Routing:

**Tree Structure:** The clock distribution network is usually constructed as a tree (hence the name "clock tree"). This tree structure helps in balancing the delays and skew.

#### Clock Tree Topologies:
- H-Tree: A hierarchical tree structure that is symmetric and balanced, often used in regular grid layouts.

- X-Tree, Y-Tree: Variations of the H-tree, used based on specific design needs.

- Spine: A clock distribution method where the clock is routed along a central "spine" and branches out to different regions. This is common in large, hierarchical designs.

**Routing Techniques:**
- Minimal Skew Routing: Ensuring that all paths from the clock source to the clock sinks (sequential elements) are balanced to minimize skew.
- Buffered Clock Tree: Inserting buffers along the clock tree to manage delay and drive strength, ensuring that the clock signal reaches all parts of the circuit with sufficient strength and minimal degradation.

#### c. Clock Tree Buffering:
**Why Buffers are Needed:**
- Load Management: The clock signal needs to drive a large number of flip-flops and other clocked elements. Buffers are used to amplify the clock signal and drive these loads effectively.
- Delay Control: Buffers help in balancing the delay in different paths of the clock tree, which is crucial for minimizing skew.
- Noise Reduction: By buffering the clock signal, noise introduced by long interconnects can be reduced.

**Types of Buffers:**
- Inverters: Simple buffers that invert the signal. Sometimes used in pairs to maintain the same logic level while buffering.
- Dedicated Clock Buffers: Specially designed buffers that are optimized for driving the clock signal with high fan-out and minimal jitter.
- Buffer Insertion: Buffers are strategically inserted at points in the clock tree where the clock signal needs to be amplified or where delay needs to be controlled.
The placement of these buffers is determined during the Clock Tree Synthesis process using EDA tools, which optimize for delay, skew, and power consumption.

#### d. Skew and Latency Management:
- Zero-Skew Clock Tree: An ideal clock tree would have zero skew, meaning all flip-flops receive the clock signal at the exact same time. While practically impossible, the goal is to minimize skew as much as possible.
- Useful Skew: Sometimes, intentional skew is introduced to improve timing in certain paths. This is known as useful skew and can help in meeting setup and hold time constraints.
Clock Latency: Clock tree buffering and routing also ensure that the clock signal arrives at the flip-flops with the desired latency, which is crucial for timing closure.

#### e. Post-CTS Optimization:
After the clock tree is synthesized, further optimization steps like Clock Tree Optimization (CTO) and Post-CTS Optimization might be performed to fine-tune the clock network, ensuring that all timing requirements are met.
- Timing Analysis: Tools perform static timing analysis (STA) to verify that the clock tree meets the required timing constraints, and any violations are corrected by adjusting the clock tree design.

![image](https://github.com/user-attachments/assets/8cc70556-8af7-462b-a56e-94e240269f08)

![image](https://github.com/user-attachments/assets/740cba8a-ebfa-4082-9804-f8612c1ef19f)

#### 1. Fix up small DRC errors and verify the design is ready to be inserted into our flow.

Conditions to be verified before moving forward with custom designed cell layout:
* Condition 1: The input and output ports of the standard cell should lie on the intersection of the vertical and horizontal tracks.
* Condition 2: Width of the standard cell should be odd multiples of the horizontal track pitch.
* Condition 3: Height of the standard cell should be even multiples of the vertical track pitch.

Commands to open the custom inverter layout

```bash
# Change directory to vsdstdcelldesign
cd Desktop/work/tools/openlane_working_dir/openlane/vsdstdcelldesign

# Command to open custom inverter layout in magic
magic -T sky130A.tech sky130_inv.mag &
```

Screenshot of tracks.info of sky130_fd_sc_hd

![image](https://github.com/user-attachments/assets/cca3530c-ab93-4437-a1d7-71ec9ceadba2)

Commands for tkcon window to set grid as tracks of locali layer

```tcl
# Get syntax for grid command
help grid

# Set grid values accordingly
grid 0.46um 0.34um 0.23um 0.17um
```

Screenshot of commands run

![image](https://github.com/user-attachments/assets/c679f56d-dfde-49ec-bd00-d0708a09fb60)

Condition 1 verified

![image](https://github.com/user-attachments/assets/d0a3fe81-fd27-4cb1-8e27-e974ee75f4d5)

Condition 2 verified

```math
Horizontal\ track\ pitch = 0.46\ um
```

![image](https://github.com/user-attachments/assets/efd11f21-642e-4dcb-ac32-d4527b5c06ab)

```math
Width\ of\ standard\ cell = 1.38\ um = 0.46 * 3
```

Condition 3 verified

```math
Vertical\ track\ pitch = 0.34\ um
```

![image](https://github.com/user-attachments/assets/bd3b34c9-803a-4045-b65c-4e22e2b7dc53)

```math
Height\ of\ standard\ cell = 2.72\ um = 0.34 * 8
```
#### 2. Save and open the finalized layout.

Command for tkcon window to save the layout with custom name

```tcl
# Command to save as
save sky130_vsdinv.mag
```

Command to open the newly saved layout

```bash
# Command to open custom inverter layout in magic
magic -T sky130A.tech sky130_vsdinv.mag &
```

Screenshot of newly saved layout

![image](https://github.com/user-attachments/assets/83bc01ce-6669-49ef-ad0c-fd8945edfc8c)

#### 3. layout lef is generated.

Command for tkcon window to write lef

```tcl
# lef command
lef write
```

Screenshot of command run

![image](https://github.com/user-attachments/assets/c814de66-a82c-4688-aa65-ee003dee0b47)

Screenshot of newly created lef file

![image](https://github.com/user-attachments/assets/65ab898e-f232-4c72-b258-f11f4b040d3a)

#### 4. Move the new lef inside picorv32a, src design directory.

Commands to copy necessary files to 'picorv32a' design 'src' directory

```bash
# Copy lef file
cp sky130_vsdinv.lef ~/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/src/

# List and check whether it's copied
ls ~/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/src/

# Copy lib files
cp libs/sky130_fd_sc_hd__* ~/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/src/

# List and check whether it's copied
ls ~/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/src/
```

Screenshot of commands run

![image](https://github.com/user-attachments/assets/6f09c960-0ae9-41f8-bc7d-51c64b588ade)

#### 5. Edit 'config.tcl' to change lib file & add new lef.

Commands to be added to config.tcl to include our custom cell in the openlane flow

```tcl
set ::env(LIB_SYNTH) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__typical.lib"
set ::env(LIB_FASTEST) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__fast.lib"
set ::env(LIB_SLOWEST) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__slow.lib"
set ::env(LIB_TYPICAL) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__typical.lib"

set ::env(EXTRA_LEFS) [glob $::env(OPENLANE_ROOT)/designs/$::env(DESIGN_NAME)/src/*.lef]
```

Edited config.tcl to include the added lef and change library to ones we added in src directory

![image](https://github.com/user-attachments/assets/b165f6e8-ae5f-4126-8a8a-bacf976dca14)

#### 6. Run synthesis with newly inserted custom inverter cell.
Commands to view and change parameters to improve timing and run synthesis

```tcl
# Now once again we have to prep design so as to update variables
prep -design picorv32a -tag 25-10_04-48 -overwrite

# Addiitional commands to include newly added lef to openlane flow merged.lef
set lefs [glob $::env(DESIGN_DIR)/src/*.lef]
add_lefs -src $lefs

# Command to display current value of variable SYNTH_STRATEGY
echo $::env(SYNTH_STRATEGY)

# Command to set new value for SYNTH_STRATEGY
set ::env(SYNTH_STRATEGY) "DELAY 3"

# Command to display current value of variable SYNTH_BUFFERING to check whether it's enabled
echo $::env(SYNTH_BUFFERING)

# Command to display current value of variable SYNTH_SIZING
echo $::env(SYNTH_SIZING)

# Command to set new value for SYNTH_SIZING
set ::env(SYNTH_SIZING) 1

# Command to display current value of variable SYNTH_DRIVING_CELL to check whether it's the proper cell or not
echo $::env(SYNTH_DRIVING_CELL)

# Now that the design is prepped and ready, we can run synthesis using following command
run_synthesis
```
![image](https://github.com/user-attachments/assets/32486db5-26dd-408c-89bd-c6fa3d93abab)

![image](https://github.com/user-attachments/assets/5fdf8128-d494-43b7-b746-cc75c1436eaf)
![image](https://github.com/user-attachments/assets/25add0b7-436f-4f64-874e-126a949351bd)

#### 7. Run floorplan and placement and verify the cell is accepted in PnR flow.

Now that our custom inverter is properly accepted in synthesis we can now run floorplan using following command

```tcl
# Now we can run floorplan
run_floorplan
```
Since we are facing unexpected un-explainable error while using `run_floorplan` command, we can instead use the following set of commands available based on information from `Desktop/work/tools/openlane_working_dir/openlane/scripts/tcl_commands/floorplan.tcl` and also based on `Floorplan Commands` section in `Desktop/work/tools/openlane_working_dir/openlane/docs/source/OpenLANE_commands.md`

```tcl
# Follwing commands are alltogather sourced in "run_floorplan" command
init_floorplan
place_io
tap_decap_or
```
![image](https://github.com/user-attachments/assets/7a0ce107-ad01-47b4-b0ec-57d3ebc0361d)

![image](https://github.com/user-attachments/assets/528d7c38-30ab-4345-9ebd-982182b316f0)

Now that floorplan is done we can do placement using following command

```tcl
# Now we are ready to run placement
run_placement
```
![image](https://github.com/user-attachments/assets/bea91c36-88cd-467e-a999-a37aaa6f0dc2)

Commands to load placement def in magic in another terminal

```bash
# Change directory to path containing generated placement def
cd Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/25-10_04-48/results/placement/

# Command to load the placement def in magic tool
magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.placement.def &
```

Screenshot of placement def in magic

![image](https://github.com/user-attachments/assets/be15452c-953f-41a6-af15-8bdff7d80322)

Screenshot of custom inverter inserted in placement def with proper abutment

![image](https://github.com/user-attachments/assets/a3f852b9-9e2b-49a4-b8df-e8b61a6b365c)

Command for tkcon window to view internal layers of cells

```tcl
# Command to view internal connectivity layers
expand
```

Abutment of power pins with other cell from library clearly visible

![image](https://github.com/user-attachments/assets/251a1fb5-b49b-4de7-ba43-906c90d0bd88)
![image](https://github.com/user-attachments/assets/58839704-747c-4e86-bc29-9278811ecdbb)

#### 8. Screenshot of my_base.sdc
![image](https://github.com/user-attachments/assets/a45b5bff-3676-478c-aa83-65248ccb6da2)

#### 9. Screenshot of sta.conf
![image](https://github.com/user-attachments/assets/3e58d1db-8039-4091-9a59-3f60b96ae125)
![image](https://github.com/user-attachments/assets/4af17f55-3da1-4c91-bc82-073f8033b806)

again run_synthesis, then floorplan, placement
![image](https://github.com/user-attachments/assets/8b056129-156f-4197-b634-3343c0eba28a)

![image](https://github.com/user-attachments/assets/b1516239-1970-4bb3-9a59-a7507e872403)

#### 10. Screenshot of openroad
![image](https://github.com/user-attachments/assets/50d3d244-c1d0-4854-9123-27d29d2d535c)

#### 11. Screenshot of CTS run

Since we confirmed that netlist is replaced and will be loaded in PnR but since we want to follow up on the earlier 0 violation design we are continuing with the clean design to further stages

Commands load the design and run necessary stages

```tcl
# Now once again we have to prep design so as to update variables
prep -design picorv32a -tag 25-10_04-48 -overwrite

# Addiitional commands to include newly added lef to openlane flow merged.lef
set lefs [glob $::env(DESIGN_DIR)/src/*.lef]
add_lefs -src $lefs

# Command to set new value for SYNTH_STRATEGY
set ::env(SYNTH_STRATEGY) "DELAY 3"

# Command to set new value for SYNTH_SIZING
set ::env(SYNTH_SIZING) 1

# Now that the design is prepped and ready, we can run synthesis using following command
run_synthesis

# Follwing commands are alltogather sourced in "run_floorplan" command
init_floorplan
place_io
tap_decap_or

# Now we are ready to run placement
run_placement

# Incase getting error
unset ::env(LIB_CTS)

# With placement done we are now ready to run CTS
run_cts
```
![image](https://github.com/user-attachments/assets/357f5032-3ff1-4a70-b706-9dfa645b22a6)

![image](https://github.com/user-attachments/assets/25841ac2-fccd-4cb2-a0ec-f2d526785eda)

![image](https://github.com/user-attachments/assets/19786d1f-3191-4691-a06f-d72d314bc3ca)

# Section - 5 Final Steps for RTL2GDS Using TritonROUTE and openSTA

### 1-Routing and DRC
Routing and Design Rule Check (DRC) are key steps in the physical design process of integrated circuits (ICs). They play crucial roles in ensuring that the design is manufacturable and meets all the required electrical and physical constraints.

#### a. Routing:
Routing is the process of connecting the various components (standard cells, macros, IOs, etc.) in an IC design with metal interconnects according to the netlist generated during the synthesis stage.

#### Key Aspects of Routing:
**Global Routing:**

- Purpose: Provides an abstract, coarse-grained plan of how the connections will be made across different regions of the chip. It divides the chip area into a grid and assigns paths to nets without detailed geometry.
- Outcome: Guides the detailed routing stage by giving an overall connectivity layout.

  
**Detailed Routing:**

- Purpose: Converts the global routing plan into actual geometrical paths on the metal layers of the chip.
- Metal Layers: The routing is done using different metal layers, with lower layers typically used for local connections and higher layers for long-distance or global connections.

#### Routing Algorithms:

Maze Routing: Finds the shortest path between two points, avoiding obstacles.
Channel Routing: Manages routing in channels between rows of cells.
Grid-Based Routing: Uses a grid to guide the routing paths and ensure that they follow design rules.

**Routing Challenges:**

Congestion: Too many wires trying to pass through the same area can lead to congestion, which can cause timing issues or even make routing impossible.
Crosstalk: Signals on adjacent wires can interfere with each other, leading to noise and potential errors.
Delay: Longer or more resistive paths can introduce delays that affect the timing of the circuit.

**Routing Constraints:**

Timing Constraints: Ensuring that the routed paths meet the required timing (setup and hold times).

**Power Constraints:** Minimizing power consumption and ensuring that power distribution is balanced.

**Signal Integrity:** Maintaining signal quality by minimizing noise, crosstalk, and other electrical issues.

#### b. Design Rule Check (DRC):
Design Rule Check (DRC) is a verification step in the physical design process that ensures the layout of the chip adheres to a set of predefined rules provided by the semiconductor foundry. These rules are necessary to guarantee that the design can be manufactured reliably.

#### Key Aspects of DRC:

 **Design Rules:**

 - Spacing Rules: Minimum distances between different metal layers, vias, or other features to avoid shorts and ensure manufacturability.
 - Width Rules: Minimum and maximum width of wires to ensure that they can be manufactured correctly and carry the required current without issues.
 - Enclosure Rules: Requirements for how different layers must overlap or enclose each other (e.g., metal layers and vias).
 - Alignment Rules: Ensure that different layers align correctly to avoid misalignment during manufacturing.

**DRC Tools:**

Tools like Calibre, Mentor Graphics, or Cadence's Assura are commonly used to run DRC checks. These tools take the physical layout as input and verify it against the foundry's design rules.

**Common DRC Violations:**

- Shorts: When two wires or components that should not be connected are too close or overlap.
  
- Opens: Missing connections due to routing errors or insufficient overlap of layers.
  
- Minimum Width Violations: Wires or features that are too narrow and might not be reliably manufactured.
  
- Spacing Violations: Features that are too close together, which can cause shorts or manufacturing issues.
 

**DRC Correction:**

After running a DRC, any violations are flagged, and the design must be corrected. This may involve adjusting the layout, rerouting wires, or modifying cell placement.

![image](https://github.com/user-attachments/assets/94885594-3726-4a89-b7e7-b27f4a263800)
![image](https://github.com/user-attachments/assets/f09e856d-7d60-456e-a62c-ffac6bcccaab)



### 2-Global routing and Detailed routing

#### a. Global Routing:

**Purpose:**  Provides a high-level, coarse plan for connecting different regions of the chip. It divides the chip into grids and assigns general paths for connections without specifying exact wire routes.

**Outcome:** Guides the detailed routing stage by outlining broad paths for signals to follow, helping to manage congestion and ensure that connections can be made.

#### b. Detailed Routing:

**Purpose:** Converts the global routing plan into precise, geometric wire routes on specific metal layers, connecting all components according to the design's netlist.

**Outcome:**  Finalizes the exact paths for each wire, ensuring they meet design rules (like spacing and width), and resolves any conflicts or congestion identified during global routing.

![image](https://github.com/user-attachments/assets/f3802b22-cd0c-425a-9825-c9c77ce44e3a)


### 3-Maze routing and Steiner tree algorithm

#### a. Maze Routing:

- Purpose: Maze routing is an algorithm used to find a path between two points (e.g., from a source to a destination pin) in a grid while avoiding obstacles.

**How It Works:**

The grid is treated as a matrix, where each cell represents a possible location for the wire. Obstacles like other wires, cells, or blocked areas are marked as unavailable.
The algorithm explores all possible paths from the source to the destination, usually using a breadth-first search (BFS) approach.
It expands from the source node by checking neighboring cells until it reaches the destination.
The shortest path found through this exploration is chosen as the route.
- Pros:

Optimal Path: Guarantees finding the shortest path if one exists.
Flexibility: Can handle complex obstacles and multiple routing layers.
- Cons:

Computationally Expensive: Can be slow and resource-intensive, especially for large designs.
Not Always Practical for Large Grids: The algorithm can become infeasible for very large or densely populated grids due to its exhaustive nature.

#### b. Steiner Tree Algorithm:

**Purpose:** The Steiner tree algorithm is used to connect multiple points (e.g., multiple pins that need to be connected by a single net) with the shortest possible interconnect length, minimizing the total wire length.

**How It Works:**

The algorithm starts by creating a minimal spanning tree (MST) that connects all the target points (e.g., pins).
Steiner points, which are additional points not originally in the set of target points, are introduced to reduce the overall wire length. These points act as intermediate nodes that help in minimizing the total connection length.
The resulting structure is a Steiner tree, which is a tree that connects all target points (and possibly additional Steiner points) with the minimal total interconnect length.

- Pros:

Minimized Wire Length: Produces a routing solution with minimal total wire length, which is beneficial for reducing delay and power consumption.
Efficient for Multi-Terminal Nets: Particularly useful for nets that need to connect more than two pins.

- Cons:
  
Complexity: Finding the exact Steiner tree is an NP-hard problem, meaning it can be computationally expensive for large nets.
Approximation: Often, heuristic or approximate methods are used to find a near-optimal Steiner tree, which might not be the absolute minimum.

![image](https://github.com/user-attachments/assets/9cfc90dd-0b71-41d4-96c9-b984e2ecbf65)
![image](https://github.com/user-attachments/assets/e284dded-e949-428b-9b2b-869dfb9edca2)
![image](https://github.com/user-attachments/assets/9fb66838-70f4-4564-8e6c-75f2b4f49143)



### 4-Power distribution and network routing

**Power Distribution:**
Power distribution in IC design refers to the process of delivering power from the external power sources (like power pads or pins) to every component within the chip, including logic gates, flip-flops, memory cells, and other circuits.

##### Key Concepts:

**Power and Ground Rails:**
These are the main conductors that distribute power (VDD) and ground (VSS) across the chip. They are typically implemented as wide metal lines running across the chip to minimize resistance and voltage drop.

##### Power Grids:
A power grid is a network of horizontal and vertical metal lines that form a mesh-like structure across the chip. This grid ensures that power is distributed uniformly to all areas of the chip.
- VDD Grid: Carries the supply voltage.
  
- VSS Grid: Carries the ground potential.

##### Power Rings:
Rings of metal lines surrounding certain regions or blocks on the chip, providing a stable supply of power and grounding. They help in minimizing noise and ensuring a reliable power supply.

##### Power Straps:
Wider metal lines or groups of parallel lines that provide robust connections between the power grid and the power sources. These straps help reduce resistance and voltage drop.

##### Decoupling Capacitors:
Capacitors placed close to the power pins of circuits to stabilize the power supply by filtering out noise and compensating for sudden changes in current demand.

#### b. Power Network Routing:
Power network routing involves the detailed placement and routing of the power distribution network (PDN) on the chip, ensuring that all blocks and standard cells receive adequate power.

##### Steps in Power Network Routing:

**Placement of Power Pads:**
Power pads (VDD, VSS) are placed around the periphery of the chip or in specific locations within the chip. These pads connect the internal power network to the external power supply.

**Creation of Power Grid:**
A grid of metal layers is laid out across the chip to distribute the power. The grid usually spans multiple metal layers, with the lower layers used for local distribution and the upper layers for global distribution.

**Power Rings and Straps:**

Power rings are placed around the periphery of major blocks (e.g., memory blocks, large logic blocks) to ensure a stable power supply.
Power straps are used to connect the power rings and the power grid, ensuring that current can flow efficiently from the power pads to all parts of the chip.

**Via Placement:**

Vias are used to connect different metal layers within the power grid. Multiple vias are often placed in parallel to reduce resistance and ensure reliable current flow.

**IR Drop Analysis:**

After routing the power network, an IR drop analysis is performed to ensure that the voltage drop across the power grid is within acceptable limits. Excessive IR drop can cause circuits to malfunction due to insufficient voltage levels.

**Electromigration Check:**

Electromigration refers to the gradual movement of metal atoms in the power grid due to high current densities, which can lead to failures. The power network is checked to ensure that the current density in all metal lines is below the safe limits to avoid electromigration.

**Noise and Decoupling:**

The power network is designed to minimize noise (e.g., ground bounce) by carefully placing decoupling capacitors and ensuring that the grid structure is robust.

![image](https://github.com/user-attachments/assets/556b6b77-e24b-447b-a94c-d089ce2301d5)




### 5-TritonRoute features

TritonRoute is an open-source detailed routing tool used in the physical design of integrated circuits (ICs). It is a critical component of the Triton suite of EDA (Electronic Design Automation) tools. TritonRoute is specifically designed for the detailed routing stage of VLSI (Very Large Scale Integration) design.

#### Key Features of TritonRoute:

**Detailed Routing:**

TritonRoute performs the detailed routing step in the IC design flow, where it converts global routing paths into exact geometrical wire routes on the metal layers of the chip.
It handles the connection of signal nets, power nets, and clock nets, ensuring that all routing adheres to design rules.

**Design Rule Checking (DRC):**

TritonRoute incorporates design rule checking within its routing algorithms to ensure that all routed wires comply with the foundry's design rules, such as spacing, width, and via requirements.
The tool is capable of resolving DRC violations that may arise during routing.

**Open-Source and Integration:**

TritonRoute is open-source, which means it can be freely used, modified, and integrated into custom EDA flows.
It is part of the broader OpenROAD project, which aims to provide a fully autonomous, open-source RTL-to-GDSII flow.

**High-Quality Routing:**

The tool focuses on generating high-quality routing solutions that minimize wire length, reduce congestion, and respect timing and power constraints.
It can handle complex designs with a large number of nets and multiple metal layers.
Scalability:

TritonRoute is designed to be scalable, handling both small and large designs efficiently. It leverages modern algorithms to manage the complexity of routing in advanced technology nodes.

**Customization and Flexibility:**

Being open-source, TritonRoute allows for extensive customization. Users can modify the tool to suit specific design requirements or integrate it with other tools in their design flow.
The tool can be adapted for different technology nodes and design styles.

**Part of the Triton Suite:**

TritonRoute works in conjunction with other tools in the Triton suite, such as TritonCTS (Clock Tree Synthesis) and TritonPlace (Placement), providing a comprehensive solution for physical design.

**Community and Support:**

As an open-source project, TritonRoute benefits from community contributions and ongoing development. It is actively maintained by researchers and contributors in the EDA community.

![image](https://github.com/user-attachments/assets/61bd696f-d250-483f-b8de-995fcb3455ed)
![image](https://github.com/user-attachments/assets/a549f2f0-0a34-4c02-aa35-947e558a2221)
![image](https://github.com/user-attachments/assets/e6966a25-abb9-4ffd-87b9-9030cd240e9f)

#### 1. Perform generation of Power Distribution Network (PDN) and explore the PDN layout.

Commands to perform all necessary stages up until now

```bash
# Change directory to openlane flow directory
cd Desktop/work/tools/openlane_working_dir/openlane

# alias docker='docker run -it -v $(pwd):/openLANE_flow -v $PDK_ROOT:$PDK_ROOT -e PDK_ROOT=$PDK_ROOT -u $(id -u $USER):$(id -g $USER) efabless/openlane:v0.21'
# Since we have aliased the long command to 'docker' we can invoke the OpenLANE flow docker sub-system by just running this command
docker
```
```tcl
# Now that we have entered the OpenLANE flow contained docker sub-system we can invoke the OpenLANE flow in the Interactive mode using the following command
./flow.tcl -interactive

# Now that OpenLANE flow is open we have to input the required packages for proper functionality of the OpenLANE flow
package require openlane 0.9

# Now the OpenLANE flow is ready to run any design and initially we have to prep the design creating some necessary files and directories for running a specific design which in our case is 'picorv32a'
prep -design picorv32a

# Addiitional commands to include newly added lef to openlane flow merged.lef
set lefs [glob $::env(DESIGN_DIR)/src/*.lef]
add_lefs -src $lefs

# Command to set new value for SYNTH_STRATEGY
set ::env(SYNTH_STRATEGY) "DELAY 3"

# Command to set new value for SYNTH_SIZING
set ::env(SYNTH_SIZING) 1

# Now that the design is prepped and ready, we can run synthesis using following command
run_synthesis

# Following commands are alltogather sourced in "run_floorplan" command
init_floorplan
place_io
tap_decap_or

# Now we are ready to run placement
run_placement

# Incase getting error
unset ::env(LIB_CTS)

# With placement done we are now ready to run CTS
run_cts

# Now that CTS is done we can do power distribution network
gen_pdn 
```

Screenshots of power distribution network run

![image](https://github.com/user-attachments/assets/06de6ac4-26fe-4f94-841e-d4f09f98c67f)
![image](https://github.com/user-attachments/assets/17f1da4a-4b23-4b48-bd22-4a6ed04f5d35)

Commands to load PDN def in magic in another terminal

```bash
# Change directory to path containing generated PDN def
cd Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/25-10_04-48/tmp/floorplan/

# Command to load the PDN def in magic tool
magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read 14-pdn.def &
```

Screenshots of PDN def

![image](https://github.com/user-attachments/assets/218f0671-17f0-40f3-b7c6-503932e53279)
![image](https://github.com/user-attachments/assets/eaa88af4-31ff-436a-92b3-55a6827620b8)

#### 2. Perfrom detailed routing using TritonRoute and explore the routed layout.

Command to perform routing

```tcl
# Check value of 'CURRENT_DEF'
echo $::env(CURRENT_DEF)

# Check value of 'ROUTING_STRATEGY'
echo $::env(ROUTING_STRATEGY)

# Command for detailed route using TritonRoute
run_routing
```

Screenshots of routing run

![image](https://github.com/user-attachments/assets/07c4404f-50da-4476-a556-bd0e78c8369d)
![image](https://github.com/user-attachments/assets/76acdcb7-e86e-4438-908d-12852f1c97f3)
![image](https://github.com/user-attachments/assets/34911b6b-2ab1-4227-b88a-5397490048fe)

Commands to load routed def in magic in another terminal

```bash
# Change directory to path containing routed def
cd Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/25-10_04-48/results/routing/

# Command to load the routed def in magic tool
magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.def &
```

Screenshots of routed def

![image](https://github.com/user-attachments/assets/21925838-cfdd-4579-a5b3-aa75a9332929)
![image](https://github.com/user-attachments/assets/0a3f5882-b4e7-4a00-9c16-b53408f2ebab)
![image](https://github.com/user-attachments/assets/f3eb197c-1b39-4884-a617-dfc018a9e8dc)
![image](https://github.com/user-attachments/assets/6150cbbd-fd55-4e7e-b74e-f7492f4b6aac)

Screenshot of fast route guide present in `openlane/designs/picorv32a/runs/15-10_07-29/tmp/routing` directory

![image](https://github.com/user-attachments/assets/07dfd38d-ba11-46ed-80d4-d81d83517cdb)

#### 3. Post-Route parasitic extraction using SPEF extractor.

Commands for SPEF extraction using external tool

```bash
# Change directory
cd Desktop/work/tools/SPEF_EXTRACTOR

# Command extract spef
python3 main.py /home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/26-03_08-45/tmp/merged.lef /home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/15-10_07-29/results/routing/picorv32a.def
```
![image](https://github.com/user-attachments/assets/eb20601f-471f-43f5-97a6-1ef7309dafed)

# Acknowledgements

* [Kunal Ghosh](https://github.com/kunalg123), Co-founder, VSD Corp. Pvt. Ltd.
* [Nickson P Jose](https://github.com/nickson-jose), Physical Design Engineer, Intel Corporation.
* [R. Timothy Edwards](https://github.com/RTimothyEdwards), Senior Vice President of Analog and Design, efabless Corporation.



