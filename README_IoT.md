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
  - 1- Execute a program on a computer
  - 2- Execute a firmware on an IoT device
  - 3- Execute a machine learning model 

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
```