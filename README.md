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
      #include <ctime>

   void compute(int* buffer, int size) {
    for (int i = 0; i < size; ++i) {
        buffer[i] = buffer[i] * buffer[i];  // read + write
    }
   }

   int main() {
    std::srand(std::time(nullptr));
    
    int* data1 = (int*)malloc(5 * sizeof(int));
    int* data2 = (int*)malloc(5 * sizeof(int));

    for (int i = 0; i < 5; ++i) {
        data1[i] = i + 1;
        data2[i] = std::rand() % 100;
    }

    compute(data1, 5);
    
    if (data2[2] % 2 == 0) {  // conditional read
        data2[2] = 42;        // write
    }

    free(data1);
    free(data2);
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

