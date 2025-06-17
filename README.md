## Problem Statement - 9 - Runtime Memory Access Analysis using LLVM/Clang

## Team Members:
- Manaswini Simhadri Kavali- 1RV22CS106
- Mandava Sai Anirudh - 1RV22CS107

This project implements a custom LLVM pass called HeapAccessTracker. It tracks heap access operations in LLVM IR.
https://github.com/kir4nn/CD-EL-HeapAccessTracker/edit/main/README.md
## Prerequisites

- LLVM 15.0.0 or later
- CMake 3.13.4 or later
- GCC 11.3.0 or later

## Setup and Installation

1. Clone the HeapAccessTracker repository:
   ```bash
   git clone https://github.com/kir4nn/CD-EL-HeapAccessTracker.git
   cd CD-EL-HeapAccessTracker
2. Build LLVM with your new pass:

   ```bash
   mkdir build
   cd build
   cmake -G "Unix Makefiles" -DLLVM_ENABLE_PROJECTS="clang;" \
        -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=../install \
        -DCMAKE_C_COMPILER=/usr/bin/gcc -DCMAKE_CXX_COMPILER=/usr/bin/g++ ../llvm
   make -j4
   make -j4 install
3. Go back to the root directory:
   ```bash
   cd ..
5. Create a test C++ file named test.cpp:
      ```bash
      gedit test.cpp
      ```
   ```
      #include <cstdlib>
      
      void processData(int* data, int size) {
          for (int i = 0; i < size; ++i) {
              data[i] *= 2;
          }
      }
      
      int main() {
          int* arr = (int*)malloc(10 * sizeof(int));
          for (int i = 0; i < 10; ++i) {
              arr[i] = i;
          }
          processData(arr, 10);
          free(arr);
          return 0;
      }
   ```
5. Generate LLVM IR:
   ```bash
   ./build/bin/clang++ -O1 -S -emit-llvm -g test.cpp -o test.ll
6. Run the HeapAccessTracker pass to generate a CSV file from the IR:
   ```bash
   ./build/bin/opt -load-pass-plugin=./build/lib/LLVMHeapAccessTracker.so \
                -passes="heap-access-tracker" -disable-output test.ll -debug-pass-manager
## Project Structure

    llvm-project/llvm/lib/Transforms/HeapAccessTracker/HeapAccessTracker.cpp: Main implementation of the HeapAccessTracker pass.
    llvm-project/llvm/lib/Transforms/HeapAccessTracker/CMakeLists.txt: CMake configuration for the pass.

## Pass Description

The HeapAccessTracker pass analyzes LLVM IR to identify and track heap access operations. It focuses on operations related to memory allocation, deallocation, and access patterns. The pass provides insights into how a program interacts with dynamically allocated memory.

## Output

The pass will output information about heap access operations it detects, including:

    Function name
    Memory address
    Access type (e.g., read, write)
    Source file
    Line number

The output is printed to the csv file during the pass execution.

