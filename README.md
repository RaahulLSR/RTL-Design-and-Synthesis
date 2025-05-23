# RTL Design and Synthesis
---
## Introduction
---
# Tools
#### need to write
## Iverilog
---
Icarus Verilog, often referred to as iverilog, is an open-source Verilog simulation and synthesis tool. It is used for RTL (Register Transfer Level) design and simulation in digital circuit design.
Test bench is the setup that is used to test the designed system by applying stimulus to the design and record its output and cross verify it with the golden reference file.
#### <image 1>
The above image describes how the overall testbench is setup. The stimulus generator generates the different combination of the input which are fed into the design and outputs are observed and listed down. Apart from this minor difference test bench is all nothing but one another Verilog file with an instantiation of the designed system. 
But remember Test-bench Verilog file will not have any primary inputs/outputs but just generates a good combination of inputs and monitors the output.

> NOTE: 
Simulator always looks for changes in the input. Upon changes in the input output is evaluated. If no change in input output is not evaluated.


The following image best describes the Iverilog based simulation flow.
 #### <image 2>
 
#### How to use:
```
Command : iverilog <design_verilog_file.v> <Testbench_verilog_file.v>
```
This command compiles the provided Verilog files using Icarus Verilog. It creates a default simulation output file named a.out

If we need the output file to be in a specific name then the command is,
```
Command : iverilog -o <simulation_result_file_name.vvp> <design_verilog_file.v> <Testbench_verilog_file.v>
```
Once the simulated output is generated the it must be executed to generate the VCD file.

## GTK wave
GTKWave is a waveform viewer used to visualize the output signals of your Verilog simulations. It reads .vcd files (Value Change Dump) and shows how our signals change over time — just like an oscilloscope but for digital logic inside the computer.

### How to use it:
```
Command: gtkwave <VCD_file_name.vcd>
```

It opens a new window with a list of the input, output and the internal signals. Among which we can select the required signals and visualize the signals in the form of waves/graphs. This make the output more understandable in GUI format.

## YOSYS
Yosys is a synthesizer that is used to convert the RTL model devolved and verified using simulator into the a netlist. The library file is the file that contains all the standard available cells in a particular flow. The synthesizer reads the design and library file and converts the RTL design into a netlist design. The following diagram illustrates the yosys setup.


#### <IMAGE3>
> NOTE: Since the RTL model has been simulated and verified for working it doesn’t guarantee that the newly synthesized Netlist also will work as intended. There is no guarantee that RTL model and Netlist model will perform in the same manner.

Hence to recheck our design the netlist based model is again put into the test bench for simulation and is verified again. But one good thing in this process is the testbench remains the same.
#### <IMAGE4>

### How to use:
Step 1: invoke yosys using the command.
```
Command : yosys
```

Step 2: read the library file.
```
Command : read_liberty -lib <path_to_library.lib>
```
Step 3: read Verilog file
```
Command : read_verilog <path_to_verilogfiles.v>
```
Step 4: module to be synthesized
```
Command : synth -top <module_name_to_be_synthesized>
```
Step 5:synthesize the netlist
```
Command : abc -liberty <path_to_the_library_file.lib>
```
Output: it gives all the signals that it has inferred from the Verilog design file.

Step 6: graphical version of realized logic.
```
Command : show
```

Step 7 : create the netlist
```
Command : write_verilog <name_of_the_netlist.v>
```
Output : it creates the netlist file
> Additional flag :  -noattr
Use case : removes all the unwanted additional information and gives the simplified netlist.

The yosys software intelligently maps the RTL model into available standard cells from the library and gives is the netlist which specifies what all standard cells must be used and how it should be connected to relaize that RTL logic.

# Key timing parameters:

## Propagation delay
o	It is the Maximum time taken by the combination circuit network to  settle to its new stable value upon a change in the input.
o	It occurs whenever there is a change in the input/output.
o	This delay is caused by the physical nature of the circuit the time it takes to charge or discharge the output capacitance.
o	This delay sets the clock speed of the entire digital system.
## Glitch 
o	It is the phenomena or a hazard that occurs because of unequal delay in the input lines.
o	This may trigger an momentarily incorrect value which is called a glitch or a race conditions.
o	Say for example assume a two input and gate,
o	if one input line has 1ns of delay and and other input line has 2ns of delay then the output of the system will momentarily [1ns] will/may have an incorrect value.
## Setup time
o	Setup time of the flip flop is the minimum interval of time before which the input to the flip flops must be stable right before the clock edge.
o	It also contributes to the maximum clock frequency.
## Hold time
o	The hold time is the minimum time after the active clock edge for which the inputs must remain stable so that it is captured properly.
## Clock to Q delay or flip flop propagation delay
o	It is the propagation delay from the active edge of the clock to the point when the output Q stabilizes in its value.

## Clock skew
o	It is the time difference between the clock signal reach two different flops.
o	It occurs due to imperfection in routing and clock tree.
## Slack
o	It is the difference in time between actual arrival time and the required time.
o	A positive slack mean the signal arrive at the flip flop well before the setup time and there is no timing violation.
o	While a negative slack means there is a timing violation that needs to be corrected. The signal arrives late.

