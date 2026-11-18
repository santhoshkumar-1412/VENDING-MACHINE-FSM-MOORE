
# Vending Machine Controller | Verilog HDL

<p align="center">
  <b>RTL Design | Finite State Machine | Functional Verification</b>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Language-Verilog-blue?style=for-the-badge" />
  <img src="https://img.shields.io/badge/Architecture-Moore%20FSM-orange?style=for-the-badge" />
  <img src="https://img.shields.io/badge/Simulation-Synopsys%20VCS-green?style=for-the-badge" />
  <img src="https://img.shields.io/badge/Debugging-Synopsys%20Verdi-purple?style=for-the-badge" />
</p>

## 1. Project Overview

This project presents the RTL design and functional verification of a **Vending Machine Controller** using Verilog Hardware Description Language (HDL).

The vending machine accepts ₹5 and ₹10 coins as inputs and dispenses a product when the accumulated amount reaches ₹15 or more.

The design is implemented using a five-state Moore Finite State Machine (FSM), where each state represents the accumulated amount inserted into the machine.

The controller generates two outputs:

- **Dispense:** Becomes HIGH when the accumulated amount reaches ₹15 or ₹20.
- **Change:** Becomes HIGH when ₹20 is inserted, indicating that ₹5 change must be returned.

The design uses a sequential FSM architecture with asynchronous active-high reset, combinational next-state logic, and combinational output logic.

The RTL implementation is simulated using **Synopsys VCS**, while waveform visualization and signal-level debugging are performed using **Synopsys Verdi**.

## 2. Design Specifications

| Parameter | Description |
|---|---|
| Design Name | Vending Machine Controller |
| Design Type | Sequential Digital Circuit |
| Architecture | Finite State Machine |
| FSM Type | Moore FSM |
| Input Coins | ₹5 and ₹10 |
| Product Price | ₹15 |
| Number of States | 5 |
| State Encoding | 3-bit Binary |
| Clock | Positive-edge triggered |
| Reset | Asynchronous active-high |
| Outputs | Dispense, Change |
| HDL | Verilog |
| RTL Simulator | Synopsys VCS |
| Waveform Viewer | Synopsys Verdi |
| Waveform Format | FSDB and VCD |

## 3. Design Methodology

The Vending Machine Controller is implemented using a five-state Moore Finite State Machine.

Each state represents the total amount of money inserted into the machine.

### 3.1 FSM State Representation

The five states are defined using 3-bit binary encoding.

| State | Binary Encoding | Accumulated Amount |
|---|---|---|
| S0 | 000 | ₹0 |
| S5 | 001 | ₹5 |
| S10 | 010 | ₹10 |
| S15 | 011 | ₹15 |
| S20 | 100 | ₹20 |

### 3.2 State Transition Logic

The FSM transitions depend on the inserted coin values.

**From S0:**
- ₹5 coin → S5
- ₹10 coin → S10

**From S5:**
- ₹5 coin → S10
- ₹10 coin → S15

**From S10:**
- ₹5 coin → S15
- ₹10 coin → S20

**From S15:**
- Product is dispensed.
- Dispense = 1
- Change = 0
- FSM returns to S0 on the next clock edge.

**From S20:**
- Product is dispensed.
- ₹5 change is indicated.
- Dispense = 1
- Change = 1
- FSM returns to S0 on the next clock edge.

The controller gives priority to the ₹5 coin input if both coin inputs are HIGH simultaneously.

### 3.3 FSM State Transition Diagram

```text
                         ₹5
                 +---------------+
                 |               v
              +------+        +------+
              |  S0  |        |  S5  |
              +------+        +------+
                 |               |
                 | ₹10           | ₹5
                 v               v
              +------+
              | S10  |
              +------+
                 |
                 | ₹5
                 v
              +------+
              | S15  | ------> Dispense = 1
              +------+         Change   = 0
                 |
                 | Next Clock
                 v
                S0

       S5 -- ₹10 --> S15

       S10 -- ₹10 --> S20
                         |
                         v
                      +------+
                      | S20  |
                      +------+
                         |
                         v
                    Dispense = 1
                    Change   = 1
                         |
                         v
                        S0
```

### 3.4 Output Logic

The outputs are generated based on the current FSM state.

