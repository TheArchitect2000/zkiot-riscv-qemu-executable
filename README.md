<p align="center">
  <a href="http://fidesonnova.io/" target="blank"><img src="https://fidesinnova.io/Download/logo/g-c-rust.png" /></a>
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
- [Local Execution](#local-execution)
  - [Step 1: Generate the Assembly File](#step-1-generate-the-assembly-file)
  - [Step 2: Generate Commitment and New Code](#step-2-generate-commitment-and-new-code)
  - [Step 3: Compile and Execute](#step-3-compile-and-execute)
- [IoT Device Execution](#iot-device-execution)
  - [Step 1: Generate the Assembly File](#step-1-generate-the-assembly-file-1)
  - [Step 2: Generate Commitment and New Code](#step-2-generate-commitment-and-new-code-1)
  - [Step 3: Cross-Compile and Upload](#step-3-cross-compile-and-upload)
- [Troubleshooting](#troubleshooting)

## Local Execution (To execute on Local Computer using RISC-V Emulator)
### Prerequisites
***1. GCC Compiler:*** For compiling C++ code.

#### Ubuntu:
To install the RISC-V GNU Compiler and Toolchain follow the instructions from https://github.com/riscv-collab/riscv-gnu-toolchain or use the instructions below
```
sudo apt update
git clone https://github.com/riscv/riscv-gnu-toolchain
```
This repository uses submodules, but submodules will fetch automatically on demand, so `--recursive` or `git submodule update --init --recursive` is not needed.

**Warning: git clone takes around 6.65 GB of disk and download size**

Several standard packages are needed to build the toolchain.

On Ubuntu, executing the following command should suffice:
```
sudo apt-get install autoconf automake autotools-dev curl python3 python3-pip libmpc-dev libmpfr-dev libgmp-dev gawk build-essential bison flex texinfo gperf libtool patchutils bc zlib1g-dev libexpat-dev ninja-build git cmake libglib2.0-dev libslirp-dev
```
We then got to the root directory of riscv-gnu-toolchain and configured it to build the Newlib cross-compiler.

```
cd riscv-gnu-toolchain
./configure --prefix=/opt/riscv
make
```
**It may take a long time to install the riscv-gnu-toolchain**

Then add the path to the system

```
echo 'export PATH=/opt/riscv/bin:$PATH' >> ~/.bashrc
source ~/.bashrc
```

#### macOS:
To install the RISC-V GNU Compiler and Toolchain follow the instructions from https://github.com/riscv-collab/riscv-gnu-toolchain

***2. RISC-V Emulator:*** For emulating program_new locally.
```
apt-get install qemu-user-static
```

## IoT Device Execution (To execute on ESP32 Microcontroller)
### Prerequisites
***1. Arduino Toolchain:*** For compiling C++ code.


#### A. Install GCC Compiler



### B. Install RISC-V Toolchain 
1. **Ubuntu**:
```
sudo apt install gcc-riscv64-unknown-elf g++-riscv64-unknown-elf
```
2. **macOS**:
Install Xcode Command Line Tools, which include GCC:
```
brew tap riscv-software-src/homebrew-riscv
brew install riscv-software-src/riscv/riscv-tools
```
### C. Install Arduino Toolchain 
***Follow the instruction from https://docs.espressif.com/projects/arduino-esp32/en/latest/installing.html to install the Arduino ESP32 toolchain.***


## Step 2. Writing a C++ program

### A. Write a C++ Program for GCC Compiler and Save it as program.cpp 
```
// Example program.cpp for gcc
int main() {
  for(int i = 297; i < 10000;){
    i += 383;
  }
}
```
### B. Write a C++ Program for Arduino Compiler and Save it as program.ino 
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

    
## Step 3. Compile and Generate an assembly file
### A. Compile `program.cpp` 
   ```
   riscv64-unknown-elf-g++ -S program.cpp -o program.s  -march=rv32gc -mabi=ilp32
   ```
### B. Compile `program.ino` 
   ***Compile `program.ino` using Arduino GUI to generate 'program.ino.elf'.***
   ***Then, Run the following command to generate the 'program.s' assembly file.***
   ```
    riscv32-esp-elf-objdump.exe -d program.ino.elf > program.s
   ```

## Step 4. Download and Edit `device_config.json` 
### A. Download device_config.json from this repository and edit the parameters.
```
{
  "Class": 32-bit Integer,
  "IoT_Manufacturer_Name": String,
  "IoT_Device_Name": String,
  "Device_Hardware_Version": float,
  "Firmware_Version": float,
  "Lines": 64-bit Array
}
```
### B. Save device_config.json on your computer

## Step 5. Download the `setup.json` file
### A. Download the 'setup.json' from this repository and save it in the same folder with device_config.json

## Step 6. Download and Execute `commitmentGenerator` 
### A. Download the `commitmentGenerator` tool from this repository and save it in the same folder with device_config.json.
### B. Open a terminal and navigate to the directory containing your `program.s`, `commitmentGenerator`, `device_config.json`, and `setup.json`:
```
commitmentGenerator setup.json deviceConfig.json program.s
```
- This command outputs:
- `program_new.s` - New generated assembly file with added macros.
```
{
  mv 0x10, x0
  mv 0x11, x1
  mv 0x12, x2
  mv 0x13, x3
  // TODO: Use the snapshot assembly code instead of "mv 0x10, x0"... .
  // lib.snapshot

  ....
  // One instruction like ADDI, MUL, etc..

  mv 0x10, x0
  mv 0x11, x1
  mv 0x12, x2
  mv 0x13, x3
  // TODO: Use the snapshot assembly code instead of "mv 0x10, x0"... .
  // lib.snapshot

  // TODO: Adding the proofGenerator assembly function here.
  // lib.proofGenerator
}
```
- `program_commitment.json` - The commitment file to be uploaded on blockchain.
- `program_param.json` - Additional parameters file that accelerate proof generation program.

### C. Compile and Execute

1. Assemble and link the new code:
   ```
   riscv64-unknown-elf-as program_new.s -o program_new.o
   riscv64-unknown-elf-ld program_new.o -o program_new
   ```
2. Run the executable:
   ```
   file program_new qemu-riscv64 ./program_new
   ```
3. Final Tasks:<br>
   <b>Execute the Program:</b> Run the <b>program_new</b> file on your chosen platform (either locally or on an IoT device).<br>
   <b>Upload Commitment to Blockchain:</b> Use the web app panel to upload <b>program_commitment.json</b> to the blockchain, ensuring the integrity of your program.<br>
   <b>Place the Parameter File:</b> Ensure that <b>program_param.json</b> is placed in the same directory as program_new to maintain consistency between the program and its parameters.

## Step 7. IoT Device Execution

For running the program on an IoT device, follow these steps.

### A. Generate the Assembly File

1. Write your C++ program in `program.cpp` as shown above.

2. Cross-compile `program.cpp` for RISC-V (or your target architecture) to generate an assembly file:
   ```
   riscv32-esp-elf-objdump.exe -d program.elf > program.s
   ```

#### B. Generate Commitment and New Code

1. Run the Commitment Generator: Open your terminal or command prompt and navigate to the directory containing your program.s, commitment_generator.exe, and device_config.json:
   ```
   commitmentGenerator program.s deviceConfig.json
   ```
   - This command outputs:
     - `program_new.s` - New generated assembly file.
     - `program_commitment.json` - The commitment file for blockchain upload.
     - `program_param.json` - Additional parameters file if required.

#### C. Cross-Compile and Upload

1. Assemble and link the new code:
   ```
   riscv32-esp-elf-as -o program_new.o program_new.s
   riscv32-esp-elf-ld -o program.elf program_new.o -T esp32_out.ld
      ```

2. Convert the program to a binary format suitable for your IoT device:
   ```
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

