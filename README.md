<p align="center">
  <a href="https://fidesinnova.io/" target="blank"><img src="https://fidesinnova.io/Download/logo/g-c-web-back.png" /></a>
</p>

# zk-IoT: C++ Program Execution with Verifiable Computing Feature on IoT devices and Computers

<a href="https://www.npmjs.com/~nestjscore" target="_blank"><img src="https://img.shields.io/npm/v/@nestjs/core.svg" alt="NPM Version" /></a>
<a href="https://www.npmjs.com/~nestjscore" target="_blank"><img src="https://img.shields.io/npm/l/@nestjs/core.svg" alt="Package License" /></a>
<a href="https://www.npmjs.com/~nestjscore" target="_blank"><img src="https://img.shields.io/npm/dm/@nestjs/common.svg" alt="NPM Downloads" /></a>
<a href="https://circleci.com/gh/nestjs/nest" target="_blank"><img src="https://img.shields.io/circleci/build/github/nestjs/nest/master" alt="CircleCI" /></a>
<a href="https://coveralls.io/github/nestjs/nest?branch=master" target="_blank"><img src="https://coveralls.io/repos/github/nestjs/nest/badge.svg?branch=master#9" alt="Coverage" /></a>
<a href="https://discord.com/invite/NQdM6JGwcs" target="_blank"><img src="https://img.shields.io/badge/discord-online-brightgreen.svg" alt="Discord"/></a>
<a href="https://twitter.com/FidesInnova" target="_blank"><img src="https://img.shields.io/twitter/follow/nestframework.svg?style=social&label=Follow"></a>

This guide will help you to learn how to:
- Step A) create a commitment for your C++ program and submit it on blockchain, 
- Step B) execute your program and create a proof for your execution on your comuter or IoT device and submit it on blockchain, 
- Step C) verify the proof on blockchain via blockchain explorer or through a verifier program.

  These steps are explained for three different scenarios to:
  1- Execute a program on a computer
  2- Execute a firmware on an IoT device
  3- Execute a machine learning model 

