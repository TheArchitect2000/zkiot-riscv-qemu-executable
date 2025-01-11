<p align="center">
  <a href="https://fidesinnova.io/" target="blank">
    <img src="https://fidesinnova.io/Download/logo/g-c-web-back.png" alt="Fides Innova Logo">
  </a>
</p>

<h1 align="left">zk-IoT: Enabling Verifiable Computing for Machine Learning Models</h1>

<p align="left">
  <a href="https://www.npmjs.com/~nestjscore" target="_blank">
    <img src="https://img.shields.io/npm/v/@nestjs/core.svg" alt="NPM Version">
  </a>
  <a href="https://www.npmjs.com/~nestjscore" target="_blank">
    <img src="https://img.shields.io/npm/l/@nestjs/core.svg" alt="Package License">
  </a>
  <a href="https://www.npmjs.com/~nestjscore" target="_blank">
    <img src="https://img.shields.io/npm/dm/@nestjs/common.svg" alt="NPM Downloads">
  </a>
  <a href="https://circleci.com/gh/nestjs/nest" target="_blank">
    <img src="https://img.shields.io/circleci/build/github/nestjs/nest/master" alt="CircleCI">
  </a>
  <a href="https://coveralls.io/github/nestjs/nest?branch=master" target="_blank">
    <img src="https://coveralls.io/repos/github/nestjs/nest/badge.svg?branch=master#9" alt="Coverage">
  </a>
  <a href="https://discord.com/invite/NQdM6JGwcs" target="_blank">
    <img src="https://img.shields.io/badge/discord-online-brightgreen.svg" alt="Discord">
  </a>
  <a href="https://twitter.com/FidesInnova" target="_blank">
    <img src="https://img.shields.io/twitter/follow/nestframework.svg?style=social&label=Follow">
  </a>
</p>


This guide will teach you how to:
- **Step A:** Create a commitment for computation from a machine learning model and submit it to the blockchain.
- **Step B:** Execute the computation, generate a ZK proof to demonstrate the correctness of the execution, and submit it to the blockchain.
- **Step C:** Verify the ZK proof using a blockchain explorer or a verifier program.

## Table of Contents
[Step A: Commitment Generation](#step-a-commitment-generation)
   - [A.1: Generating C Code with Cython](#a1-generating-c-code-with-cython-converting-python-code-to-c-and-compiling-for-risc-v-architecture)
     - [A.1.1: Create a .pyx File](#a11-create-a-pyx-file)
     - [A.1.2: Create a setup.py File](#a12-create-a-setuppy-file)
     - [A.1.3: Compile with Cython](#a13-compile-with-cython)
   - [A.2: Setting Up for RISC-V Architecture](#a2-setting-up-for-risc-v-architecture)
     - [A.2.1: Dependencies](#a21-dependencies)
     - [A.2.2: Compile the C Code](#a22-compile-the-c-code)
   - [A.3: Generating an Executable (Optional)](#a3-generating-an-executable-optional)
   - [A.4: Simulating or Running on RISC-V](#a4-simulating-or-running-on-risc-v)
     - [A.4.1: Install QEMU](#a41-install-qemu)
     - [A.4.2: Run the File in QEMU](#a42-run-the-file-in-qemu)
[Important Notes](#important-notes)

# Step A. Commitment Generation

## A.1. Generating C Code with Cython (Converting Python Code to C and Compiling for RISC-V Architecture)

### A.1.1. Create a .pyx file
- First, rename your Python file to .pyx:

```
mv test.py test.pyx
```
### A.1.2. Create a setup.py file
- Create a new file named setup.py and add the following code:
```
from distutils.core import setup
from Cython.Build import cythonize

setup(
    ext_modules=cythonize("test.pyx")
)
```
### A.1.3. Compile with Cython
- Run the following command to generate the C code:
(pip3 install cython)
sudo apt-get install libc6-dev-riscv64-cross
sudo apt-get install libpython3-dev-riscv64-cross
riscv64-unknown-elf-gcc --print-sysroot

```
python3 setup.py build_ext --inplace
```
- After running this command, a C file (e.g., test.c) will be generated.

## A.2. Setting Up for RISC-V Architecture

### A.2.1. Dependencies
- To run the generated C code on the RISC-V architecture, you need specific libraries:
   libpython: To interface the C code with the Python environment.
   RISC-V compiler such as riscv64-unknown-elf-g++ or riscv32-unknown-elf-gcc.
  
### A.2.2. Compile the C Code
- Use the RISC-V compiler to generate assembly code. If your C file is test.c, run:

```
riscv64-linux-gnu-gcc -march=rv32gc -mabi=ilp32 -I/usr/include/python3.8  -I/usr/include  -I/usr/riscv64-linux-gnu/include/  -o demo3asembly.s -S demo3.
```
```
riscv64-unknown-elf-gcc -S demo3.c -o demo3asembly.s -march=rv32gc -mabi=ilp32 -I/usr/include -I/usr/include/python3.8
```
```
riscv64-unknown-elf-gcc -S test.c -o test.s -march=rv32gc -mabi=ilp32
```
- S: Generates assembly code.<br>
- o test.s: Outputs the result to test.s.<br>
- march=rv32gc: Specifies the 32-bit RISC-V architecture with support for standard and compressed instructions.<br>
- mabi=ilp32: Uses the 32-bit ABI model.<br>

## A.3. Generating an Executable (Optional)
- If you want to generate an executable, use the following command:

```
riscv64-unknown-elf-gcc test.c -o test.elf -march=rv32gc -mabi=ilp32 -lpython3.10
```
- lpython3.10: Links the Python library (change the version based on your installation).

## A.4. Simulating or Running on RISC-V
- If you don't have access to a real RISC-V processor, you can use a simulator.

### A.4.1. Install QEMU
- First, install QEMU:
```
sudo apt install qemu-system-misc
```
## A.4.2. Run the File in QEMU
- To run the test.elf file in QEMU, use:
```
qemu-riscv32 test.elf
```
### Important Notes
- The C code generated by Cython includes Python API functions. Therefore, you'll need the appropriate Python libraries for the RISC-V architecture.<br>
- RISC-V is commonly used in embedded systems, so this process is typically done in such environments.<br>
- If you have questions about dependencies or encounter errors, please provide additional details about your environment.<br>