| Current State | Dispense | Change |
|---|---|---|
| S0 | 0 | 0 |
| S5 | 0 | 0 |
| S10 | 0 | 0 |
| S15 | 1 | 0 |
| S20 | 1 | 1 |

**Boolean Output Expressions**

**Dispense:**

Dispense = 1 when State = S15 or S20

**Change:**

Change = 1 when State = S20

Since both outputs depend only on the current FSM state, the controller follows Moore FSM architecture.

### 3.5 Clock and Reset Operation

The FSM state register is updated at every positive edge of the clock.

An asynchronous active-high reset initializes the FSM to the initial state S0.

```verilog
always @(posedge clk or posedge rst) begin
    if (rst)
        state <= s0;
    else
        state <= next_state;
end
```

## 4. RTL Module Architecture

The project consists of two Verilog modules.

| Module | Functionality |
|---|---|
| `vending_machine` | Implements FSM state transitions and output generation |
| `vending_machine_tb` | Generates clock, reset and coin inputs for functional verification |

### 4.1 Vending Machine Controller

The main RTL module implements:

- Five FSM states using 3-bit binary encoding.
- Asynchronous active-high reset.
- Positive-edge-triggered state register.
- Combinational next-state logic.
- Combinational output logic.
- Product dispensing and change indication.
- Automatic return to the initial state after dispensing.

### 4.2 FSM Architecture

The controller is designed using three major logic sections:

1. **State Register:** Stores the current FSM state and updates on the positive clock edge.
2. **Next-State Logic:** Determines the next state based on the current state and coin inputs.
3. **Output Logic:** Generates Dispense and Change signals based on the current state.

## 5. Repository Structure

```text
vending-machine-fsm-moore/
│
├── vending_machine.v
│   └── RTL source code
│
├── vending_machine_tb.v
│   └── Functional verification testbench
│
├── screenshots/
│   └── vending_machine_waveform.png
│
├── README.md
│
└── .gitignore
```

Generated simulation files such as FSDB databases, VCD files, compiled executables and intermediate simulation directories can be maintained locally rather than committed to GitHub.

## 6. Functional Verification

A Verilog testbench is developed to verify the functional behavior of the Vending Machine Controller.

The testbench generates a clock with a 10 ns period and applies different coin combinations to verify FSM state transitions and output responses.

### 6.1 Test Scenarios

Three test cases are implemented to verify the controller functionality.

| Test Case | Coin Sequence | Total Amount | Expected Dispense | Expected Change |
|:---:|---|---:|:---:|:---:|
| TC1 | ₹5 + ₹10 | ₹15 | 1 | 0 |
| TC2 | ₹10 + ₹10 | ₹20 | 1 | 1 |
| TC3 | ₹5 | ₹5 | 0 | 0 |

### 6.2 Test Case 1: ₹5 + ₹10

- Initially, the FSM is in S0.
- A ₹5 coin is inserted.
- FSM transitions from S0 to S5.
- A ₹10 coin is inserted.
- FSM transitions from S5 to S15.
- Dispense becomes HIGH.
- Change remains LOW.

**Expected Output:**

```text
Dispense = 1
Change   = 0
```

### 6.3 Test Case 2: ₹10 + ₹10

- Initially, the FSM is in S0.
- The first ₹10 coin is inserted.
- FSM transitions from S0 to S10.
- The second ₹10 coin is inserted.
- FSM transitions from S10 to S20.
- Dispense becomes HIGH.
- Change becomes HIGH.

**Expected Output:**

```text
Dispense = 1
Change   = 1
```

### 6.4 Test Case 3: Insufficient Amount

- Initially, the FSM is in S0.
- A ₹5 coin is inserted.
- FSM transitions from S0 to S5.
- The inserted amount is insufficient to purchase the product.

**Expected Output:**

```text
Dispense = 0
Change   = 0
```

### 6.5 Expected Simulation Behavior

```text
TC1: A=₹5, B=₹10
     State = S15
     Dispense = 1, Change = 0

TC2: A=₹10, B=₹10
     State = S20
     Dispense = 1, Change = 1

TC3: A=₹5
     State = S5
     Dispense = 0, Change = 0
```

## 7. Simulation Environment

The design is simulated using Synopsys VCS, and waveform analysis is performed using Synopsys Verdi.

### 7.1 Simulation Workflow

