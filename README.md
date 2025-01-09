<p align="center">
  <a href="https://fidesinnova.io/" target="blank"><img src="https://fidesinnova.io/Download/logo/g-c-web-back.png" /></a>
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

1. [Local Execution (To execute on Local Computer using RISC-V Emulator)](#local-execution-to-execute-on-local-computer-using-risc-v-emulator)  
   - [Prerequisites](#prerequisites)  
   - [Step 1: Writing a C++ Program](#step-1-writing-a-c-program)  
   - [Step 2: Compile and Generate an Assembly File](#step-2-compile-and-generate-an-assembly-file)  
   - [Step 3: Download and Edit `device_config.json`](#step-3-download-and-edit-device_configjson)  
   - [Step 4: Download the `setupN.json` File](#step-4-download-the-setupNjson-file)  
   - [Step 5: Download and Execute `commitmentGenerator`](#step-5-download-and-execute-commitmentgenerator)  
   - [Step 6: Compile and Execute](#step-6-compile-and-execute)  

2. [IoT Device Execution (To execute on ESP32 Microcontroller)](#iot-device-execution-to-execute-on-esp32-microcontroller)  
   - [Prerequisites](#prerequisites-1)  
   - [Step 1: Writing a C++ Program](#step-1-writing-a-c-program-1)  
   - [Step 2: Compile and Generate an Assembly File](#step-2-compile-and-generate-an-assembly-file-1)  
   - [Step 3: Download and Edit `device_config.json`](#step-3-download-and-edit-device_configjson-1)  
   - [Step 4: Download the `setupN.json` File](#step-4-download-the-setupNjson-file-1)  
   - [Step 5: Download and Execute `commitmentGenerator`](#step-5-download-and-execute-commitmentgenerator-1)  
   - [Step 6: Compile and Execute](#step-6-compile-and-execute-1)  




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

***2. RISC-V Emulator:*** For emulating program_new locally.
```
apt-get install qemu-user-static
```
#### macOS:
To install the RISC-V GNU Compiler and Toolchain follow the instructions from https://github.com/riscv-collab/riscv-gnu-toolchain

### Clone the Repository
Clone the repository to your server or local machine.
```
git clone https://github.com/FidesInnova/zkiot.git
cd zkiot  
```

### Use the `wizardry`

The `wizardry.sh` script automates the entire process of running the sample code for you. To use it, simply run:

```bash
./wizardry.sh
```

### Step 1. Writing a C++ program
Write a C++ program for the GCC Compiler and save it as `program.cpp` in the `zkiot` project folder.
**A sample program is provided in the repository for testing purposes.**

```
// Example program.cpp for GCC
#include "lib/fidesinnova.h"

int main() {
    int result;

    asm volatile (
        "li s1, 1\n"
        "li s2, 1\n"
        "mul s2, s2, s1\n"
        "addi s2, s2, 1\n"
        "mul s2, s2, s1\n"
        ...
        ...
    );
    return 0;
}
```
The above example demonstrates how to use inline assembly with RISC-V instructions in a C++ program. Modify it to test other instructions.
- Refer to the `RV32IM_ISA.md` file in the repository to view the list of currently implemented and available instructions for use.

### Step 2. Compile and Generate an assembly file
Compile the `program.cpp`
For RISC-V32:
 ```
 riscv64-unknown-elf-g++ -S program.cpp -o program.s -march=rv32gc -mabi=ilp32 -lstdc++
 ```
For RISC-V64:
 ```
 riscv64-unknown-elf-g++ -S program.cpp -o program.s -lstdc++
 ```

### Step 3. Edit the `device_config.json`
#### Use the `device_config.json` from this repository and edit the parameters as needed:
```
{
  "class": 32-bit Integer,
  "iot_developer_name": "String",
  "iot_device_name": "String",
  "device_hardware_version": "String",
  "firmware_version": "String",
  "code_block": "64-bit Array"
}
```
- **class**: A 32-bit integer indicating the number of gates in your ZKP. For more details, refer to the [Fidesinnova Documentation](https://fidesinnova-1.gitbook.io/fidesinnova-docs/zero-knowledge-proof-zkp-scheme/1-setup-phase).
- **code_block**: A two-part 64-bit array specifying the starting and ending line numbers of the section in `program.s` for which you want to create a ZKP.


### Step 4. The Necessary Files
Inside the `data` folder, you will find the `setupN.json` files, where `N` corresponds to your class number. 
The `class.json` file contains critical information, including:
- **number of gates (n_g)**: The total number of gates in your ZKP.
- **number of inputs (n_i)**: The number of inputs to the ZKP.
- **n**: Calculated as `n = n_i + n_g + 1`.
- **m**: Calculated as `m = 2 * n_g`.
- **field size (p)**: The size of the finite field used in the ZKP.
- **generator (g)**: The generator of the field.

Ensure you have the correct `setupN.json` file for your class to proceed with the ZKP setup.

### Step 5. Execute `commitmentGenerator` 
#### Open a terminal and navigate to the directory containing your `program.s`, `commitmentGenerator`, `class.json`, `device_config.json`, and `data/setupN.json`:
Ensure your directory structure looks like this:
```
zkiot/|
      ├── class.json
      ├── commitmentGenerator
      ├── device_config.json
      ├── data/ |
                ├── setup1.json │
                ├── setup2.json │
                ├── setup3.json │
                └── ... (other setupN.json files)
      ├── program.cpp
      ├── program.s
      └── ... (other project files)
```

Now, run the `commitmentGenerator`:
```
./commitmentGenerator
```
The `commitmentGenerator` will create the following files:
- `data/program_commitment.json`: The commitment file to be uploaded to the blockchain using the **developer panel**.
- `data/program_param.json`: An additional parameters file that accelerates the proof generation process.
- `program_new.s`: A newly generated assembly file with added macros.

The `program_new.s` will look something like this:
```
// program_new.s
  ....
  jal store_register_instances
  mul s2, s2, s1    #  User Code
  la t0, x18_array
  sw x18, 4(t0)
  addi s2, s2, 1    #  User Code
  la t0, x18_array
  sw x18, 8(t0)
  mul s2, s2, s1    #  User Code
  la t0, x18_array
  sw x18, 12(t0)
  ...
  call proofGenerator
  ...
```

### Step 6. Compile and Execute
#### A. Assemble and link the new code:
For RISC-V32:
```
riscv32-unknown-elf-g++ -march=rv32im -mabi=ilp32 program_new.s lib/polynomial.cpp -o program -lstdc++
```
For RISC-V64:
```
riscv64-unknown-elf-g++ program_new.s lib/polynomial.cpp -o program -lstdc++
```

#### B. Run the executable:
For RISC-V32:
```
qemu-riscv32-static program
```
For RISC-V64:
```
qemu-riscv64-static program
```
After running the code in QEMU, you will be prompted to enter the contents of `data/program_commitment.json`, `data/program_param.json`, `class.json`, and `data/setupN.json`. You need to input them one by one, ensuring that each file's contents are ended with a blank line.

Once the proof generation is complete, QEMU will print the proof as a JSON in the terminal. Copy this output into the `data/proof.json` file.

### Step 7. Verification
To verify the proof, simply run:
```
./verifier
```
Alternatively, you can upload your proof using the **developer panel** to the blockchain and use the Fidesinnova explorer to check the verification.

## IoT Device Execution (To execute on ESP32 Microcontroller)
### Prerequisites
***Arduino Toolchain:*** For compiling C++ code.
Follow the instructions from https://docs.espressif.com/projects/arduino-esp32/en/latest/installing.html to install the Arduino ESP32 toolchain.


### Step 1. Writing a C++ program
Write a C++ Program for GCC Compiler and Save it as program.cpp 
```
// Example program.cpp for GCC
#include "fidesinnova.h"

void setup() {
    int result;

    asm volatile (
        "li s2, 4\n"
        "li s3, 5\n"
        "li s4, 26\n"
        "mul s2, s2, s3\n"
        "addi s2, s2, 11\n"
        "mul s2, s2, s4\n"
        "mul s2, s2, s4\n"
    );
    proofGenerator();
    return 0;
}

void loop() {
}
```

### Step 2. Compile and Generate an assembly file
Compile the `program.ino`
***For ESP32-C3, compile `program.ino` using Arduino GUI to generate 'program.ino.elf'.***
***Then, Run the following command to generate the 'program.s' assembly file.***
 ```
 riscv32-esp-elf-objdump.exe -d program.ino.elf > program.s
 ```
### Step 3. Download and Edit `device_config.json` 
#### A. Download device_config.json from this repository and edit the parameters.
```
{
  "class": 32-bit Integer,
  "iot_manufacturer_name": String,
  "iot_device_name": String,
  "device_hardware_version": String,
  "firmware_version": String,
  "code_block": 64-bit Array
}
```
#### B. Save device_config.json on your computer

### Step 4. Download the `setupN.json` file
Download the `setupN.json` file from this repository and save it in the same directory as `device_config.json`. Ensure that in `setupN.json`, the `X` matches your class number.

### Step 5. Download and Execute `commitmentGenerator` 
#### A. Download the `commitmentGenerator` tool from this repository and save it in the same folder with device_config.json.
#### B. Open a terminal and navigate to the directory containing your `program.s`, `commitmentGenerator`, `class.json`, `device_config.json`, and `setupN.json`:
```
./commitmentGenerator
```
This command will prompt you to enter the path and filenames for `program.s`, `commitmentGenerator`, `class.json`, `device_config.json`, `setupN.json`, and `program_new.s` (the output file for the assembly code).
- `program_commitment.json` - The commitment file to be uploaded to the blockchain.
- `program_param.json` - Additional parameters file that accelerates proof generation program.
- `program_new.s` - New generated assembly file with added macros.
```
// program_new.s
  ....
  jal store_register_instances
  mul s2, s2, s3    // user code
  la t0, x18_array
  sw x18, 4(t0)
  addi s2, s2, 11    // user code
  la t0, x18_array
  sw x18, 8(t0)
  mul s2, s2, s4    // user code
  la t0, x18_array
  sw x18, 12(t0)
  mul s2, s2, s4    // user code
  la t0, x18_array
  sw x18, 16(t0)
  la a0, z_array
  li t0, 1
  sw t0, 0(a0)
  la a1, x0_array
  lw t0, 0(0)
  sw t0, 4(a0)
  la a1, x1_array
  lw t0, 0(1)
  sw t0, 8(a0)
  ...
  ...
```

### Step 6. Compile and Execute
#### A. Assemble and link the new code:
```
riscv32-esp-elf-as -o program_new.o program_new.s
riscv32-esp-elf-ld -o program.elf program_new.o -T esp32_out.ld
```
#### B. Convert the program to a binary format suitable for your IoT device:
```
riscv32-esp-elf-objcopy -O binary program_new.elf program_new.bin
```
#### C. Upload `program_new.bin` to your IoT device and execute it.





<!---

### C. Install Arduino Toolchain 
***Follow the instruction from https://docs.espressif.com/projects/arduino-esp32/en/latest/installing.html to install the Arduino ESP32 toolchain.***

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

3. Final Tasks:<br>
   <b>Execute the Program:</b> Run the <b>program_new</b> file on your chosen platform (either locally or on an IoT device).<br>
   <b>Upload Commitment to Blockchain:</b> Use the web app panel to upload <b>program_commitment.json</b> to the blockchain, ensuring the integrity of your program.<br>
   <b>Place the Parameter File:</b> Ensure that <b>program_param.json</b> is placed in the same directory as program_new to maintain consistency between the program and its parameters.

### Step 7. IoT Device Execution
4. Final Tasks:<br>
   <b>Execute the Program:</b> Run the <b>program_new</b> file on your chosen platform (either locally or on an IoT device).<br>
   <b>Upload Commitment to Blockchain:</b> Use the web app panel to upload <b>program_commitment.json</b> to the blockchain, ensuring the integrity of your program.<br>
   <b>Place the Parameter File:</b> Ensure that <b>program_param.json</b> is placed in the same directory as program_new to maintain consistency between the program and its parameters.

## Troubleshooting

- **GCC Not Found**: Ensure GCC is installed and added to your system path.
- **Cross-Compilation Errors**: Verify that the RISC-V toolchain is correctly installed and configured.
- **Commitment Upload Issues**: Ensure the `commitmentGenerator` and blockchain client are correctly set up.



--->
<br>
<br>

# Converting Python Code to C and Compiling for RISC-V Architecture
This guide explains how to convert Python code to C and then compile it for the RISC-V architecture.

## 1. Generating C Code with Cython
### 1.1 Create a .pyx file
First, rename your Python file to .pyx:

```
mv test.py test.pyx
```
### 1.2 Create a setup.py file
Create a new file named setup.py and add the following code:
```
from distutils.core import setup
from Cython.Build import cythonize

setup(
    ext_modules=cythonize("test.pyx")
)
```
### 1.3 Compile with Cython
Run the following command to generate the C code:
(pip3 install cython)
sudo apt-get install libc6-dev-riscv64-cross
sudo apt-get install libpython3-dev-riscv64-cross
riscv64-unknown-elf-gcc --print-sysroot

```
python3 setup.py build_ext --inplace
```
After running this command, a C file (e.g., test.c) will be generated.

## 2. Setting Up for RISC-V Architecture
### 2.1 Dependencies
To run the generated C code on the RISC-V architecture, you need specific libraries:

libpython: To interface the C code with the Python environment.
RISC-V compiler such as riscv64-unknown-elf-g++ or riscv32-unknown-elf-gcc.
### 2.2 Compile the C Code
Use the RISC-V compiler to generate assembly code. If your C file is test.c, run:

```
riscv64-linux-gnu-gcc -march=rv32gc -mabi=ilp32 -I/usr/include/python3.8  -I/usr/include  -I/usr/riscv64-linux-gnu/include/  -o demo3asembly.s -S demo3.
```
```
riscv64-unknown-elf-gcc -S demo3.c -o demo3asembly.s -march=rv32gc -mabi=ilp32 -I/usr/include -I/usr/include/python3.8
```
```
riscv64-unknown-elf-gcc -S test.c -o test.s -march=rv32gc -mabi=ilp32
```
-S: Generates assembly code.<br>
-o test.s: Outputs the result to test.s.<br>
-march=rv32gc: Specifies the 32-bit RISC-V architecture with support for standard and compressed instructions.<br>
-mabi=ilp32: Uses the 32-bit ABI model.<br>

## 3. Generating an Executable (Optional)
If you want to generate an executable, use the following command:

```
riscv64-unknown-elf-gcc test.c -o test.elf -march=rv32gc -mabi=ilp32 -lpython3.10
```
-lpython3.10: Links the Python library (change the version based on your installation).

## 4. Simulating or Running on RISC-V
If you don't have access to a real RISC-V processor, you can use a simulator.

### 4.1 Install QEMU
First, install QEMU:
```
sudo apt install qemu-system-misc
```
### 4.2 Run the File in QEMU
To run the test.elf file in QEMU, use:
```
qemu-riscv32 test.elf
```
### Important Notes
- The C code generated by Cython includes Python API functions. Therefore, you'll need the appropriate Python libraries for the RISC-V architecture.<br>
- RISC-V is commonly used in embedded systems, so this process is typically done in such environments.<br>
- If you have questions about dependencies or encounter errors, please provide additional details about your environment.<br>





