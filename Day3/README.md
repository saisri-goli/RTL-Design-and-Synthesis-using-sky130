# Day 3 – Combinational, Sequential and Counter Optimizations

## Index

1. [Introduction to Types of Optimizations](#1-introduction-to-types-of-optimizations)
2. [Combinational Logic Optimization](#2-combinational-logic-optimization)
3. [Sequential and Counter Optimization](#3-sequential-and-counter-optimization)
4. [Learning Outcomes](#4-learning-outcomes)

---

## 1. Introduction to Types of Optimizations

### 1.1 What is RTL Optimization?

RTL optimization is the process of simplifying a digital design to reduce unnecessary hardware complexity while maintaining the intended functionality.

The main objectives of RTL optimization are:

* Reducing hardware area.
* Minimizing unnecessary logic gates and flip-flops.
* Improving circuit efficiency.
* Reducing power consumption.
* Improving timing performance where possible.

In this workshop, three types of optimization techniques were explored using Yosys and the SKY130 standard-cell library.

### 1.2 Types of Optimizations

**1. Combinational Logic Optimization**

Combinational optimization simplifies logic expressions using Boolean algebra, constant propagation and logic reduction.

Examples include:

* Replacing unnecessary multiplexers with simpler logic gates.
* Simplifying Boolean expressions.
* Removing redundant logic.
* Reducing the number of logic gates.

**2. Sequential Logic Optimization**

Sequential optimization focuses on circuits containing memory elements such as flip-flops.

It identifies constant values and redundant sequential logic while considering clock and reset behaviour.

Examples include:

* Sequential constant propagation.
* Removing unnecessary flip-flops.
* Identifying redundant sequential elements.

**3. Counter Optimization**

Counter optimization examines whether all counter bits are required to generate the observable outputs.

If only a subset of counter bits affects the output, unused bits and their associated logic may be eliminated during synthesis.

---

## 2. Combinational Logic Optimization

### 2.1 Introduction

Combinational logic optimization reduces the complexity of digital circuits by applying constant propagation and Boolean logic simplification.

In this workshop, four Verilog designs (`opt_check.v`, `opt_check2.v`, `opt_check3.v` and `opt_check4.v`) were synthesized using Yosys to observe how multiplexers and Boolean expressions are simplified into basic logic gates.

### 2.2 Yosys Optimization Commands

The following commands were used for the combinational optimization examples.

**Step 1: Launch Yosys**

```bash
yosys
```

Starts the interactive Yosys synthesis environment.

**Step 2: Load the SKY130 Standard-Cell Library**

```yosys
read_liberty -lib /home/saisri24/vlsi/rtl/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
```

Loads the SKY130 high-density standard-cell library in Liberty format.

**Step 3: Read the Verilog Design**

For example:

```yosys
read_verilog opt_check.v
```

Reads the specified Verilog RTL file into Yosys.

**Step 4: Perform Synthesis**

```yosys
synth -top opt_check
```

Synthesizes the design using `opt_check` as the top-level module.

**Step 5: Clean Up the Optimized Design**

```yosys
opt_clean -purge
```

This is an important cleanup command used in the optimization flow.

* `opt_clean` removes unused cells and wires remaining after optimization.
* `-purge` additionally removes internal nets even if they have public names.

This helps produce a cleaner design representation by removing unnecessary internal elements.

**Step 6: Perform Technology Mapping**

```yosys
abc -liberty /home/saisri24/vlsi/rtl/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
```

Invokes ABC to optimize the combinational logic and map it to the available SKY130 standard cells.

**Step 7: Visualize the Synthesized Circuit**

```yosys
show
```

Displays the synthesized circuit structure graphically using Graphviz and Xdot.

### 2.3 Optimization Example 1 – opt_check

The first design contains a multiplexer with one input tied to logic `0`.

The Boolean expression is:

```verilog
assign y = a ? b : 1'b0;
```

**Optimization:**

* When `a = 0`, the output is `0`.
* When `a = 1`, the output follows `b`.

Therefore:

$$
y = a \cdot b
$$

The multiplexer is simplified into a two-input AND gate.

**Yosys Commands:**

```yosys
read_verilog opt_check.v
synth -top opt_check
opt_clean -purge
abc -liberty /home/saisri24/vlsi/rtl/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```

**Image 1: Synthesized opt_check design showing the multiplexer simplified into an AND gate.**
<img width="610" height="240" alt="opt_check" src="https://github.com/user-attachments/assets/90f6cf3d-d8af-4d5b-b883-a13cd4889d24" />

### 2.4 Optimization Example 2 – opt_check2

The second design contains a multiplexer with one input tied to logic `1`.

The Boolean expression is:

```verilog
assign y = a ? 1'b1 : b;
```

**Optimization:**

* When `a = 1`, the output is `1`.
* When `a = 0`, the output follows `b`.

Therefore:

$$
y = a + b
$$

The multiplexer is simplified into a two-input OR gate.

**Yosys Commands:**

```yosys
read_verilog opt_check2.v
synth -top opt_check2
opt_clean -purge
abc -liberty /home/saisri24/vlsi/rtl/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```

**Image 2: Synthesized opt_check2 design showing the multiplexer simplified into an OR gate.**
<img width="598" height="145" alt="opt_check2" src="https://github.com/user-attachments/assets/96e1fac4-5c0b-4c6a-bc53-6f31ba8174ac" />

### 2.5 Optimization Example 3 – opt_check3

The third design contains nested multiplexers with connections to logic `0`.

The Boolean expression is:

```verilog
assign y = a ? (c ? b : 1'b0) : 1'b0;
```

**Optimization:**

Using constant propagation and Boolean simplification:

$$
y = a \cdot c \cdot b
$$

The nested multiplexers are simplified into a three-input AND gate.

**Yosys Commands:**

```yosys
read_verilog opt_check3.v
synth -top opt_check3
opt_clean -purge
abc -liberty /home/saisri24/vlsi/rtl/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```

**Image 3: Synthesized opt_check3 design showing a three-input AND gate.**
<img width="603" height="186" alt="opt_check3" src="https://github.com/user-attachments/assets/9b9b2f81-bdae-402f-87f7-b576d0c3ab0e" />

### 2.6 Optimization Example 4 – opt_check4

The fourth design contains a combination of conditional expressions and Boolean operations.

The original logic was analyzed and simplified by Yosys using constant propagation and Boolean logic optimization.

The resulting equivalent logic is an XNOR operation.

$$
y = a \odot c
$$

Here, XNOR produces logic `1` when both inputs have the same logical value.

**Yosys Commands:**

```yosys
read_verilog opt_check4.v
synth -top opt_check4
opt_clean -purge
abc -liberty /home/saisri24/vlsi/rtl/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```

**Image 4: Synthesized opt_check4 design showing an XNOR-equivalent implementation.**
<img width="606" height="187" alt="opt_check4" src="https://github.com/user-attachments/assets/adb15270-61c6-4bc4-8247-76d1da45195d" />

### 2.7 Multiple Module Optimization

Combinational optimization was also performed on designs containing multiple interconnected Verilog modules.

The `flatten` command was introduced to merge the submodules into the top-level design before cleanup and technology mapping.

#### Example 1 – multiple_module_opt

**Yosys Commands:**

```yosys
read_verilog multiple_module_opt.v
synth -top multiple_module_opt
flatten
opt_clean -purge
abc -liberty /home/saisri24/vlsi/rtl/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```

**Command Explanation:**

* `flatten` – Replaces module instances with their internal logic, removing the hierarchy.
* `opt_clean -purge` – Removes unused cells and internal nets.

The optimization allows Yosys to simplify logic across module boundaries after flattening the design.

**Image 5: Synthesized and optimized multiple_module_opt design.**
<img width="602" height="258" alt="mtple_mod_opt" src="https://github.com/user-attachments/assets/18ddd3eb-4e92-458b-8d59-6554fd38e897" />

#### Example 2 – multiple_module_opt2

The same synthesis flow was applied to the second multiple-module design.

The resulting output was simplified to a constant logic value, eliminating the need for unnecessary standard-cell logic.

**Image 6: Synthesized and optimized multiple_module_opt2 design.**
<img width="593" height="637" alt="mtple_mod_opt2" src="https://github.com/user-attachments/assets/f4a75a6a-7807-4219-8a10-e680021a8e92" />

---

## 3. Sequential and Counter Optimization

### 3.1 Sequential Logic Optimization

Sequential logic optimization focuses on simplifying circuits containing flip-flops while preserving their clock-dependent behaviour.

Unlike combinational logic, sequential circuits depend on their previous state, clock edges and reset conditions.

In this workshop, five designs (`dff_const1.v` to `dff_const5.v`) were simulated and synthesized to understand how Yosys identifies constant and redundant sequential logic.

### 3.2 Common Simulation Procedure

Each flip-flop design was simulated using Icarus Verilog, and the corresponding waveform was inspected using GTKWave.

For example, to simulate `dff_const1`:

**Step 1: Compile the design and testbench**

```bash
iverilog dff_const1.v tb_dff_const1.v
```

Compiles the Verilog design and its testbench.

**Step 2: Execute the simulation**

```bash
./a.out
```

Runs the compiled simulation and generates the VCD waveform file when waveform dumping is enabled in the testbench.

**Step 3: Open the waveform**

```bash
gtkwave tb_dff_const1.vcd
```

Displays the clock, reset and output signals in GTKWave.

The same procedure was followed for `dff_const2` through `dff_const5`, using their respective design and testbench filenames.

### 3.3 Common Synthesis Procedure

The flip-flop designs were synthesized using Yosys and the SKY130 standard-cell library.

The following sequence was used for each design.

```yosys
read_liberty -lib /home/saisri24/vlsi/rtl/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib

read_verilog dff_const1.v

synth -top dff_const1

dfflibmap -liberty /home/saisri24/vlsi/rtl/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib

abc -liberty /home/saisri24/vlsi/rtl/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib

show
```

For the remaining examples, the design filename and top-module name were changed to match the respective design.

### 3.4 DFF_CONST1 – Asynchronous Reset with Constant Data

The first design contains a flip-flop with an asynchronous active-high reset.

When reset is asserted, the output `q` is immediately forced to `0`. When reset is deasserted, the output does not immediately become `1`; it waits for the next positive clock edge.

This demonstrates that a constant data input does not necessarily make the flip-flop removable because the output depends on the reset and clock behaviour.

**Image 7: dff_const1 Verilog code and simulation waveform.**
<img width="1513" height="592" alt="dff_const1" src="https://github.com/user-attachments/assets/87fa051f-dc44-4df5-878f-c5ead8d14da4" />

The synthesized circuit retains the flip-flop because its output is not a constant under all reset and clock conditions.

**Image 8: Yosys synthesized circuit of dff_const1.**
<img width="787" height="272" alt="show_dff_const1" src="https://github.com/user-attachments/assets/5b4c3dc1-cbbe-44a1-b042-3349c30f1e54" />


### 3.5 DFF_CONST2 – Constant Output Flip-Flop

The second design assigns logic `1` to the flip-flop output in both the reset and normal operating conditions.

Therefore, the output remains `1` throughout the simulation, independent of the clock and reset transitions.

Since the output is constant, the flip-flop is unnecessary and can be optimized away by synthesis.

**Image 9: dff_const2 Verilog code and simulation waveform showing a constant HIGH output.**
<img width="1467" height="620" alt="dff_const2" src="https://github.com/user-attachments/assets/7989bb43-d475-4b93-9bcd-920f44680be5" />

Yosys identifies the constant output and eliminates the unnecessary sequential logic. Consequently, no flip-flop is required in the optimized implementation.

**Image 10: Yosys synthesized circuit of dff_const2 showing constant propagation.**
<img width="801" height="582" alt="show_Dff_const2" src="https://github.com/user-attachments/assets/2353379d-ac2d-4ac7-b16d-f72c5d8f94c8" />

### 3.6 DFF_CONST3 – Sequential Dependency Between Flip-Flops

The third design contains two flip-flops, where the output of one flip-flop feeds the input of the other.

During reset, the two flip-flops are assigned different values. After reset is deasserted, their values update on the active clock edge.
Due to clocked sampling and the dependency between the two flip-flops, the output exhibits a temporary transition in the waveform before reaching its stable value.

**Image 11: dff_const3 Verilog code and waveform showing the temporary output transition.**
<img width="1375" height="562" alt="dff_const3" src="https://github.com/user-attachments/assets/14c2749e-dfb2-44b5-a376-5ac39d574035" />

Both flip-flops are retained because the intermediate sequential behaviour affects the observable output.

**Image 12: Yosys synthesized circuit of dff_const3 showing the retained sequential elements.**
<img width="1517" height="388" alt="show_dff_const3" src="https://github.com/user-attachments/assets/715bc63e-1b86-453d-9342-3e42cb173b5a" />

### 3.7 DFF_CONST4 – Sequential Constant Propagation

The fourth design contains two flip-flops with values that remain constant at logic `1` under both reset and normal operating conditions.

Since the output remains HIGH throughout the simulation, the sequential logic does not contribute to any observable state change.

**Image 13: dff_const4 Verilog code and simulation waveform.**
<img width="1392" height="616" alt="dff_const4" src="https://github.com/user-attachments/assets/b34687c6-e074-48db-bed9-627e5203e575" />

The constant sequential logic is optimized away, and no unnecessary standard-cell flip-flops are retained.
Yosys can therefore propagate the constant value and eliminate the unnecessary flip-flops.

**Image 14: Yosys synthesized circuit of dff_const4 showing constant optimization.**
<img width="600" height="618" alt="show_dff_const4" src="https://github.com/user-attachments/assets/283ece99-9290-4487-966c-42a9df160ca9" />

### 3.8 DFF_CONST5 – Sequential Logic with Clock-Dependent Output

The fifth design contains two flip-flops with different reset and data assignments.

The output depends on the stored state of the flip-flops and their clocked updates. Even though the output eventually reaches a stable value, the intermediate behaviour must be preserved.

This demonstrates that a signal reaching a constant value after some clock cycles does not necessarily mean that the sequential logic can be eliminated.

**Image 15: dff_const5 Verilog code and simulation waveform.**
<img width="1501" height="626" alt="dff_const5" src="https://github.com/user-attachments/assets/f5315001-7946-478f-b7d5-7968c64fcb22" />

The sequential elements required to preserve the clock-dependent behaviour are retained in the synthesized circuit.

**Image 16: Yosys synthesized circuit of dff_const5.**
<img width="1503" height="286" alt="show_dff_const5" src="https://github.com/user-attachments/assets/513b76ae-dcb4-461a-8ebb-7159f4a4c882" />

---

### 3.9 Counter Optimization

Counter optimization is another form of sequential optimization that identifies unused counter bits and eliminates unnecessary hardware.

In this workshop, two 3-bit counter designs were analyzed to understand how the output logic affects the number of flip-flops required after synthesis.

### Example 1 – counter_opt

The design contains a three-bit counter, but the output `q` uses only `count[0]`.

The upper two counter bits do not affect the observable output and can therefore be removed during synthesis.

The least significant bit toggles on every clock cycle, allowing the optimized implementation to use a single flip-flop and inverter.

**Yosys Commands:**

```yosys
read_verilog counter_opt.v
synth -top counter_opt
dfflibmap -liberty /home/saisri24/vlsi/rtl/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty /home/saisri24/vlsi/rtl/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```

**Observation:**

Only the least significant counter bit contributes to the output. Therefore, the upper two bits and their associated logic are removed.

**Image 17: Synthesized counter_opt design showing one flip-flop and inverter.**
<img width="1517" height="247" alt="counter_opt" src="https://github.com/user-attachments/assets/6a070861-dc47-4a73-ac97-342dde3d6436" />

### Example 2 – counter_opt2

In this design, the output `q` depends on a comparison involving the complete counter:

```verilog
assign q = (count[2:0] == 3'b100);
```

Unlike the previous example, all three counter bits contribute to the output comparison.

Therefore, none of the counter bits can be removed as unused. All three flip-flops are required to preserve the original design functionality.

**Yosys Commands:**

```yosys
read_verilog counter_opt2.v
synth -top counter_opt2
dfflibmap -liberty /home/saisri24/vlsi/rtl/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty /home/saisri24/vlsi/rtl/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```

**Observation:**

Since the output depends on the complete three-bit counter value, all three flip-flops and the required comparison logic are retained.

**Image 18: Synthesized counter_opt2 design showing all three flip-flops and comparison logic.**
<img width="1512" height="416" alt="counter_opt2" src="https://github.com/user-attachments/assets/62341d4e-b6bb-4a88-9e67-8ea2fbc41f21" />

---

## Learning Outcomes

Through this workshop, I gained practical understanding of the following concepts:

1. **Combinational Optimization:** Understood how constant propagation and Boolean simplification convert unnecessary multiplexers into simpler AND, OR and XNOR logic.

2. **Multiple Module Optimization:** Learned how `flatten` removes module hierarchy and allows optimization across interconnected submodules.

3. **Sequential Constant Propagation:** Understood how reset conditions, clock transitions and stored values determine whether flip-flops can be removed or must be retained.

4. **Unused Counter Bit Optimization:** Learned how synthesis removes unused counter bits when they do not contribute to observable outputs.

5. **Yosys Synthesis Analysis:** Gained hands-on experience using `synth`, `opt_clean -purge`, `dfflibmap`, `abc` and `show` to analyze optimized hardware structures.

### Overall Understanding

This workshop demonstrated that RTL optimization is not simply about reducing the number of lines of Verilog code. It is about identifying unnecessary logic and sequential elements while preserving the complete observable behaviour of the design.

By analyzing combinational circuits, flip-flop coding styles and counters, I understood how Yosys applies optimization techniques to generate simpler and more efficient hardware implementations using the SKY130 standard-cell library.

---

## References

* [RTL Design and Synthesis – Lab 3 Reference](https://github.com/saumya73551/RTL-Design-/blob/main/lab3/README.md)
* [VSD RTL – Day 3 Introduction to Optimizations](https://github.com/ananyarf/VSD-RTL/tree/main/Day-03-Introduction-to-Optimizations)
* [Yosys Documentation](https://yosyshq.readthedocs.io/projects/yosys/en/latest/)