```text
       Verilog RTL Design
               |
               v
        Verilog Testbench
               |
               v
         Synopsys VCS
               |
               v
         RTL Simulation
               |
               v
       FSDB / VCD Database
               |
               v
         Synopsys Verdi
               |
               v
        Waveform Analysis
```

### 7.2 VCS Compilation

Compile the RTL source and testbench using:

```bash
vcs -full64 -debug_access+all \
vending_machine.v \
vending_machine_tb.v \
-o simv
```

Ensure that the Verdi FSDB PLI integration is configured correctly when generating FSDB waveforms.

### 7.3 Execute Simulation

```bash
./simv
```

The simulation executes the testbench and displays the monitored signal values in the terminal.

### 7.4 FSDB Waveform Generation

The following commands are included in the testbench to generate an FSDB waveform:

```verilog
initial begin
    $fsdbDumpfile("vending_machine.fsdb");
    $fsdbDumpvars(0, vending_machine_tb);
end
```

Ensure that the appropriate FSDB PLI support is enabled during VCS compilation.

### 7.5 VCD Waveform Generation

The testbench also supports VCD waveform generation:

```verilog
initial begin
    $dumpfile("vending_machine.vcd");
    $dumpvars(0, vending_machine_tb);
end
```

### 7.6 Open Waveform in Synopsys Verdi

To view the FSDB waveform:

```bash
verdi -ssf vending_machine.fsdb &
```

The generated waveform can be explored using the Verdi waveform viewer.

## 8. Waveform Analysis

Synopsys Verdi is used to inspect and analyze the simulation waveforms.

The following signals are observed:

| Signal | Width | Description |
|---|---:|---|
| `clk` | 1 bit | Clock input |
| `rst` | 1 bit | Asynchronous reset |
| `coin5` | 1 bit | ₹5 coin input |
| `coin10` | 1 bit | ₹10 coin input |
| `dispense` | 1 bit | Product dispensing output |
| `change` | 1 bit | Change indication output |

Waveform analysis helps to:

- Verify clock and reset behavior.
- Observe coin input transitions.
- Analyze FSM state transitions.
- Verify product dispensing at ₹15.
- Verify change indication at ₹20.
- Debug sequential and combinational logic.
- Inspect signal-level functionality.

### Simulation Waveform

The following screenshot shows the simulation signals observed in Synopsys Verdi.

![Vending Machine Verdi Waveform](screenshots/vending_machine_waveform.png)

## 9. Tools and Technologies

| Tool / Technology | Purpose |
|---|---|
| Verilog HDL | RTL design implementation |
| Finite State Machine | Controller architecture |
| Synopsys VCS | RTL compilation and simulation |
| Synopsys Verdi | Waveform visualization and debugging |
| FSDB | Simulation waveform database |
| VCD | Value Change Dump waveform |
| Git | Version control |
| GitHub | Source code management and documentation |

## 10. Key Learning Outcomes

Through this project, the following concepts are explored:

- Finite State Machine design.
- Moore FSM architecture.
- Binary state encoding.
- Sequential and combinational logic.
- Asynchronous reset implementation.
- State transition logic.
- Output generation using FSM states.
- Verilog RTL coding.
- Testbench development and functional verification.
- RTL simulation using Synopsys VCS.
- Waveform analysis using Synopsys Verdi.
- FSDB and VCD waveform generation.

## 11. Future Enhancements

Potential extensions to this project include:

- Adding support for additional coin denominations.
- Implementing a product selection mechanism.
- Supporting multiple products with different prices.
- Adding a coin return mechanism.
- Implementing an inventory management feature.
- Developing a parameterized vending machine controller.
- Performing RTL synthesis and area analysis.
- Performing timing analysis and gate-level verification.

## 12. Author

**SANTHOSH KUMAR N A**

RTL Design | Digital Design | VLSI Engineering

- GitHub: [Your GitHub Profile](https://github.com/your-username)
- LinkedIn: [Your LinkedIn Profile](https://linkedin.com/in/your-profile)

## 13. License

This project is intended for educational and learning purposes.

You are welcome to explore, modify and extend the implementation with appropriate attribution.

---

<p align="center">
  <b>Designed with Verilog HDL | Simulated with Synopsys VCS | Debugged with Synopsys Verdi</b>
</p>

<p align="center">
  ⭐ If you find this project useful, consider starring the repository!
</p>
