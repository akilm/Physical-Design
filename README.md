# Physical design using open-source EDA Tools
This Repository contains all the information needed for physical design flow for your IPs or SOCs using qflow and other opensource tools. The Repository was created during the Beginner Physical design using open-source EDA Tools conducted by Kunal Ghosh - [(VLSI System Design)](https://www.vlsisystemdesign.com/).

# Contents

# IC Design Components and Terminologies
**IC** or **Integrated circuit** is basically an electronic circuit consisting of large number of transistors, resistors and capacitors etc inside a single semiconductor chip. They come in a variety of packages and sizes. Some of the commonly used ICs include Timer 555 ic, 741 operational amplifiers, 78xx series of voltage regulators and 74xx series of logic gates.<br/>
**SOC - System on Chip** is also a type of IC which has the capabilities of a computer built inside the chip. It typically consists of a CPU, memory , input and output ports, analog IPs and other units integrated within itself.SoCs can be applied to any computing task. They are typically used in mobile computing such as tablets, smartphones, smartwatches and netbooks as well as embedded systems.<br/>
**Core** is the section of the chip where the fundamental logic of the design is placed. **Die**   contains the core, it is the small semiconductor material specimen on which the fundamental circuit is fabricated. IC's are fabricated on a single 9 inch or 12 inch diameter silicon wafer, which contains hundreds of mirror images of the fundamental logic. This wafer is then cut into small pieces, each piece has similar functionality of the fundamental logic called Die. **Pad cells** surround the rectangular metal patches where external bonds are made. input,output and power pad cells are commonly used pad cells.
## Package Types ##
Integrated circuits are put into protective packages to allow easy handling and assembly onto printed circuit boards and to protect the devices from damage. A very large number of different types of package exist. Through-hole packages, Surface mount, Chip carrier, Pin grid arrays, Flat packages and ball grid array are some of the most common packages. In this workshop we deal with a variant of Flat package called QFN-48 ( Quad Flat No leads with 48 pins)

## Foundry IPs and Macros
PLLs, DAC, ADC and SRAMs come under the category of foundry IP. They have to be manually designed or need some human interference (or intelligence) essentially to define and create them. IPs(Intellectual property) are targeted on a particular technology nodes. Macros are basically digital blocks which are made up of purely digital logic.

# RISC-V
The RISC V is an open source specification of an Instruction Set Architecture (ISA).x86 is commonly used ISA for personal computers and ARM architecture is commonly used for Mobile Phones .But these are proprietary standards Unlike most other ISA designs, the RISC-V ISA is provided under open source licenses that do not require fees to use, which provides it a huge edge over other commercially available ISAs. It is a simple, stable, small standard base ISA with extensible ISA support, that has been redefining the flexibility, scalability, extensibility, and modularity of chip designs. 

## Assembly language - Bridge between software and hardware
Applications and softwares running in our laptops and PCs are written in a variety of languages like C, C++, Python and Java etc. These different languages cannot be directly processed and implemented by the hardware (Microprocessor Core). They need to be converted into form that is understandable by the core. This is where the ISA or the instruction set architecture (assembly language) comes into the picture. Special programs called the compiler and assembler are used to convert the instructions in different languages to the target assembly language. The compiler converts the code into assembly language, which in our case is the RISC-V ISA and the assembler takes care of converting the different instructions in textual format into binary representations which are understood by the microprocessor and executed.

## PicoRV32 and PicoSOC (FPGA Flavour)
with the advent of an open standard ISA like RISC-V, there are many people who have created their own microprocessor cores which are documented in the RISC-V github handle found at [RISC-V Cores List](https://github.com/riscv/riscv-cores-list).PicoRV32 is one such implementation of the RISC-V ISA created by clifford wolf. [PicoRV32](https://github.com/cliffordwolf/picorv32) is a CPU core that implements the RISC-V RV32IMC Instruction Set. It can be configured as RV32E, RV32I, RV32IC, RV32IM, or RV32IMC core, and optionally contains a built-in interrupt controller. [PicoSOC](https://github.com/cliffordwolf/picorv32/blob/master/picosoc/picosoc.v) is an example SOC created using PicoRV32. PicoSOC is a type of SOC flavour which is more suited towards **FPGA** implementation and contains a less-detailed description of memory and other Components since these are resources already available inside the FPGA.

## Raven (ASIC Flavour)
It is an ASIC implementation of the PicoSoC-PicoRV32. The core was previously proven with an FPGA implementation and Raven is the first SoC built with it.The system integrator is Tim Edwards, another champion in the open source domain. Raven includes analog IPs like PLLs (Clock Multipliers), ADCs, DACs comparators and some other blocks as well.[Efabless](https://efabless.com/) has successfully bench-tested the Raven at 100MHz, and based on simulations the design should be able to operate at up to 150MHz.  

# Physical Design Flow
A typical back-end flow of chip design is explained in the below section. For each step, different open-source tools were used. Qflow is a complete tool chain for synthesizing digital circuits starting from verilog source and ending in physical layout for a specific target fabrication process.The tool chain is basically a collection of steps ordered in a particular sequence that makes it easier to analyse the design as it propagates through the steps.
A typical Qflow gui would look like the below picture. The steps are ordered in a sequential-order i.e, they can only be executed one after the other from top to bottom.

## Logic Synthesis
Logic synthesis is the process of translating and mapping RTL code written in HDL (such as Verilog or VHDL) into technology specific gate level representation. [Yosys](http://www.clifford.at/yosys/) is a framework for Verilog RTL synthesis. It currently has extensive Verilog-2005 support and provides a basic set of synthesis algorithms for various application domains.

## Floorplanning
The second step in the physical design flow is floorplanning. Floorplanning is the process of identifying structures that should be placed close together, and allocating space for them in such a manner as to meet the sometimes conflicting goals of available space (cost of the chip), required performance, and the desire to have everything close to everything else.Floor planning is done using an opensource tool called [graywolf](https://github.com/rubund/graywolf) as a part of Qflow.
## Placement
 Placement is the process of automatically assigning correct positions to predesigned cells on the chip with no overlapping such that some objective function is optimized. Placement is design state after Floorplanning and before routing. Placement also is done using [graywolf](https://github.com/rubund/graywolf) as a part of Qflow.
## Clock Tree Synthesis
Clock Tree Synthesis is a process which makes sure that the clock gets distributed evenly to all sequential elements in a design.
The goal of CTS is to minimize the skew and latency. Clock Tree Synthesis also is done using [graywolf](https://github.com/rubund/graywolf) as a part of Qflow.
## Routing
Routing is the next stage after clock tree synthesis which involves making connections as per the netlist generated earlier. This includes interconnections between standard cells, macros and the Pads present in the chip boundary. Routing is done with the help of an opensource tool called [Qrouter](http://opencircuitdesign.com/qrouter/)
## Static Timing Analysis
Static Timing Analysis (STA) is one of the techniques to verify design in terms of timing.The STA will validate whether the design could operate at the rated clock frequency, without any timing violations. Some of the basic timing violations are setup violation and hold violation. STA is done using the help of [Opentimer](https://github.com/OpenTimer/OpenTimer)  

## Layout Viewer
[Magic](http://opencircuitdesign.com/magic/) is the open source tool used for viewing and creating physical layouts for circuits after SPICE simulation. After the completion of the layout, The tool also has a facility of extracting parasitics and other delays in SPICE format which can further be simulated using spice tools
## Pre-Layout and Post-Layout SPICE simulation
[ngspice](http://ngspice.sourceforge.net/) the open source spice simulator for electric and electronic circuits. It is used to create a netlist, view waveforms and calculate a rough estimate of the timing parameters. It is used both in the pre-layout and the post-layout stages for running SPICE simulations.post-layout spice simulation is done after extracting the post layout spice file using magic layout viewer.

## Installation Guide
The details of installation are well documented in the README file of https://github.com/kunalg123/vsdflow. One can install all the tools easily by following all the steps given there instead of going through the hassles of installing each tool individually.</br>
List of tools installed using vsdflow
1. Yosys - RTL Synthesis
2. blifFanout - High fanout net (HFN) synthesis
3. graywolf - Placement
4. qrouter - Detailed routing
5. magic - VLSI Layout tool
6. netgen - LVS
7. OpenTimer and OpenSTA - Static timing analysis tool

 For installing **ngspice** for windows or Mac OS, it is already available as a precompiled version [here](http://ngspice.sourceforge.net/download.html). For installing ngspice in linux environments, [Synaptic package manager](https://geek-university.com/linux/synaptic/) needs to be installed first. ngspice can then be installed using the package manager.

