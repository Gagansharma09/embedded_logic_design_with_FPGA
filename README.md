# Embedded Logic with FPGA

This repository documents my hands-on work in:
- RTL design (Verilog/SystemVerilog)
- FPGA-based embedded logic
- Zynq / PYNQ workflows
- Tool command-line usage (Vivado, Tcl)

This repo will evolve as I progress.

                                     experiment_01
tasks to be done in this experiment                                     
1-DESIGN OF 3bit ADDER Wand implement it verify it's functionality 
2-implement a 3x3 binary multiplexer using full adder and verify its functionality 
test the functionality with vio and fpga board

A- 3 bit full adder implemetation
              A[0]   A[1]   A[2]         B[0]   B[1]   B[2]                                 
              |      |      |             |      |      |
              +------+------+-------------+------+------+
                                 |
        +--------------------------------------------------+
        |                    top_adder                     |
        |                                                  |
        |    A  B            A  B            A  B          |
        |  +------+        +------+        +------+        |
        |  |  fa   |       |  fa   |       |  fa   |       |
        |  |       |       |       |       |       |       |
        |  |Sum Car|       |Sum Car|       |Sum Car|       |
        |  +------+        +------+        +------+        |
        |                                                  |
        +--------------------------------------------------+
                               |
                 |      |        |        |
             Sum[0]   Sum[1]   Sum[2]   Sum[3]


 +------------------------------------------------------+
 |                                                      |
 |                      FPGA                            |
 |                                                      |
 |    +----------------------------------------------+  |
 |    |                                              |  |
 |    |                top_adder                     |  |
 |    |                                              |  |
 |    |        [   fa   ]   [   fa   ]   [   fa   ]  |  |
 |    |                                              |  |
 |    +----------------------------------------------+  |
 |                                                      |
 |        A                B                 Sum        |
 |        ^                ^                  |         |
 |        |                |                  v         |
 +--------|----------------|------------------|---------+
          |                |                  |
          |                |                  |
 +------------------------------------------------------+
 |                      VIO IP                          |
 +------------------------------------------------------+







Step 3: Adding Clocking Wizard IP

To operate the counter at 1 Hz, we must generate a 1 Hz clock from the FPGA’s onboard clock.

Why is Clocking Wizard IP required?
	•	The onboard FPGA clock runs at 125 MHz
	•	A 1 Hz clock cannot be generated directly using Clocking Wizard alone
	•	Therefore, we combine:
	•	Clocking Wizard IP (to generate an intermediate clock)
	•	Clock Divider logic (to derive 1 Hz)
Design Options for Clock Scaling

Option 1: Direct division from 125 MHz
	•	Input: 125 MHz
	•	Output: 1 Hz
	•	Requires a very large divider
	•	Higher resource usage
	•	Larger timing margin and error risk
  Option 2: Two-stage clock generation (Recommended)
	1.	Use Clocking Wizard IP to generate a 5 MHz clock
	2.	Feed the 5 MHz clock into a clock divider
	3.	Divide down to 1 Hz

Preferred approach

Reasons:
	•	Smaller divider logic
	•	Better timing margins
	•	Reduced synthesis complexity
	•	More reliable clock behavior

Clocking Wizard Constraint
Important Note
The minimum output frequency supported by the Clocking Wizard IP is 4.7 MHz.

Therefore:
	•	We generate a 5 MHz clock using Clocking Wizard
	•	Then divide it down to 1 Hz using a counter-based divider

  125 MHz FPGA Clock
        │
        ▼
Clocking Wizard IP
(Generates 5 MHz)
        │
        ▼
Clock Divider Logic
(Divide by 5,000,000)
        │
        ▼
     1 Hz Clock
