# RISC-V RV32IM ISA Implementation
### Overview
RISC-V is an open standard instruction set architecture (ISA) that emphasizes simplicity, modularity, and extensibility. The RV32IM extension adds integer multiplication and division instructions to the base integer instruction set (RV32I).

Our project currently supports a few instructions, with ongoing development to expand the implementation.

### Supported Instructions
The following table summarizes the current status of instruction implementation:

| **Instruction** | **Category**      | **Status**         | **Description**                             |
|------------------|-------------------|--------------------|---------------------------------------------|
| `LUI`            | Control Flow      | 🚧 Planned         | Load Upper Immediate                        |
| `AUIPC`          | Control Flow      | 🚧 Planned         | Add Upper Immediate to PC                   |
| `JAL`            | Control Flow      | 🚧 Planned         | Jump and Link                               |
| `JALR`           | Control Flow      | 🚧 Planned         | Jump and Link Register                      |
| `BEQ`            | Control Flow      | 🚧 Planned         | Branch if Equal                             |
| `BNE`            | Control Flow      | 🚧 Planned         | Branch if Not Equal                         |
| `BLT`            | Control Flow      | 🚧 Planned         | Branch if Less Than                         |
| `BGE`            | Control Flow      | 🚧 Planned         | Branch if Greater Than or Equal             |
| `BLTU`           | Control Flow      | 🚧 Planned         | Branch if Less Than Unsigned                |
| `BGEU`           | Control Flow      | 🚧 Planned         | Branch if Greater Than or Equal Unsigned    |
| `LB`             | Memory Access     | 🚧 Planned         | Load Byte                                   |
| `LH`             | Memory Access     | 🚧 Planned         | Load Halfword                               |
| `LW`             | Memory Access     | 🚧 Planned         | Load Word                                   |
| `LBU`            | Memory Access     | 🚧 Planned         | Load Byte Unsigned                          |
| `LHU`            | Memory Access     | 🚧 Planned         | Load Halfword Unsigned                      |
| `SB`             | Memory Access     | 🚧 Planned         | Store Byte                                  |
| `SH`             | Memory Access     | 🚧 Planned         | Store Halfword                              |
| `SW`             | Memory Access     | 🚧 Planned         | Store Word                                  |
| `ADDI`           | Arithmetic        | ✅ Implemented     | Add Immediate                               |
| `SLTI`           | Arithmetic        | 🚧 Planned         | Set Less Than Immediate                     |
| `SLTIU`          | Arithmetic        | 🚧 Planned         | Set Less Than Immediate Unsigned            |
| `XORI`           | Arithmetic        | 🚧 Planned         | XOR Immediate                               |
| `ORI`            | Arithmetic        | 🚧 Planned         | OR Immediate                                |
| `ANDI`           | Arithmetic        | 🚧 Planned         | AND Immediate                               |
| `SLLI`           | Bitwise           | 🚧 Planned         | Shift Left Logical Immediate                |
| `SRLI`           | Bitwise           | 🚧 Planned         | Shift Right Logical Immediate               |
| `SRAI`           | Bitwise           | 🚧 Planned         | Shift Right Arithmetic Immediate            |
| `ADD`            | Arithmetic        | ✅ Implemented     | Addition                                    |
| `SUB`            | Arithmetic        | 🚧 Planned         | Subtraction                                 |
| `SLL`            | Bitwise           | 🚧 Planned         | Shift Left Logical                          |
| `SLT`            | Arithmetic        | 🚧 Planned         | Set Less Than                               |
| `SLTU`           | Arithmetic        | 🚧 Planned         | Set Less Than Unsigned                      |
| `XOR`            | Bitwise           | 🚧 Planned         | XOR                                         |
| `OR`             | Bitwise           | 🚧 Planned         | OR                                          |
| `AND`            | Bitwise           | 🚧 Planned         | AND                                         |
| `MUL`            | Arithmetic        | ✅ Implemented     | Multiply                                    |
| `MULH`           | Arithmetic        | 🚧 Planned         | Multiply High (signed)                      |
| `MULHSU`         | Arithmetic        | 🚧 Planned         | Multiply High (signed)                      |
| `MULHU`          | Arithmetic        | 🚧 Planned         | Multiply High (unsigned)                    |
| `DIV`            | Arithmetic        | 🚧 In Progress     | Divide                                      |
| `DIVU`           | Arithmetic        | 🚧 Planned         | Divide (unsigned)                           |
| `REM`            | Arithmetic        | 🚧 Planned         | Remainder                                   |
| `REMU`           | Arithmetic        | 🚧 Planned         | Remainder (unsigned)                        |


### Legend
* ✅ **Implemented**: The instruction is fully implemented and tested.
* 🚧 **In Progress**: The instruction is under active development.
* 🚧 **Planned**: The instruction is planned for future development.
