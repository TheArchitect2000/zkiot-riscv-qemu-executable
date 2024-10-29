<p align="center">
  <a href="http://fidesonnova.io/" target="blank"><img src="https://fidesinnova.io/Download/logo/gihub-cover-fides.png" /></a>
</p>



# zk-IoT: C++ Program Execution and Blockchain Commitment for IoT devices and Local Systems

<a href="https://www.npmjs.com/~nestjscore" target="_blank"><img src="https://img.shields.io/npm/v/@nestjs/core.svg" alt="NPM Version" /></a>
<a href="https://www.npmjs.com/~nestjscore" target="_blank"><img src="https://img.shields.io/npm/l/@nestjs/core.svg" alt="Package License" /></a>
<a href="https://www.npmjs.com/~nestjscore" target="_blank"><img src="https://img.shields.io/npm/dm/@nestjs/common.svg" alt="NPM Downloads" /></a>
<a href="https://circleci.com/gh/nestjs/nest" target="_blank"><img src="https://img.shields.io/circleci/build/github/nestjs/nest/master" alt="CircleCI" /></a>
<a href="https://coveralls.io/github/nestjs/nest?branch=master" target="_blank"><img src="https://coveralls.io/repos/github/nestjs/nest/badge.svg?branch=master#9" alt="Coverage" /></a>
<a href="https://discord.com/invite/NQdM6JGwcs" target="_blank"><img src="https://img.shields.io/badge/discord-online-brightgreen.svg" alt="Discord"/></a>
<a href="https://twitter.com/FidesInnova" target="_blank"><img src="https://img.shields.io/twitter/follow/nestframework.svg?style=social&label=Follow"></a>


This guide will help you execute your C++ program on your computer or IoT device and commit the results to the blockchain.

## Overview
You can execute your program in two ways:<br>
<b>1- Locally on your computer</b><br>
<b>2- On an IoT device (e.g., ESP32)</b><br>

This project provides a framework for compiling, executing, and committing C++ user programs. The framework supports both local (computer) and IoT device execution. After executing the program, the final commitment is uploaded to the blockchain to ensure traceability and security.