ReadMe 1. [Computer Execution (To execute on a Computer using RISC-V Emulator)](#local-execution-to-execute-on-local-computer-using-risc-v-emulator)  
   - [Prerequisites](#prerequisites)
   - [Clone the Repository](#clone-the-repository)
   - [Use the `wizardry`](use-the-wizardry)
   - [Step 1: Writing a C++ program](#step-1-writing-a-c-program)  
   - [Step 2: Compile and Generate an assembly file](#step-2-compile-and-generate-an-assembly-file)  
   - [Step 3: Edit `device_config.json`](#step-3-edit-device_configjson)  
   - [Step 4: The Necessary Files](#step-4-the-necessary-files)  
   - [Step 5: Execute `commitmentGenerator`](#step-5-execute-commitmentgenerator)  
   - [Step 6: Compile and Execute](#step-6-compile-and-execute)
   - [Step 7: Verification](#step-7-verification)

# Step A. Commitment Generation
## A.1. Install GCC compiler and emualtor to compile and execute C++ programms
### A.1.1. Ubuntu:
- Install the RISC-V GNU Compiler and Toolchain follow the instructions from https://github.com/riscv-collab/riscv-gnu-toolchain or use the instructions below
```
sudo apt update
git clone https://github.com/riscv/riscv-gnu-toolchain
```
- This repository uses submodules, but submodules will fetch automatically on demand, so `--recursive` or `git submodule update --init --recursive` is not needed. Several standard packages are needed to build the toolchain which requires to run below commands:
```
sudo apt-get install autoconf automake autotools-dev curl python3 python3-pip libmpc-dev libmpfr-dev libgmp-dev gawk build-essential bison flex texinfo gperf libtool patchutils bc zlib1g-dev libexpat-dev ninja-build git cmake libglib2.0-dev libslirp-dev
```
- Go to the riscv-gnu-toolchain directory run the below commands to configure and build the Newlib cross-compiler.
```
cd riscv-gnu-toolchain
./configure --prefix=/opt/riscv
make
```
- Add the following path to the PATH system variable.
```
echo 'export PATH=/opt/riscv/bin:$PATH' >> ~/.bashrc
source ~/.bashrc
```
- Install the RISC-V QEMU emulator.
```
apt-get install qemu-user-static
```
### A.1.2. macOS:
- Install Homebrew
```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```
- Add the following path to the system.
```bash
echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> ~/.zshrc
source ~/.zshrc
```
- Verify the Homebrew installation
```bash
brew --version
```
- Install RISC-V Toolchain
```bash
brew tap riscv-software-src/riscv
```
- Build the toolchain
```bash
brew install riscv-tools
```
- Verify the Installation
```bash
brew test riscv-tools
```
- Install Spike RISC-V Simulator to run RISC-V binaries
```
git clone https://github.com/riscv/riscv-isa-sim.git
cd riscv-isa-sim
mkdir build
cd build
../configure --prefix=/usr/local
make
sudo make install
```

## A.2. Clone the zkIoT repository
```
git clone https://github.com/FidesInnova/zkiot.git
cd zkiot  
```

## A.3. Writing a C++ program
Write a C++ program for the GCC Compiler and save it as `program.cpp` in the `zkiot` project folder.
**A sample program, program.cpp, is provided in the zkiot repository for testing purposes.**

## A.4. Compile the C++ program
 ```
 riscv64-unknown-elf-g++ -S program.cpp -o program.s -lstdc++
 ```

## A.5. Edit `device_config.json` in the zkiot folder
```
{
  "class": 5, // 32-bit Integer
  "iot_developer_name": "My Company", // "String"
  "iot_device_name": "Smart Device", // "String"
  "device_hardware_version": "1.0", // "String"
  "firmware_version": "1.0", // "String"
  "code_block": [26087, 26118] // "64-bit Array"
}
```
- To learn about these parameters, please refer to the [Fidesinnova Documentation](https://fidesinnova-1.gitbook.io/fidesinnova-docs/zero-knowledge-proof-zkp-scheme/1-setup-phase).

<!-- 
### Step 4. The Necessary Files
Inside the `data` folder, you will find the `setupN.json` files, where `N` corresponds to your class number. 
The `class.json` file contains critical information, including:
- **number of gates (n_g)**: The total number of gates in your ZKP.
- **number of inputs (n_i)**: The number of inputs to the ZKP.
- **n**: Calculated as `n = n_i + n_g + 1`.
- **m**: Calculated as `m = 2 * n_g`.
- **field size (p)**: The size of the finite field used in the ZKP.
- **generator (g)**: The generator of the field.

Ensure you have the correct `setupN.json` file for your class to proceed with the ZKP setup. -->

## A.6. Check the Commitment folder structure
Ensure your directory structure looks like below and contains `program.s`, `commitmentGenerator`, `class.json`, `device_config.json`, and `data/setupN.json`:
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
## A.7. Execute `commitmentGenerator` 
### A.7.1 Ubuntu
```
./commitmentGenerator
```
### A.7.2 macOS
```
./commitmentGeneratorForMac
```
The `commitmentGenerator` will create the following files:
- `data/program_commitment.json`: The commitment file
- `data/program_param.json`: An additional parameters file that accelerates the proof generation process.
- `program_new.s`: A newly generated assembly file with added macros.

## A.8. Submit the commitment on blockchain 
Using Fidesinnova's node web app (e.g., panel.zksensor.tech) submit the generated commitment. 
### A.8.1. Goto the **IoT Developer Console**.
### A.8.2 Click on **Submit Commitment**.

# Step B. Proof Generation
## B.1. Compile the `program_new.s`
```
riscv64-unknown-elf-g++ program_new.s lib/polynomial.cpp -o program -lstdc++
```

## B.2. Run the executable
### B.2.1. Ubuntu
```
qemu-riscv64-static program
```
- After running the code in QEMU, you will be prompted to enter the contents of `data/program_commitment.json`, `data/program_param.json`, `class.json`, and `data/setupN.json`. After copying the files contents in your terminal, press 'Enter'.
- QEMU will print the proof as a JSON in the terminal. Copy this output into the `data/proof.json` file.
###  B.2.2. macOS
```
spike pk program
```
## B.3. Submit the generated proof on blockchain 
Using Fidesinnova's node web app (e.g., panel.zksensor.tech) submit the generated ZK proof file. 
### B.3.1. Goto the **IoT Developer Console**.
### B.3.2. Click on **Submit Proof**.

# Step C. Proof Verification
## C.1. Verifier program
Simply run the verifier program.
### C.1.1. Ubuntu
```
./verifier
```
### C.1.2. macOS
```
./verifierForMac
```
### C.2. Web ZKP Explorer
Goto https://explorer.fidesinnova.io, search the submitted proof, from the menu choose "Verify Proof". 


ReadMe 2. [IoT Device Execution (To execute on ESP32 Microcontroller)](#iot-device-execution-to-execute-on-esp32-microcontroller)  
   - [Prerequisites](#prerequisites-1)  
   - [Step 1: Writing a C++ Program](#step-1-writing-a-c-program-1)  
   - [Step 2: Compile and Generate an Assembly File](#step-2-compile-and-generate-an-assembly-file-1)  
   - [Step 3: Download and Edit `device_config.json`](#step-3-download-and-edit-device_configjson-1)  
   - [Step 4: Download the `setupN.json` File](#step-4-download-the-setupNjson-file-1)  
   - [Step 5: Download and Execute `commitmentGenerator`](#step-5-download-and-execute-commitmentgenerator-1)  
   - [Step 6: Compile and Execute](#step-6-compile-and-execute-1)
     
# Step A. IoT Device Execution (To execute on ESP32-C3 microcontroller)
## Install Arduino Toolchain to compile C++ programs
Follow the instructions from https://docs.espressif.com/projects/arduino-esp32/en/latest/installing.html to install the Arduino ESP32 toolchain.

## A.1. Clone the zkIoT repository
```
git clone https://github.com/FidesInnova/zkiot.git
cd zkiot  
```

## A.2. Writing an Arduino program
Write an Arduino program and save it as program.ino 
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

## A.3. Compile and generate an assembly file
- For ESP32-C3, compile `program.ino` using Arduino GUI to generate 'program.ino.elf'.
- Run the following command to generate the 'program.s' assembly file.
 ```
 riscv32-esp-elf-objdump.exe -d program.ino.elf > program.s
 ```

## A.5. Edit `device_config.json` in the zkiot folder
```
{
  "class": 5, // 32-bit Integer
  "iot_developer_name": "My Company", // "String"
  "iot_device_name": "Smart Device", // "String"
  "device_hardware_version": "1.0", // "String"
  "firmware_version": "1.0", // "String"
  "code_block": [26087, 26118] // "64-bit Array"
}
```
- To learn about these parameters, please refer to the [Fidesinnova Documentation](https://fidesinnova-1.gitbook.io/fidesinnova-docs/zero-knowledge-proof-zkp-scheme/1-setup-phase).

<!-- 
### Step 4. The Necessary Files
Inside the `data` folder, you will find the `setupN.json` files, where `N` corresponds to your class number. 
The `class.json` file contains critical information, including:
- **number of gates (n_g)**: The total number of gates in your ZKP.
- **number of inputs (n_i)**: The number of inputs to the ZKP.
- **n**: Calculated as `n = n_i + n_g + 1`.
- **m**: Calculated as `m = 2 * n_g`.
- **field size (p)**: The size of the finite field used in the ZKP.
- **generator (g)**: The generator of the field.

Ensure you have the correct `setupN.json` file for your class to proceed with the ZKP setup. -->

## A.6. Check the Commitment folder structure
Ensure your directory structure looks like below and contains `program.s`, `commitmentGenerator`, `class.json`, `device_config.json`, and `data/setupN.json`:
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
## A.7. Execute `commitmentGenerator` 
### A.7.1 Ubuntu
```
./commitmentGenerator
```
### A.7.2 macOS
```
./commitmentGeneratorForMac
```
The `commitmentGenerator` will create the following files:
- `data/program_commitment.json`: The commitment file
- `data/program_param.json`: An additional parameters file that accelerates the proof generation process.
- `program_new.s`: A newly generated assembly file with added macros.

## A.8. Submit the commitment on blockchain 
Using Fidesinnova's node web app (e.g., panel.zksensor.tech) submit the generated commitment. 
### A.8.1. Goto the **IoT Developer Console**.
### A.8.2 Click on **Submit Commitment**.

# Step B. Proof Generation
## B.1. Assemble and link the program_new, generating the ELF file
```
riscv32-esp-elf-as -o program_new.o program_new.s
riscv32-esp-elf-ld -o program_new.elf program_new.o -T esp32_out.ld
```
## B.2. Convert the program to a binary format suitable for your IoT device
```
riscv32-esp-elf-objcopy -O binary program_new.elf program_new.bin
```
## B.3. Upload `program_new.bin` on your IoT device and execute it.
Make sure your IoT device sends the generated proof to the IoT server node in order to be stored in a public database or blockchain.

# Step C. Proof Verification
## C.1. Web ZKP Explorer
```
Goto https://explorer.fidesinnova.io, search the submitted proof, from the menu choose "Verify Proof". 


ReadMe 3. [Machine Learning Model](#iot-device-execution-to-execute-on-esp32-microcontroller)  
   - [Prerequisites](#prerequisites-1)  
   - [Step 1: Writing a C++ Program](#step-1-writing-a-c-program-1)  
   - [Step 2: Compile and Generate an Assembly File](#step-2-compile-and-generate-an-assembly-file-1)  
   - [Step 3: Download and Edit `device_config.json`](#step-3-download-and-edit-device_configjson-1)  
   - [Step 4: Download the `setupN.json` File](#step-4-download-the-setupNjson-file-1)  
   - [Step 5: Download and Execute `commitmentGenerator`](#step-5-download-and-execute-commitmentgenerator-1)  
   - [Step 6: Compile and Execute](#step-6-compile-and-execute-1)
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
