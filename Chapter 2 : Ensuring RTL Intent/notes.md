# Intro 
* Before synthesizing, the designer needs to ensure that the RTL actually implements the functionality that is desired. For this purpose, the designer runs a lot of simulations.
* Only when the designer is sure of the RTL achieving the desired functionality, the RTL is sent for synthesis and subsequent steps.
*  The main advantages of describing the design in RTL rather than logic gates are mainly two fold:
   1. Higher level of abstraction makes it easier to describe the design, compared to providing the gate level connectivity. 
   2. RTL simulations are much faster compared to the corresponding gate level.

# Need for Unambiguous Simulation 
* Simulation is unambiguous till the time the RTL has only one possible interpretation.
* RTL codes may have multiple interpretations. simulation tool will choose one of them.
* It's possible that  interpretation choosed by simulator differs from the realized. Now design functionality may be verified by simulation but real hardware exibit different behaviour. 
* **Functional verification** of an RTL code (through simulation) is one of the most basic and very fundamental aspect of a chip design and therefore, it is imperative that any RTL code that is written is un-ambiguous in terms of functionality.

# Simulation Race
* simulation race is the condition where multiple interpretations are correct. A simulator is free to choose any one of these multiple interpretations.
*  It is not necessary that the final realization will match the interpretation that your simulator chose.
*  Simulation race is usually encountered in **Verilog** and **SystemVerilog** code. VHDL does not have any simulation race.
*  SystemVerilog has provided ways to do away with a lot of race situations. However, it is still possible to have a SystemVerilog code, where a portion still has a race. This can happen, if the designer has of his code and not really exploited the full prowess of capabilities provided by SystemVerilog to avoid a race situation. 
*  In case of a design having a race:
    * Different simulators can give different results
    * Different versions of the same simulator can give different results
    * Same version of the same simulator can give different results – based on changes made to the code, which have seemingly no relation with the change in behavior being exhibited.
    
 ## 1. Read-Write Race
 * Read-Write race occurs when a signal is being read at the same time as being written-into.
 ### combinitional Read-Write
 * **Example:**
  ```verilog
always @ (b or c)
a = b & c;
always @ (b or d)
if (b) o = a ˆ d;
```
* **a** is written into in the first always block while being read by the second always block 
* if b is changed here we have two scenarios and simulator will choose  
    1. first always block is excecuted first so second always block read the updated value **a**.
    2. second always block is excecuted first so second always block read the old  value of **a**.
* solution for this race is simple : 
  * a is put into senstivity list that at worst case second always block is executed twice.
  * SystemVerilog provides a much more elegant solution to this kind of ambiguity. Instead of using the keyword, always, you should use *always_comb*.
  * Verilog provide a similar solution in **verilog 2001** . you should use always@(\*) for combinitional always. 
 
 ### sequential Read-Write
 * To illustrate this consider the following examble 
  ```verilog
always @ (posedge CLK)
  b=c ;
always @ (posedge CLK)
  a=b;
```
* **b** is written into first always block while being read by the second block.
*  when positive edge trigger here we have two scenarios and simulator will choose:  
    1. first always block is excecuted first so second always block read the updated value **b** and thus **a** , **b** get **C** value.
    2. second always block is excecuted first so  **a** gets the old value of **b** and **b** gets **c**.
* solution for this race is simple : 
  * For a sequential element, we should use a Non-Blocking Assignment (NBA).
  * With an NBA, the Right-Hand-Side is read immediately, but, the updating of the Left-Hand-Side happens after all the reads scheduled for the current time have already taken place. **Think of this as an infinitesimally small delay!!!**
  * Verilog provide a similar solution in **verilog 2001** . you should use always@(\*) for combinitional always. 
 ## 2. Write-Write Race
 * Write-Write race occurs when multiple values are being written into a signal – at the same time.
 * A Write-Write race can occur through combinations of:
    * **assign/assign statements** – if two assign statements try to update the same variable.However, typically, these are caught easily in simulations – as the assigned variable might tend to go to an X.
    *  **always/always statements** – if two always blocks try to update the same variable.These situations might be encountered only in a testbench. When used in a design this will result in a Multiple Driver scenario after synthesis – which is a violation of basic electrical requirement. The simulator might not necessarily see the Multiple Driver scenario!!!
 * The simplest solution to this ambiguity is to avoid updating a signal in more than one concurrent block. A signal should be updated in only one concurrent statement.
 ## 3. Always-Initial Race
 * Always-Initial race occurs when an initial block is updating a signal which also appears in the sensitivity list of an always block.
 ## 4. Race Due to Inter-leaving of Assign with Procedural Block
 ## Avoiding simulation race
 #### 1.  When modeling sequential logic, use nonblocking assignments.
 #### 2.  When modeling latches, use nonblocking assignments.
 #### 3.  When modeling combinational logic with an always block, use blocking assignments.
 #### 4.  When modeling both sequential and combinational logic within the same always block, use nonblocking assignments.
 #### 5.  Do not mix blocking and nonblocking assignments in the same always block.
 #### 6.  Do not make assignments to the same variable from more than one always block.
 #### 7.  Use $strobe to display values that been assigned using nonblocking assignments.
 #### 8.  Do not make assignments using #0 delays.

 #  Feedthroughs 
 # Simulation-Synthesis Mismatch
 # Latch Inference
 # Synchronous Reset
 # Limitations of Simulation