## Table of Contents
- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Usage](#usage)
  - [Local Execution](#local-execution)
    - [Step 1: Generate the Assembly File](#step-1-generate-the-assembly-file)
    - [Step 2: Generate Commitment and New Code](#step-2-generate-commitment-and-new-code)
    - [Step 3: Compile and Execute](#step-3-compile-and-execute)
  - [IoT Device Execution](#iot-device-execution)
    - [Step 1: Generate the Assembly File](#step-1-generate-the-assembly-file-1)
    - [Step 2: Generate Commitment and New Code](#step-2-generate-commitment-and-new-code-1)
    - [Step 3: Cross-Compile and Upload](#step-3-cross-compile-and-upload)
- [Troubleshooting](#troubleshooting)


## Prerequisites
1. **To execute on Local Computer RISC-V Emulator**
   A. ***GCC Compiler***: For compiling C++ code.
   B. ***RISC-V Toolchain***: Required for cross-compilation if running on IoT devices.
2. **To execute on ESP32 Microcontroller**
   C. ***Arduino Toolchain***: For compiling C++ code.

### A. Install GCC Compiler
1. **Ubuntu**:
   ```bash
   sudo apt update
   sudo apt install gcc g++ -y
   ```
2. **macOS**:
   - Install Xcode Command Line Tools, which include GCC:
     ```bash
     xcode-select --install
     ```
### B. Install RISC-V Toolchain 
1. **Ubuntu**:
   ```bash
   sudo apt install gcc-riscv64-unknown-elf g++-riscv64-unknown-elf
   ```
2. **macOS**:
   - Install Xcode Command Line Tools, which include GCC:
     ```bash
     brew tap riscv-software-src/homebrew-riscv
     brew install riscv-software-src/riscv/riscv-tools
     ```
### C. Install Arduino Toolchain 
***Follow the instruction from https://docs.espressif.com/projects/arduino-esp32/en/latest/installing.html to install the Arduino ESP32 toolchain.***



## Writing a C++ program

### Write a C++ Program for GCC Compiler and Save it as program.cpp 
   ```
    // Example program.cpp for gcc
    int main() {
      for(int i = 297; i < 10000;){
        i += 383;
      }
    }
   ```
### Write a C++ Program for Arduino Compiler and Save it as program.ino 
   ```
     // Example program.ino for Arduino
     // Microcontroller ESP32-C3/C6
     void setup() {
       for(int i = 297; i < 10000;){
         i += 383;
       }
     }
     void loop() {
     }
   ```

    
## Compile and Generate an assembly file
### A. Compile `program.cpp` 
   ```bash
   riscv64-unknown-elf-g++ -S program.cpp -o program.s  -march=rv32gc -mabi=ilp32
   ```
### A. Compile `program.ino` 
   ***Compile `program.ino` using Arduino GUI to generate 'program.elf'.***
   ***Then, Run the following command to generate program.s from the elf file.***
   ```bash
    riscv32-esp-elf-objdump.exe -d program.elf > program.s
   ```

## Download and Edit `device_config.json` 
   ***Download device_config.json from this repository and edit the parameters.***
   ```bash
   {
      "Class": 32-bit Integer,
      "IoT_Manufacturer_Name": String,
      "IoT_Device_Name": String,
      "Device_Hardware_Version": float,
      "Firmware_Version": float,
      "Lines": 64-bit Array
    }
   ```bash
   ***Save device_config.json on your computer.***

## Download and Execute the Commitment Generator Program 
    ***Commitment Generator is a custom tool used to create commitments for an arbitrary assembly program.***
    ***Download the `commitmentGenerator` tool from this repository and save it in the same folder with device_config.json.***


### Step 2: Adding ZKP codes
#### Local Execution
For running the program on a computer, follow these steps.



#### Step 3: Generate Commitment and New Code

1. Run the Commitment Generator: Open your terminal or command prompt and navigate to the directory containing your program.s, commitment_generator.exe, and device_config.json:
   ```bash
   commitmentGenerator program.s deviceConfig.json
   ```
   - This command outputs:
     - `program_new.s` - New generated assembly file.
     - `program_commitment.json` - The commitment file for blockchain upload.
     - `program_param.json` - Additional parameters file if required.

#### Step 4: Compile and Execute

1. Assemble and link the new code:
   ```bash
   riscv64-unknown-elf-as program_new.s -o program_new.o
   riscv64-unknown-elf-ld program_new.o -o program_new
   ```

2. Run the executable:
   ```bash
   file program_new qemu-riscv64 ./program_new
   ```
3. Final Tasks:<br>
   <b>Execute the Program:</b> Run the <b>program_new</b> file on your chosen platform (either locally or on an IoT device).<br>
   <b>Upload Commitment to Blockchain:</b> Use the web app panel to upload <b>program_commitment.json</b> to the blockchain, ensuring the integrity of your program.<br>
   <b>Place the Parameter File:</b> Ensure that <b>program_param.json</b> is placed in the same directory as program_new to maintain consistency between the program and its parameters.

### IoT Device Execution

For running the program on an IoT device, follow these steps.

#### Step 1: Generate the Assembly File

1. Write your C++ program in `program.cpp` as shown above.

2. Cross-compile `program.cpp` for RISC-V (or your target architecture) to generate an assembly file:
   ```bash
   riscv32-esp-elf-objdump.exe -d program.elf > program.s
   ```

#### Step 2: Generate Commitment and New Code

1. Run the Commitment Generator: Open your terminal or command prompt and navigate to the directory containing your program.s, commitment_generator.exe, and device_config.json:
   ```bash
   commitmentGenerator program.s deviceConfig.json
   ```
   - This command outputs:
     - `program_new.s` - New generated assembly file.
     - `program_commitment.json` - The commitment file for blockchain upload.
     - `program_param.json` - Additional parameters file if required.

#### Step 3: Cross-Compile and Upload

1. Assemble and link the new code:
   ```bash
   riscv32-esp-elf-as -o program_new.o program_new.s
   riscv32-esp-elf-ld -o program.elf program_new.o -T esp32_out.ld
      ```

2. Convert the program to a binary format suitable for your IoT device:
   ```bash
   riscv32-esp-elf-objcopy -O binary program_new.elf program_new.bin
   ```

3. Upload `program_new.bin` to your IoT device and execute it.

4. Final Tasks:<br>
   <b>Execute the Program:</b> Run the <b>program_new</b> file on your chosen platform (either locally or on an IoT device).<br>
   <b>Upload Commitment to Blockchain:</b> Use the web app panel to upload <b>program_commitment.json</b> to the blockchain, ensuring the integrity of your program.<br>
   <b>Place the Parameter File:</b> Ensure that <b>program_param.json</b> is placed in the same directory as program_new to maintain consistency between the program and its parameters.

## Troubleshooting

- **GCC Not Found**: Ensure GCC is installed and added to your system path.
- **Cross-Compilation Errors**: Verify that the RISC-V toolchain is correctly installed and configured.
- **Commitment Upload Issues**: Ensure the `commitmentGenerator` and blockchain client are correctly set up.

