# 16-bit Pipelined RISC Processor

A 16-bit RISC processor with a five-stage pipeline, designed and simulated in Logisim-evolution. It includes a custom ALU, a forwarding unit that resolves data hazards without stalling, and hazard detection for load-use and control hazards.

Course project for COE 301 (Computer Architecture and Assembly Language) at King Fahd University of Petroleum and Minerals (KFUPM), Spring 2025.

## Features

- **Five-stage pipeline:** Instruction Fetch, Instruction Decode, Execute, Memory Access, and Write Back.
- **Harvard architecture:** separate word-addressable instruction and data memories, each 2^16 x 16 bits.
- **Eight registers:** seven general-purpose 16-bit registers (R1-R7), with R0 hardwired to zero.
- **Three instruction formats:** R-type, I-type, and J-type.
- **Data forwarding:** forwarding paths to the ALU inputs, store data, branch comparison, and jump-register address.
- **Hazard detection:** a one-cycle stall for load-use hazards and for taken branches and jumps.
- **Modular ALU:** separate arithmetic, logic, shift/rotate, and bit-reversal units.

## Instruction Set

| Type | Instructions |
|---|---|
| R-type arithmetic | ADD, SUB, SLT, SLTU |
| R-type logical | AND, OR, XOR, NOR |
| R-type shift and rotate | SLL, SRL, SRA, ROR |
| R-type bit reversal | REVL, REVH, REVA |
| R-type jump | JR |
| I-type arithmetic and logical | ADDI, ANDI, ORI, XORI |
| I-type memory | LW, SW |
| I-type branch | BEQ, BNE |
| J-type | J, JAL, LUI |

## Architecture

### Pipeline stages

| Stage | Function |
|---|---|
| IF | Fetches the instruction from instruction memory |
| ID | Decodes the instruction and reads register values |
| EX | Performs the ALU operation and resolves branches |
| MEM | Reads or writes data memory |
| WB | Writes the result back to the register file |

### Control

The main control unit generates the datapath control signals from the 5-bit opcode. A separate ALU control unit combines `ALUOp` with the function field to produce a 4-bit ALU control signal.

### Forwarding

The forwarding unit compares register addresses across the pipeline registers and drives the forwarding multiplexers:

| Signal | Purpose |
|---|---|
| ForwardA, ForwardB | First and second ALU inputs |
| ForwardC, ForwardD | Branch comparison operands |
| ForwardE | Jump-register (JR) target address |

Each selects the value from the MEM stage, the WB stage, or the register file.

### Stalling

- **Load-use hazard:** when an instruction needs a value loaded by the instruction directly before it, the PC and IF/ID register are held and a bubble is inserted into ID/EX.
- **Control hazard:** branches are resolved in the EX stage, so a taken branch or a jump costs one cycle.

## Repository Structure

```
.
├── 16Bit_CPU.circ              # Complete processor (open this file)
├── RegisterFileCir.circ        # Register file
├── ALU.circ                    # Complete ALU
├── adder_suber.circ            # Adder/subtractor
├── FA_using_TruthTable.circ    # Full adder
├── Logic_Unit.circ             # AND, OR, XOR, NOR unit
├── shifterr.circ               # Shift and rotate unit
├── REVL.circ / REVH.circ / REVA.circ   # Bit-reversal units
├── ALUOp.circ                  # ALU control unit
├── ForwardControl.circ         # Forwarding unit
├── PCsourcepip1.circ           # Next-PC selection logic for the pipeline
├── DataMemory.circ             # Data memory
├── instractionMemo_DataMemo.circ   # Instruction and data memories
├── IstSpliter.circ             # Instruction field splitter
├── testCase                    # Test program as a Logisim memory image (hex)
├── Test Cases Excel.xlsx       # Test cases and expected results
└── Project Report COE301.docx  # Full design report
```

`16Bit_CPU.circ` contains both a single-cycle version (`Main_CPU`) and the pipelined version (`Pip_CPU`) as subcircuits.

## Getting Started

### Requirements

- [Logisim-evolution](https://github.com/logisim-evolution/logisim-evolution)

### Run a program

1. Open `16Bit_CPU.circ` in Logisim-evolution.
2. Open the `Pip_CPU` circuit.
3. Right-click the instruction memory, choose **Load Image**, and select `testCase`.
4. Use **Simulate > Tick Full Cycle** to step through execution, or enable **Auto-Tick** to run it.
5. Watch the register file and data memory to check the results.

## Testing

Each component was tested on its own before integration, then every instruction was verified in the full processor. Dedicated test sequences cover:

- EX-to-EX and MEM-to-EX forwarding
- Load-use stalls
- Taken and not-taken branches
- Jumps and procedure calls with JAL and JR

A complete test program initialises an array in memory, calls a procedure to sum it, and stores the result.

## Team

Ahmed Malaekah, Abdulmlek Sait, Ibrahim Albahrani

## References

- D. Patterson and J. Hennessy, *Computer Organization and Design*: basis for the five-stage pipeline structure.

## Tools

Logisim-evolution
