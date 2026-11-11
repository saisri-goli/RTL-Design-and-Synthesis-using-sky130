# Day 2 – Timing Libraries, Hierarchical Synthesis and Efficient Flop Coding Styles

## Index

1. [Introduction to Timing Libraries](#1-introduction-to-timing-libraries)
2. [Types of RTL Synthesis](#2-types-of-rtl-synthesis)
3. [Flip-Flop Coding Styles](#3-flip-flop-coding-styles)
4. [RTL Logic Optimization](#4-rtl-logic-optimization)
5. [Conclusion and Learning Outcomes](#5-conclusion-and-learning-outcomes)

---

## 1. Introduction to Timing Libraries

### 1.1 What is a Liberty File?

A Liberty (`.lib`) file describes the electrical, functional and timing characteristics of standard cells available in a technology library.

It provides the information required by synthesis and timing analysis tools to understand the behaviour and performance of different standard cells.

The SKY130 high-density standard-cell library used in this workshop is:

`sky130_fd_sc_hd__tt_025C_1v80.lib`

### 1.2 Important Parameters in a Timing Library

A Liberty file contains several important parameters:

- **Cell Area:** Represents the physical area occupied by a standard cell.
- **Timing Arcs and Propagation Delay:** Describe the delay between input and output transitions.
- **Setup and Hold Time:** Specify the timing requirements for data around the active clock edge of a flip-flop.
- **Input Capacitance:** Represents the capacitance seen at a cell's input pin.
- **Output Transition:** Describes the rise and fall transition characteristics of an output signal.
- **Power Characteristics:** Provide information about the power consumption of standard cells.
- **Process, Voltage and Temperature (PVT):** Define the operating conditions under which the cell characteristics are characterized.

### 1.3 Process Corners

Standard-cell libraries are characterized at different process corners to account for variations in semiconductor manufacturing.

- **TT – Typical-Typical:** Represents typical NMOS and PMOS process characteristics.
- **FF – Fast-Fast:** Represents faster-than-typical transistor characteristics.
- **SS – Slow-Slow:** Represents slower-than-typical transistor characteristics.

These corners are used along with voltage and temperature conditions to evaluate circuit behaviour under different operating scenarios.

The library used in this workshop corresponds to the typical process corner at 25°C and 1.8 V.

---

## 2. Types of RTL Synthesis

RTL synthesis converts a Verilog description into a hardware logic representation. The synthesis process can be performed in different ways depending on the design hierarchy and optimization requirements.

In this workshop, hierarchical synthesis, flat synthesis and individual submodule synthesis were explored using Yosys.

### 2.1 Hierarchical Synthesis

Hierarchical synthesis preserves the module structure of the original RTL design.

A design containing multiple modules, such as OR and AND submodules, was loaded into Yosys to examine how the design hierarchy is maintained.

**Yosys Commands:**

Start Yosys:

```bash
yosys
```

Read the Verilog design:

```yosys
read_verilog multiple_modules.v
```

Select the top-level module:

Perform synthesis:

```yosys
synth -top multiple_modules
```

Visualize the synthesized design:

```yosys
show
```

**Image 1: Yosys visualization of multiple modules in the design.**
<img width="1032" height="362" alt="show_mul_mod" src="https://github.com/user-attachments/assets/5dceebcc-26a9-47e7-a772-bccb7ccf2e5f" />


**Image 2: Hierarchical synthesis visualization showing the preserved module structure.**
<img width="1091" height="752" alt="mul_mod_hier" src="https://github.com/user-attachments/assets/1fe78f78-62c6-421d-999f-31bb2fb8dcd9" />


### 2.2 Flat Synthesis

Flat synthesis converts a hierarchical design into a single-level logic representation by eliminating the module boundaries.

In Yosys, the `flatten` command is used to merge the submodules into the top-level design.

**Yosys Commands:**

```yosys
read_verilog multiple_modules.v
synth -top multiple_modules
abc -liberty /home/saisri24/vlsi/rtl/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
flatten
show
```

**Command Explanation:**

- `read_verilog` – Loads the Verilog design.
- `flatten` – Merges the submodules into the top-level module.
- `synth -top` – Synthesizes the flattened design.
- `show` – Displays the resulting logic structure.

The flattened representation allows Yosys to optimize the combined logic without maintaining the original module boundaries.

**Image 3: Flattened multiple-module RTL design representation.**
<img width="1092" height="777" alt="mul_mod_flat" src="https://github.com/user-attachments/assets/424aed7a-d779-4834-b695-f0ca7b2a31c1" />


**Image 4: Yosys visualization of the flattened multiple-module design.**
<img width="1586" height="417" alt="flatten_mul_mod" src="https://github.com/user-attachments/assets/f00b3618-139f-4567-b0ac-66fc877bc5ef" />


### 2.3 Submodule Synthesis

Submodule synthesis involves synthesizing an individual module independently instead of synthesizing the complete hierarchical design.

This helps in understanding the internal logic implementation of individual modules.

**Yosys Commands:**

For the first submodule:

```yosys
read_verilog multiple_modules.v
synth -top sub_module1
show
```
**Image 5: Synthesized logic visualization of Submodule 1.**
<img width="1307" height="412" alt="sub_module1" src="https://github.com/user-attachments/assets/e6813b1f-620b-4b2f-b96b-be18716f40e3" />

For the second submodule:

```yosys
read_verilog multiple_modules.v
synth -top sub_module2
show
```
**Image 6: Synthesized logic visualization of Submodule 2.**
<img width="1301" height="357" alt="sub_module2" src="https://github.com/user-attachments/assets/0f48e829-eaee-4c12-bd1e-ce9eafc11b44" />

---

## 3. Flip-Flop Coding Styles

### 3.1 Introduction to Flip-Flops

A flip-flop is a sequential logic element used to store one bit of information. Its output depends on the input and clock signal, along with any reset or set control signals.

Different flip-flop coding styles are used depending on the required control behaviour.

In this day, three flip-flop coding styles were studied:

1. Asynchronous Reset
2. Asynchronous Set
3. Synchronous Reset

The RTL designs were simulated to observe their behaviour and synthesized using Yosys with the SKY130 standard-cell library.

### 3.2 Standard-Cell Library Mapping

To map the flip-flop structures to available standard cells, the `dfflibmap` command was used.

The following command was executed after the synthesis stage and before ABC technology mapping:

```yosys
dfflibmap -liberty /home/saisri24/vlsi/rtl/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
```

**Explanation:**

- `dfflibmap` – Maps internal flip-flop representations to compatible flip-flop cells available in the technology library.
- `-liberty` – Specifies the Liberty file containing the standard-cell information.

This step helps translate generic sequential logic into technology-specific flip-flop implementations.

### 3.3 Asynchronous Reset Flip-Flop

An asynchronous reset flip-flop resets its output immediately when the reset signal is activated, without waiting for a clock edge.

The reset operation is independent of the clock.

**Behaviour:**

- When reset is activated, the output is immediately forced to logic 0.
- When reset is inactive, the flip-flop operates according to its clock and data input.

**Simulation:** The asynchronous reset waveform was observed to verify that the output responds immediately to the reset signal.

**Image 7: Simulation waveform of an asynchronous reset flip-flop.**
<img width="1512" height="516" alt="asyn_dff" src="https://github.com/user-attachments/assets/37235107-b7e5-46ab-9dff-44f8b58b8b4b" />


**Synthesis using Yosys:**

**Image 8: Yosys synthesis visualization of the asynchronous reset flip-flop.**
<img width="1236" height="248" alt="aff_asyncres_yosys" src="https://github.com/user-attachments/assets/b37c4d58-0f0e-4a86-abc8-f8d8feb37493" />


### 3.4 Asynchronous Set Flip-Flop

An asynchronous set flip-flop forces its output to logic 1 immediately when the set signal is activated, independently of the clock.

**Behaviour:**

- When set is activated, the output is immediately forced to logic 1.
- When set is inactive, the flip-flop operates according to its clock and data input.

**Simulation:** The asynchronous set waveform was observed to verify that the output becomes HIGH immediately when the set signal is activated.

**Image 9: Simulation waveform of an asynchronous set flip-flop.**
<img width="1513" height="515" alt="dff_async_Set" src="https://github.com/user-attachments/assets/8f24c913-0dfb-4f65-aa11-9c7dc8fc2e59" />


**Synthesis using Yosys:**

**Image 10: Yosys synthesis visualization of the asynchronous set flip-flop.**
<img width="1265" height="310" alt="dff_async_set yosys" src="https://github.com/user-attachments/assets/cff703b7-f693-4b41-bfad-ab3ef332d87b" />


### 3.5 Synchronous Reset Flip-Flop

A synchronous reset flip-flop resets its output only at the active clock edge.

Unlike asynchronous reset, activating the reset signal alone does not immediately change the output.

**Behaviour:**

- When reset is active at the triggering clock edge, the output is forced to logic 0.
- When reset is inactive, the flip-flop captures the input data at the active clock edge.

**Simulation:** The waveform was observed to verify that the output responds to the reset only at the active clock edge.

**Image 11: Simulation waveform of a synchronous reset flip-flop.**
<img width="1547" height="510" alt="dff_synch" src="https://github.com/user-attachments/assets/005ce14e-eccd-419a-b32f-edc63b862e65" />


**Synthesis using Yosys:**
**Image 12: Yosys synthesis visualization of the synchronous reset flip-flop.**
<img width="1290" height="298" alt="dff_syncres_yosys" src="https://github.com/user-attachments/assets/e6cf1403-bf4c-42cb-bf94-6b6188f01c89" />

---

## 4. RTL Logic Optimization

### 4.1 Introduction to RTL Optimization

RTL optimization reduces unnecessary hardware complexity by expressing arithmetic operations in simpler forms.

Certain arithmetic operations, especially multiplication by constants, can be implemented using bit shifting and concatenation instead of general-purpose multipliers.

This can help reduce the hardware required for implementing arithmetic expressions.

### 4.2 Multiplication by 2 – mult2

For a 3-bit unsigned input `a`, multiplication by 2 is equivalent to shifting the bits one position to the left.

**Original Expression:**

```verilog
assign y = a * 2;
```

**Optimized Expression:**

```verilog
assign y = {a, 1'b0};
```

**Explanation:**

A left shift by one position multiplies an unsigned binary number by 2.

The least significant bit is filled with zero. Therefore, the operation can be implemented using simple wiring instead of a general-purpose multiplier.

For a 3-bit input, the result requires 4 bits.

**Image 13: Yosys synthesized logic visualization of the multiplication-by-2 design (mult2).**
<img width="960" height="317" alt="mult_2" src="https://github.com/user-attachments/assets/ba1c73bf-2008-40a4-8a83-00b06b3f7f0a" />

### 4.3 Multiplication by 9 – mult8

Multiplication by 9 can be expressed as the sum of the input and its value shifted left by three positions.

Since:

\[
9a = 8a+a
\]

**Original Expression:**

```verilog
assign y = a * 9;
```

**Equivalent Expression:**

```verilog
assign y = (a << 3) + a;
```

For a 3-bit unsigned input, this can also be represented using concatenation:

```verilog
assign y = {a, a};
```

**Explanation:**

- `a << 3` shifts the input three positions to the left, multiplying it by 8.
- Adding the original input produces 9 times the input.
- `{a, a}` concatenates the input with itself, producing the same 6-bit result for a 3-bit input.

This representation demonstrates how constant multiplication can be simplified using bit concatenation and arithmetic properties.


**Image 14: Yosys synthesized logic visualization of the multiplication-by-9 design (mult8).**
<img width="602" height="203" alt="Screenshot 2026-11-11 122459" src="https://github.com/user-attachments/assets/2be2d8a4-2083-4445-a00c-24b7cabef914" />

---

## 5. Conclusion and Learning Outcomes

Day 2 provided an understanding of standard-cell timing libraries, different synthesis approaches, sequential logic implementation and RTL optimization techniques.

### Key Learning Outcomes

1. **Timing Libraries:** Understood the purpose of Liberty files and the importance of cell area, propagation delay, setup time, hold time and PVT process corners.

2. **Hierarchical and Flat Synthesis:** Learned how Yosys handles multi-module RTL designs, preserves module hierarchy and uses the `flatten` command to combine submodules into a single-level representation.

3. **Flip-Flop Coding Styles:** Studied asynchronous reset, asynchronous set and synchronous reset behaviour through RTL simulation and waveform analysis.

4. **Technology Mapping:** Understood how `dfflibmap` maps sequential logic to available SKY130 standard cells and how ABC performs technology mapping for combinational logic.

5. **RTL Optimization:** Learned how multiplication by constants can be simplified using bit shifting, arithmetic expressions and bit concatenation to create efficient hardware representations.

### Overall Understanding

This workshop demonstrated how RTL coding styles and design hierarchy influence synthesized hardware. It also provided practical experience in mapping sequential and combinational logic to the SKY130 standard-cell library and understanding how arithmetic expressions can be optimized before hardware implementation.
