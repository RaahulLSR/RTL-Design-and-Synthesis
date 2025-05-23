# RTL Design and Synthesis
# Introduction
This repository documents my learnings and practical implementations from the RTL Design and Synthesis course conducted by VSD (VLSI System Design). The course introduces a hands-on approach to digital design using open-source EDA tools such as:

- Icarus Verilog for writing and simulating Verilog RTL code

- GTKWave for visualizing signal waveforms

- Yosys for logic synthesis and gate-level netlist generation

- Sky130 PDK for synthesizing real-world designs using open-source standard cells

# Tools
In this section, we explore the key open-source tools used in the RTL design and synthesis flow. Each tool plays a specific role in the hardware design lifecycle, from writing and simulating Verilog code to waveform analysis and logic synthesis:

Icarus Verilog (iverilog): A simulator for compiling and running Verilog designs, enabling functional verification through testbenches.

GTKWave: A waveform viewer that helps visualize signal transitions and verify logical behavior after simulation.

Yosys: A synthesis framework used to convert RTL designs into gate-level netlists. It supports various optimization passes and backend flows.

Sky130 PDK: An open-source Process Design Kit provided by Google and SkyWater, used to map synthesized designs to real-world standard cells.

## Iverilog
Icarus Verilog, often referred to as iverilog, is an open-source Verilog simulation and synthesis tool. It is used for RTL (Register Transfer Level) design and simulation in digital circuit design.
Test bench is the setup that is used to test the designed system by applying stimulus to the design and record its output and cross verify it with the golden reference file.
![image](https://github.com/user-attachments/assets/7ad01840-c79e-49a1-920e-992cbeca20cf)
The above image describes how the overall testbench is setup. The stimulus generator generates the different combination of the input which are fed into the design and outputs are observed and listed down. Apart from this minor difference test bench is all nothing but one another Verilog file with an instantiation of the designed system. 
But remember Test-bench Verilog file will not have any primary inputs/outputs but just generates a good combination of inputs and monitors the output.

> NOTE: 
Simulator always looks for changes in the input. Upon changes in the input output is evaluated. If no change in input output is not evaluated.


The following image best describes the Iverilog based simulation flow.
![image](https://github.com/user-attachments/assets/bda58486-c5b8-41a2-b1a7-182bf9149810)

 
#### How to use:
```
Command : iverilog <design_verilog_file.v> <Testbench_verilog_file.v>
```
![image](https://github.com/user-attachments/assets/d3ee032e-8525-448d-97f2-3a3440a1f3a0)

This command compiles the provided Verilog files using Icarus Verilog. It creates a default simulation output file named a.out

If we need the output file to be in a specific name then the command is,
```
Command : iverilog -o <simulation_result_file_name.vvp> <design_verilog_file.v> <Testbench_verilog_file.v>
```
Once the simulated output is generated the it must be executed to generate the VCD file.
![image](https://github.com/user-attachments/assets/4804da44-19f1-47cb-a111-2e9c237daa2d)

## GTK wave
GTKWave is a waveform viewer used to visualize the output signals of your Verilog simulations. It reads .vcd files (Value Change Dump) and shows how our signals change over time — just like an oscilloscope but for digital logic inside the computer.

### How to use it:
```
Command: gtkwave <VCD_file_name.vcd>
```

It opens a new window with a list of the input, output and the internal signals. Among which we can select the required signals and visualize the signals in the form of waves/graphs. This make the output more understandable in GUI format.
![image](https://github.com/user-attachments/assets/e548ab52-7deb-474c-8084-3e53196747c9)

## YOSYS
Yosys is a synthesizer that is used to convert the RTL model devolved and verified using simulator into the a netlist. The library file is the file that contains all the standard available cells in a particular flow. The synthesizer reads the design and library file and converts the RTL design into a netlist design. The following diagram illustrates the yosys setup.


![image](https://github.com/user-attachments/assets/0d2fd5b5-6323-4882-a8fc-cdb741d68a81)

> NOTE: Since the RTL model has been simulated and verified for working it doesn’t guarantee that the newly synthesized Netlist also will work as intended. There is no guarantee that RTL model and Netlist model will perform in the same manner.

Hence to recheck our design the netlist based model is again put into the test bench for simulation and is verified again. But one good thing in this process is the testbench remains the same.
![image](https://github.com/user-attachments/assets/dd0c505a-04fa-4377-ba1d-8cf758204d9b)


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
The below image shows the output of the above codes
![image](https://github.com/user-attachments/assets/023aed57-2c2f-44d0-8c3c-2081acf76f01)
Step 4: module to be synthesized
```
Command : synth -top <module_name_to_be_synthesized>
```
On synthesizing the module a inference table is created as below,
![image](https://github.com/user-attachments/assets/4d52c306-485b-4427-a318-249000c8ff70)
Step 5:synthesize the netlist
```
Command : abc -liberty <path_to_the_library_file.lib>
```
Output: it gives all the signals that it has inferred from the Verilog design file.
![image](https://github.com/user-attachments/assets/14df84ef-2ea2-4871-a350-d31a210d3393)
Step 6: graphical version of realized logic.
```
Command : show
```
Ath the end of this step we could see the below graphical representation of the circuit,
![image](https://github.com/user-attachments/assets/e945fe44-b8b8-4be0-936a-fd96ee6118aa)

Step 7 : create the netlist
```
Command : write_verilog <name_of_the_netlist.v>
```
Output : it creates the netlist file
> Additional flag :  -noattr
Use case : removes all the unwanted additional information and gives the simplified netlist.

![image](https://github.com/user-attachments/assets/2abcded2-00d7-4eb5-a747-4762cc435e8a)
The above image shows the netlist file genrated for a good_mux module.


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

# Why different flavours of gate?
![image](https://github.com/user-attachments/assets/ea45ff50-551e-4de8-a4f3-7c54930dd361)

Lets assume the above circuit. Assume the propagation delay of the combinational network is say X ns. And the setup time of flop b is Tsetup\_B  and the clock to Q of flop A is TCQ\_A.
Now the clock time must be atleast greater than the sum of Clock-to-queue delay + combinational network delay + setup time of flop b.

So from the above statement we could conclude that, inorder for us to not have any setup time violation we need faster combination networks. so we need only faster circuits isn’t it ? But surprisingly the answer is NO!!.

Like setup time requirement we also have hold time requirement, which says
THOLD < TCQ_A + Tcomb

As we already saw the hold time is the time after the active edge of the clock for which the input must remain stable. So that means the fastest change in flop-A must reflect at the inputs of flop-B only after the hold time.
Hence in this case we may need circuits with larger delays.

Apart from this there are various other reasons for the requirement of slower cells like Area, Power, Capacitance etc… And always remember the speed doesn’t come at free of cost. Faster cells occupy more real estate as well as consume higher energy when compared their slower logical equivalents. 

Yes faster circuits yield faster system but they are bad interms of power and Area. And holdtime violations have higher probability of occurance. So the best way is to use the different flavours of cell in the optimum ways resulting in a balanced system in all aspects like speed power and area.

# What is .lib
---
It is a collection of all the standard logic modules and basic gates like And, Or, Not etc.. . Each logic modules and gates are available in different flavours like 2-input, 3-input etc… The flavour not only varies in the Fan-in and Fan-out but also in the other parameters like speed, area, delay time, etc…

## Library naming convention
PVT :
•	P – process
•	V – voltage
•	T – temperature
### Name  : “sky130_fd_sc_hd_tt_025C_1v80”
here,
>fd  -- foundry  -- meaning cells are model according to the foundry specification.

> Sc – standard cell 

> Hd – high density – this indicates that the library is optimized for minimal area.

> 025C – meaning the standard verified temperature is 25 degree centigrade

> 1v80 – the operating  voltage is 1.8 V .

The library file contains various information like
•	The technology used 
•	What is the delay model used.
•	What are the units of various parameters used in the library.
•	It contains the information of the PVT.
•	The file contains all the delay timing and leakage power for all the input and output combination.
> The keyword cell marks the beginning of the cell definition in the library.

# Hierarchal vs Flat synthesis
Hierarchal synthesis is the process in which various sub-modules of a Verilog files or imported as a black box and whatever inside it remains unrevealed.
Whereas in flat synthesis all the submodules are opened and realized using standard logic gates by the synthesizer.
The normal method of synthesization will always result in hierarchal synthesis for synthesizing a flat model we need to use the command,
```
command : flatten
```
Hierarchal synthesis is very helpful,
•	When there is a lot of instantiation of the same module.
•	And when we need to split up the whole design into various submodules to make the work of synthesizer easy and accommodatable.

> Additional tip: stacked PMOS is always not preferred. Even the synthesizer tries to avoid stacked PMOS as much as possible.


# Flip flop coding style
## Why flip flop in a circuit ?
The D flip flops acts like a barrier gate that isolates one combination logic network from another. This means the all the glitches and signal delays are neutralized and separated from one network to another making the design manageable.

## Two types of reset:
### Synchronous reset:

The reset signal is evaluated only at the active clock edge, which means the reset will occur only the reset signal is active only at the active edge of the clock. This can be achieved in hardware level by adding a mux at the input of the flop.

### Asynchronous reset:
An asynchronous reset is a reset which resets the system at any moment when the rest signal is high irrespective of the clock/inputs.

> NOTE: while synthesizing the flip flops in yosys we must use the keyword dfflibmap while reading the liberty file for importing the flip flop library.


# Optimization
Synthesizer is an intelligent software which tries to minimize the use of hardware as much as possible. For example multiplying by powers of 2 doesn’t require hardware it is just a left shift of bits in the hardware level inputs are directly connected to the outputs. Hence when we do synthesis of this logic the synthesizer would not infer any hardware but just rewire hence has no work for writing netlist.
## Combination logic optimization 
•	Logic simplification 
•	Constant propagation – direct optimization
![image](https://github.com/user-attachments/assets/e4dc865d-7ee0-4334-8221-2907decdb54d)

•	Boolean logic optimization
o	This is the process of simplifying the given Boolean expression in simpler and less complex term.

## Sequential logic optimization
- Sequential constant propagation
-- Ther might be few situation where the output always remains constant either 0 or 1 irrespective of the clock and input. This situation is called sequential constant propagation. The best example is when the input of a flip flop in grounded. There is no way the output can become high{assuming there is no set input}.
- State optimization
-- Optimization of unused states.
- Retiming
-- Retiming is the process of splitting the combinational network and redistribute it so that the delays are equally distributed.
- Sequential logic cloning
-- Clone a single flop into two same flops but in different paths so that we could counter act for the routing delay by using the positive slack at the separation point.
-- So the process of doing the optimization is taken care by the synthesizer itself. To do the optimization the command is,

```
Command : opt_clean -purge
```
> This command must be executed after the synthesis before doing the abc or writing the netlist.

## Unused output optimization
On performing optimization the synthesizer looks only for input and output any action or logic performed on internal signal that in no way affects the primary outputs are neglected by the synthesizer while performing optimization.

# Gate level simulation (GLS)
Gate level simulation is the process of testing the netlist using the same testbench designed for RTL model. This process verifies that the netlist is logically same as the RTL code. Apart from this we can also run timing aware GLS which checks for timing violation in the design.
![image](https://github.com/user-attachments/assets/5e1d14cf-a38b-4ae0-99d4-55751b7a6357)


## Reasons for synthesis simulation mismatch

- Missing sensitivity
-- Output are monitored only on change in input. If no activity in the input then output is not monitored
- Blocking and Non – blocking statements
-- The problem arises only inside the always block.
- [=] -> blocking assignment 
--Which means the first assignment is done after which the second assignment occurs
--The order of the assignment statement matters.
- [<=] -> non blocking assignment 
-- Which means all the RHS will be evaluated parallelly and then assigned to the LHS simultaneously.
-- The order of the assignment statement doesn’t matter
Always use non blocking statements for sequential circuits. And blocking statements for combinational circuits


# If construct

If is mainly used to create priority logic. Always the condition are checked according to the priority order once satisfied all the condition below it are not at all evaluated. This is usually constructed using nested mux.
The danger with if clause statements is it might lead to inferred latches if bad coding style is followed like if the else block is not defined in the code though it doesn’t show any error the at the hardware level it latches on to the previously exited value using a d latch which the tool synthesizes as a D latch and hence this is called inferred latch. If the circuit is sequential and the behaviour is the intended behaviour then the d-latch is completely fine but in case of combination circuit it creates problem.

# case construct
Case statements are also realized at the hardware level using a mux. But the problem is if the case statement is incomplete again it would create an inferred latch. So to rectify this, the best practice is to always use default case inside the case statement.
Similar to this partial assignment will also result in inferred latch. So to avoid it assign all the outputs in all the segments of cases.
The key difference between case and if-elseif-else clause is that whenever the higher prior condition excutes all the below condition cannot be even evaluated whereas in the case of case statements the code excutes in sequential manner if multiple condition are met it might lead to unpredictable output. This is because case statement check for all the conditions so the best practise is to avoid overlapping conditions in case statements.

# For loop
The for loop is always used inside the always block. It is always used to multiple evaluation or assigning expression and variables.

# Generate for loop
The generate for loop is used to generate or instantiate hardware modules multiple time. Whenever a single submodule is instantiated multiple times the generate for loop becomes more handy are creating and connecting them.

# Acknowledgements
- Kunal Gosh, Co-Founder (VSD Corp. Pvt Ltd)
- Shon Taware, Teaching Assistant (VSD Corp. Pvt Ltd)
# References
https://www.vlsisystemdesign.com/rtl-design-using-verilog-with-sky130-technology/
