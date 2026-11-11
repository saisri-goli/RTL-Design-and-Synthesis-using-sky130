# Day 1 – RTL Design and Synthesis Workshop

## Index

1. [Getting the Tools](#1-getting-the-tools)
2. [Git Cloning – RTL Design and Synthesis Workshop](#2-git-cloning--rtl-design-and-synthesis-workshop)
3. [Good Mux – RTL Simulation](#3-good-mux--rtl-simulation)
4. [Good Mux – Logic Synthesis using Yosys](#4-good-mux--logic-synthesis-using-yosys)
5. [Conclusion and Learning Outcomes](#5-conclusion-and-learning-outcomes)

---

## 1. Getting the Tools

### 1.1 Introduction

Before starting RTL design and synthesis, the required open-source tools were installed in the Ubuntu environment.

The following tools were used throughout the workshop:

- **Icarus Verilog:** An open-source Verilog compiler and simulator used to compile and simulate RTL designs.
- **GVim:** A graphical text editor used to create, edit and view Verilog source files and synthesized netlists.
- **GTKWave:** A waveform viewer used to analyze digital signal transitions generated during simulation.
- **Yosys:** An open-source RTL synthesis tool used to convert Verilog RTL descriptions into gate-level logic and perform technology mapping.
- **Xdot and Graphviz:** Supporting tools used to visualize the synthesized circuit structure.

### 1.2 Installation Commands

First, update the Ubuntu package lists:

```bash
sudo apt update
```

This command refreshes the package information from the configured Ubuntu repositories.

Install the required tools:

```bash
sudo apt install -y iverilog gvim gtkwave yosys xdot graphviz
```

The `-y` option automatically confirms the installation prompts.

### 1.3 Tool Version Verification

After installation, the following commands were used to verify the tools.

**Icarus Verilog:**

```bash
iverilog -V
```

Displays the installed Icarus Verilog compiler version.

**Image 1: Icarus Verilog version verification.**
<img width="960" height="253" alt="iverilog" src="https://github.com/user-attachments/assets/59ec0084-dea2-454f-9a91-604f65b45728" />


**GTKWave:**

```bash
gtkwave --version
```

Displays the installed GTKWave version.

**Image 2: GTKWave version verification.**
<img width="1101" height="465" alt="gtkwave" src="https://github.com/user-attachments/assets/08ffaee0-6df9-453b-9a33-21faaf173888" />


**Yosys:**

```bash
yosys -V
```
**Image 3: Yosys version verification.**
<img width="985" height="620" alt="yosys" src="https://github.com/user-attachments/assets/5d6ed776-c907-4c8b-9771-f335b70a2805" />


---

## 2. Git Cloning – RTL Design and Synthesis Workshop

### 2.1 Cloning the Repository

The workshop source repository was downloaded from GitHub using Git.

**Command:**

```bash
git clone https://github.com/kunalg123/sky130RTLDesignAndSynthesisWorkshop.git
```

**Explanation:**

- `git clone` downloads a remote GitHub repository to the local Ubuntu system.
- The repository contains workshop materials, Verilog source files, standard-cell libraries and other supporting resources.

**Image 4: Successfully cloning the RTL Design and Synthesis Workshop repository from GitHub.**
<img width="1527" height="920" alt="git_clone" src="https://github.com/user-attachments/assets/b43cb536-c4d2-41dd-82d4-2f4dd4113f9b" />


### 2.2 Exploring the Verilog Files

After cloning, navigate to the `verilog_files` directory:

```bash
cd sky130RTLDesignAndSynthesisWorkshop/verilog_files
```

This directory contains the Verilog RTL designs and testbench files used throughout the workshop.

To display the available files:

```bash
ls 
```

The command lists the files and directories along with their permissions, sizes and other details.

**Image 5: Verilog source files available in the cloned workshop repository.**
<img width="1073" height="512" alt="git_check" src="https://github.com/user-attachments/assets/017a1f52-f6f0-4dd2-b164-955dc4337342" />


---

## 3. Good Mux – RTL Simulation

### 3.1 Introduction to RTL Simulation

RTL (Register Transfer Level) describes the behaviour and data flow of a digital circuit using a hardware description language such as Verilog.

RTL simulation is the process of executing the Verilog design with test inputs to observe its output behaviour before proceeding to hardware synthesis.

For this exercise, a 2:1 multiplexer (`good_mux.v`) was simulated using a separate testbench (`tb_good_mux.v`).

**Working Principle of a 2:1 Multiplexer:**

A 2:1 multiplexer selects one of two inputs based on the select signal.

- When `sel = 0`, the output `y` follows input `i0`.
- When `sel = 1`, the output `y` follows input `i1`.

### 3.2 Simulation Commands

**Step 1: Compile the Verilog Design and Testbench**

```bash
iverilog good_mux.v tb_good_mux.v
```

This command compiles the RTL design and its testbench using Icarus Verilog.

The compilation generates an executable file named `a.out` by default.

**Step 2: Execute the Simulation**

```bash
./a.out
```

This executes the compiled simulation.

The testbench generates input combinations, evaluates the multiplexer behaviour and produces a VCD (Value Change Dump) file if waveform dumping is enabled in the testbench.

**Step 3: Open the Generated VCD File**

```bash
gtkwave tb_good_mux.vcd
```

This opens the generated VCD file in GTKWave for waveform analysis.

### 3.3 Simulation Output

The simulation generated the waveform file `tb_good_mux.vcd`, which contains the recorded signal transitions of the testbench.

**Image 6: Successful RTL simulation and generation of the VCD waveform file.**
<img width="1335" height="182" alt="good_mux_vcd" src="https://github.com/user-attachments/assets/9b9dc834-14d3-4457-bd9f-ff822e331128" />


### 3.4 Waveform Analysis using GTKWave

GTKWave was used to visualize and analyze the simulated signals.

The following signals were observed:

- `i0` – First data input.
- `i1` – Second data input.
- `sel` – Selection control signal.
- `y` – Multiplexer output.

**Waveform Interpretation:**

- When `sel = 0`, the output follows `i0`, irrespective of the value of `i1`.
- When `sel = 1`, the output follows `i1`, irrespective of the value of `i0`.

The observed waveform confirms the expected functionality of the 2:1 multiplexer.

**Image 7: GTKWave waveform showing the input signals, select signal and output of the 2:1 multiplexer.**
<img width="1581" height="495" alt="good_mux_ourpur" src="https://github.com/user-attachments/assets/94c70dc4-4e6d-4d59-80de-ea89526cde5d" />

---

## 4. Good Mux – Logic Synthesis using Yosys

### 4.1 Introduction to Logic Synthesis

Logic synthesis is the process of converting an RTL description into a gate-level representation of digital hardware.

In this exercise, Yosys was used to synthesize the `good_mux` Verilog module using the SKY130 standard-cell library.

The library file used was:

`sky130_fd_sc_hd__tt_025C_1v80.lib`

This is the SKY130 high-density standard-cell library characterized at the typical process corner, 25°C and 1.8 V.

### 4.2 Launching Yosys

From the `verilog_files` directory, start Yosys:

```bash
yosys
```

This opens the interactive Yosys synthesis environment, where synthesis commands can be executed.

### 4.3 Reading the Standard Cell Library

**Command 1:**

```yosys
read_liberty -lib /home/saisri24/vlsi/rtl/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
```

**Explanation:**

- `read_liberty` reads the standard-cell library in Liberty format.
- `-lib` loads the library cells as black-box definitions rather than implementing their internal transistor-level functionality.
- The library provides the cell definitions and characteristics required for technology mapping.

**Command 2: Read the Verilog RTL**

```yosys
read_verilog good_mux.v
```

Reads the Verilog source file and converts the RTL module into Yosys's internal design representation.

**Command 3: Perform RTL Synthesis**

```yosys
synth -top good_mux
```

Performs synthesis of the design and identifies `good_mux` as the top-level module.

The synthesis process transforms the behavioural RTL description into a gate-level logic representation.

**Image 8: Yosys commands for loading the SKY130 standard-cell library, reading the Verilog RTL and performing synthesis.**
<img width="1507" height="560" alt="read_lib , verlog" src="https://github.com/user-attachments/assets/d4b3d8c8-7cd3-4b41-897e-23f280504615" />


### 4.4 Technology Mapping using ABC

**Command 4:**

```yosys
abc -liberty /home/saisri24/vlsi/rtl/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
```

**Explanation:**

- `abc` invokes the ABC logic optimization and technology mapping engine.
- `-liberty` specifies the standard-cell library used for technology mapping.
- ABC optimizes the combinational logic and maps it to suitable cells available in the SKY130 library.

This step helps convert the synthesized logic into a technology-specific gate-level implementation.

**Image 9: ABC technology mapping output using the SKY130 standard-cell library.**
<img width="1448" height="661" alt="abc" src="https://github.com/user-attachments/assets/2dd68f3d-6cbe-4d1e-8b9d-79114452a092" />


### 4.5 Visualizing the Synthesized Circuit

**Command 5:**

```yosys
show
```

**Explanation:**

The `show` command generates a graphical representation of the synthesized circuit.

Yosys uses Graphviz and Xdot to display the circuit structure, allowing the internal logic and connections to be visually inspected.

**Image 10: Synthesized good_mux circuit visualization using Yosys show and Xdot.**
<img width="812" height="388" alt="show" src="https://github.com/user-attachments/assets/785b10de-5371-448d-9011-97bdf54c14eb" />


### 4.6 Generating the Synthesized Verilog Netlist

**Command 6:**

```yosys
write_verilog good_mux_netlist.v
```

**Explanation:**

This command exports the synthesized design into a Verilog netlist named `good_mux_netlist.v`.

The generated file represents the synthesized hardware structure.

**Command 7:**

```yosys
!gvim good_mux_netlist.v
```

**Explanation:**

The `!` prefix executes a shell command from the Yosys console.

GVim opens the generated Verilog netlist so that the synthesized design can be inspected.

**Image 11: Generated good_mux_netlist.v file opened in GVim.**
<img width="1088" height="682" alt="write_verilog" src="https://github.com/user-attachments/assets/eaab9b4d-9b2d-4f2e-aa6b-58f8a9db83ea" />


### 4.7 Generating a Netlist Without Attributes

**Command 8:**

```yosys
write_verilog -noattr good_mux_netlist.v
```

**Explanation:**

The `-noattr` option prevents Yosys-specific attributes from being included in the generated Verilog netlist.

This produces a cleaner Verilog representation without synthesis attributes.

To inspect the generated file again:

```yosys
!gvim good_mux_netlist.v
```

**Image 12: Synthesized Verilog netlist generated without Yosys-specific attributes and inspected using GVim.**
<img width="1177" height="556" alt="noattr" src="https://github.com/user-attachments/assets/d5bb5faa-ade7-43c6-b508-269fb9be8cfa" />

---

## 5. Conclusion and Learning Outcomes

Day 1 provided practical exposure to RTL simulation and logic synthesis using open-source EDA tools and the SKY130 standard-cell library.

### Key Learning Outcomes

1. **Tool Installation and Setup:** Installed and verified Icarus Verilog, GVim, GTKWave, Yosys, Xdot and Graphviz in the Ubuntu environment.

2. **RTL Simulation:** Understood how Verilog RTL and testbench files are compiled using Icarus Verilog and executed to generate simulation results.

3. **Waveform Analysis:** Used GTKWave to inspect input signals, select signals and output transitions of a 2:1 multiplexer.

4. **Logic Synthesis:** Used Yosys to read Verilog RTL, perform synthesis, optimize logic using ABC and map the design using the SKY130 standard-cell library.

5. **Netlist Generation and Visualization:** Generated the synthesized Verilog netlist, inspected it using GVim and visualized the synthesized circuit using Xdot.

### Overall Understanding

The RTL design flow starts with writing a behavioural Verilog description, followed by simulation to verify its functionality. Once the design behaviour is confirmed, synthesis converts the RTL into a hardware representation. Technology mapping then uses a standard-cell library to implement the logic using available cells.

This exercise provided a practical understanding of the transition from Verilog RTL to a technology-mapped digital circuit using the SKY130 platform.
