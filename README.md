Download Link: https://assignmentchef.com/product/solved-project-1-cda-4630-5636-embedded-systems
<br>
This is an <strong>individual</strong> assignment. You are not allowed to take or give any help in completing this project. Please <strong>strictly</strong> <strong>follow the submission instructions</strong> (outlined at the end of this document) and submit your source code in e-Learning (http://elearning.ufl.edu/) website before the deadline. Please include the following sentence on top of your submission: “<strong>I have neither given nor received any unauthorized aid on this assignment</strong>”.

<strong>Petri Net Simulator for a Simplified MIPS Processor: </strong>In this project you will create a Petri Net simulator for a simplified MIPS Processor. The model will use colored tokens (token with values) rather than the default Petri net. Your simulator should be able to generate step-by-step simulation of the Petri net model of the MIPS processor described below. Please go through this document first and then view the sample input/output files in the class assignments page.

<strong> </strong>

Figure 1. Petri Net Model of a MIPS Processor                                                                        <strong> </strong>

<strong> </strong>

<strong> </strong>

<strong>We first describe three important places (instruction memory, register file, and data memory) of the Petri net model. Next, we describe ten transitions. The remaining places can be viewed as buffers. All the arc carries (consumes) 1 token unless marked otherwise. </strong>

<ol>

 <li><strong> Instruction Memory (INM):</strong></li>

</ol>

The processor to be simulated only supports four types of instructions: Add (ADD), Subtract (SUB), Multiply (MUL) and Store (ST). At a time step, the place denoted as Instruction Memory (INM) can have up to 16 instruction tokens. This is shown as <strong>Ii</strong> in Figure 1. We will provide an input file (instructions.txt) with instruction tokens. It supports the following instruction format. Please note that the “First Source Operand” is always register. The “Second Source Operand” is register for arithmetic instructions (ADD, SUB and MUL) and immediate value for store (ST) instruction.

&lt;Opcode&gt;, &lt;Destination Register&gt;, &lt;First Source Operand&gt;, &lt;Second Source Operand&gt;

Sample instruction tokens and equivalent functionality are shown below:

&lt;ADD, R1, R2, R3&gt;                              è R1 = R2 + R3

&lt;SUB, R4, R3, R5&gt;                               è R4 = R3 – R5

&lt;MUL, R7, R2, R3&gt;                              è R7 = R2 * R3

&lt;ST, R7, R1, 4&gt;                                    è DataMemory [R1+4] = R7

<ol start="2">

 <li><strong>Register File (RGF):</strong></li>

</ol>

This MIPS processor supports up to 16 registers (R0 through R15). At a time step, it can have up to 16 tokens. The token format is &lt;registername, registervalue&gt;, e.g., &lt;R1, 5&gt;. This is shown as <strong>Xi</strong> in Figure 1. We will provide an input file (registers.txt) with register tokens that you can use to initialize some of the registers.

<ol start="3">

 <li><strong>Data Memory (DAM):</strong></li>

</ol>

This MIPS processor supports up to 16 locations (address 0 to 15) in the data memory. At a time step, it can have up to 16 tokens. The token format is &lt;address, value&gt;, e.g., &lt;6, 5&gt; implies that memory address 6 has value 5. This is shown as <strong>Di</strong> in Figure 1.

<ol start="4">

 <li><strong>READ: </strong></li>

</ol>

The READ transition is a slight deviation from Petri net semantics since it does not have any direct access to instruction tokens. Assume that it knows the top (in-order) instruction in the Instruction Memory (INM). It checks for the availability of the source operands in the Register File (RGF) for the top instruction token and passes them to Instruction Buffer (INB) by replacing the source operands with the respective values. For example, if the top instruction token in INM is &lt;ADD, R1, R2, R3&gt; and there are two tokens in RGF as &lt;R2, 5&gt; and &lt;R3, 7&gt;, then the instruction token in INB would be &lt;ADD, R1, 5, 7&gt; once both READ and DECODE transitions are activated. In case any of the source registers are not available in RGF, then neither READ nor DECODE transition are executed. In other words, the top instruction token is not removed from the INM unless both source operands are available in the RGF. Therefore, both READ and DECODE transitions are executed together. Please note that when READ consumes two register tokens, it also returns them to RGF in the same time step (no change in RGF due to READ).

<ol start="5">

 <li><strong>DECODE:</strong></li>

</ol>

The DECODE transition consumes the top (in-order) instruction token from INM and updates the values of the source registers with the values from RGF (with the help of READ transition, as described above), and places the modified instruction token in INB.




<ol start="6">

 <li><strong>ISSUE1:</strong></li>

</ol>

ISSUE1 transition consumes one (in-order) arithmetic (ADD, SUB or MUL) instruction token (if any) from INB and places it in the Arithmetic Instruction Buffer (AIB). The token format for AIB is same as the token format in INB i.e., &lt;opcode, dest register, source value1, source value2&gt;.

<ol start="7">

 <li><strong>ISSUE2:</strong></li>

</ol>

ISSUE2 transition consumes one (in-order) store (ST) instruction token (if any) from INB and places it in the Store Instruction Buffer (SIB). The token format for SIB is same as the token format in INB i.e., &lt;opcode, dest register, source value1, source value2&gt;.




<ol start="8">

 <li><strong>Add – Subtract Unit (ASU) </strong></li>

</ol>

ASU transition performs arithmetic computations (ADD or SUB) and consumes one instruction (ADD or SUB) token (if any) from AIB, and places the result in the result buffer (REB). The format of the token in result buffer is same as a token in RGF i.e., &lt;destination-register-name, value&gt;.




<ol start="9">

 <li><strong>Multiply Unit – Stage 1 (MLU1) </strong></li>

</ol>

MLU1 consumes one instruction (MLU) token (if any) from AIB, and places the partial result in the partial result buffer (PRB). The token format for PRB is same as the token format in AIB i.e., &lt;opcode, dest register, source value1, source value2&gt;.




<ol start="10">

 <li><strong>Multiply Unit – Stage 2 (MLU2) </strong></li>

</ol>

Computes as per the instruction token from the PRB, and places the final result in the result buffer (REB). The format of the token in result buffer is same as a token in RGF i.e., &lt;destination-register-name, value&gt;.




<ol start="11">

 <li><strong>Address Calculation (ADDR)</strong></li>

</ol>

ADDR transition performs effective address calculation for the store instruction by adding the offset with the source operand. It produces a token as &lt;register-name, data memory address&gt; and places it in the address buffer (ADB).




<ol start="12">

 <li><strong>STORE: </strong></li>

</ol>

The STORE transition consumes a token from ADB and write the data to the data memory for the corresponding address. Assume that you will always have the data from the RGF. It places the data value in the Data Memory (DAM). The format of the token in DAM is already specified, i.e., &lt;address, value&gt;.




<ol start="13">

 <li><strong>WRITE</strong></li>

</ol>

Transfers the result (one token) from the Result Buffer (REB) to the register file (RGF). If there are more than one token in REB in a time step, the WRITE transition writes the token that belongs to the in-order first instruction. You do not have to worry about write-after-write hazard. We will not provide test cases that produces hazards or exceptions.

<strong> </strong>

<strong> </strong>

<strong> </strong>

<strong> </strong>

<strong> </strong>

<strong> </strong>

<strong><u>Command Line and Input/Output Formats</u></strong><strong>: </strong>

<strong>Command Line:</strong>

The simulator should be executed with the following command line:

<strong>./MIPSsim</strong>   or  <strong>java MIPSsim</strong>

Please hardcode the input and output files as follows:

Instructions (input):  instructions.txt

Registers (input): registers.txt

Data Memory (input): datamemory.txt

Simulation (output): simulation.txt <strong>File Formats:</strong>

<em>We will provide inputs in the specific format as listed below:</em> <u>Input Register File Format: (see registers.txt for example)</u>

&lt;register name,value&gt;

…

<u>Input Data Memory File Format: (see datamemory.txt for example)</u>

&lt;memory address,data value&gt;

…

<u>Input Instruction Memory File Format (see instructions.txt for example):</u>

&lt;opcode,dest,src1,src2&gt;

…

<u>Step-by-step Snapshot Output File Format  </u>(see simulation.txt for example): <strong>Please note that the following comments are not part of the output format</strong>.




STEP 0:

INM: I1,I2,I3,…      # Where Ii are comma separated instruction tokens.   INB:                  # Comma separated tokens with source values.

AIB:                  # Comma separated arithmetic instruction tokens

SIB:                  # Comma separated store instruction tokens

PRB:                  # Comma separated instruction tokens ADB:                  # Comma separated address tokens

REB:                  # Comma separated result buffer tokens

RGF:RF1,RF2,…         # Comma Separated register file tokens.

DAM:D1,D2,…           # Comma Separated data memory tokens. &lt;blank_line&gt; STEP 1:

Continue until the end of simulation. End of simulation is determined when none of the transitions can be fired in a time step.

Additional Notes: (refer to sample input and output files for ease of understanding)

<ul>

 <li>STEP 0 values represent initial states of all the places. STEP 1 represents the tokens of all the places at the end of first time step. In general, STEP i values should reflect the tokens of all the places at the end of time step i. In each time step, you are supposed to execute each transition exactly once (if it has required input tokens at the beginning of that cycle).</li>

 <li>When there are more than one tokens in a place, please print them in instruction order (<strong>in-order</strong>) except for DAM and RGF. The token for DAM and RGF should be printed in the sorted order based on memory address or register name (starting with smallest), respectively.</li>

</ul>

<strong><u>Submission Policy</u>: </strong>

Please follow the submission policy outlined below. There will be up to 10% <strong>score penalty</strong> based on the nature of submission policy violations.

<ol>

 <li>Please submit only one source file. <strong>Please add “.txt” at the end of your filename</strong>. Your file name must be MIPSsim (e.g., MIPSsim.c.txt <strong>or</strong>cpp.txt <strong>or</strong> MIPSsim.java.txt). On top of the source file, please include the sentence: “/* On my honor, I have neither given nor received unauthorized aid on this assignment */”.</li>

 <li>Please test your submission. These are the exact steps we will follow too.

  <ul>

   <li>Download your submission from eLearning (ensures your upload was successful).</li>

   <li>Remove “.txt” extension (e.g., MIPSsim.c.txt should be renamed to MIPSsim.c) o Login to <strong>cise.ufl.edu</strong> or storm.cise.ufl.edu If you don’t have a CISE account, go to http://cise.ufl.edu/help/account.shtml and apply for one CISE class account. Then you use <strong>putty</strong> and <strong>winscp</strong> or other tools to login and transfer files.</li>

   <li>Please compile to produce an executable named <strong>MIPSsim</strong>.

    <ul>

     <li>gcc MIPSsim.c –o MIPSsim<strong> or</strong>  javac MIPSsim.java  <strong>or</strong>                                        g++ MIPSsim.cpp –o MIPSsim           <strong>or </strong> g++ -std=c++0x MIPSsim.cpp -o MIPSsim</li>

    </ul></li>

   <li>Please do not print anything on screen.</li>

   <li>Execute to generate simulation file and test with the correct one

    <ul>

     <li>./MIPSsim <strong>or</strong>      java  MIPSsim</li>

     <li>diff –w –b –B simulation.txt txt</li>

    </ul></li>

  </ul></li>

</ol>

<ol start="3">

 <li><em>In previous years, there were many cases where output format was different, filename was different, command line arguments were different, or e-Learning submission was missing, All of these led to unnecessary frustration and waste of time for TA, instructor and students. </em><strong>Please use the exactly same commands as outlined above to avoid 10% score penalty.</strong></li>

 <li><strong>You are not allowed to take or give any help in completing this project.</strong> In the previous years, some students violated academic honesty (giving help or taking help in completing this project). We were able to establish violation in several cases – those students received “0” in the project and their names were reported to UF Ethics office. This year we would also impose one additional letter grade penalty.</li>

</ol>

Someone could potentially lose two letter grade points (e.g., “A-” grade to “B” grade) – one for getting 0 score in the project and then another grade point penalty on top of it. Moreover, the names of the students will also be reported to UF Dean of Students Office (DSO). If your name is already in DSO for violation in another course, the penalty for second offence is determined by DSO. In the past, two students from my class were suspended for a semester due to repeat academic honesty violation (implies deportation for international students).

<strong><u>Grading Policy</u> </strong>

The class assignments webpage has the sample input and output files. Correct handling of the sample input (with possible changes of register and data values) will be used to determine 60% of credit awarded. The register and data memory values will be between 0 and 63. The remaining 40% will be determined from other input test cases that you will not have access prior to grading. The other test cases can have different number or order of instructions as well as different register and data memory tokens. It is recommended that you construct your own sample input files with which to further test your simulator. <strong>Please note that the new test will NOT test any hazards, exceptions or scenarios that are not described in this document</strong>.