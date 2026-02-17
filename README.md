
# Hermes Bytecode v98 Analysis Toolkit

This repository contains the specialized build instructions and documentation required to disassemble Hermes Bytecode (HBC) Version 98.

HBC v98 is the standard for the Hermes V1 engine architecture used in React Native 0.76 - 0.83+. Standard tools and legacy branches typically only support up to Version 96.

----------

## 🛠 Setup & Compilation

To handle v98 bundles, the compiler must be built from the specific development branch that contains the updated "Hermes V1" bytecode definitions.

### 1. Prerequisites

Ensure your Mac has the required build tools installed via Homebrew:

brew install cmake ninja

### 2. Building the Compiler, download from repo

### Clone the repository
git clone https://github.com/facebook/hermes.git
cd hermes
  

### 3. Switch to the branch supporting the HBC v98 bump Fetch and Switch to the v98 branch

git fetch origin

git checkout cipolleschi/bump-hermesv1-083

### 4. Delete any existing build artifacts to avoid version conflicts

rm -rf build_release

### 5. Configure the fresh build

cmake -S . -B build_release -G Ninja -DCMAKE_BUILD_TYPE=Release

### 6. Build the hermesc compiler specifically

cmake --build ./build_release --target hermesc


### 7. Verify Binary Capability

Confirm the binary is reporting the correct version before use:

./build_release/bin/hermesc -version

Expected Output:  HBC bytecode version: 98

----------

## 📂 Disassembly Workflow

Use the following command to transform your binary bundle (index.android.bundle) into a searchable assembly text file:

./build_release/bin/hermesc -b -dump-bytecode index.android.bundle -out dump.txt

### Flag Breakdown:

-   -b: Bytecode Mode. Essential flag that tells the tool the input is a binary file. Without this, hermesc will attempt to parse the file as JavaScript text and throw "Invalid UTF-8" errors.
    
-   -dump-bytecode: Triggers the disassembler to output human-readable Hermes assembly instructions.
    
-   -out dump.txt: Directs the output to a text file. Production bundle dumps are extremely large and should be analyzed in a text editor like VS Code or Sublime Text.
    

----------

## 🔍 Navigating the Output

The dump.txt file is your primary resource for reverse-engineering the application logic:

### 1. The String Table

Located at the top of the file. This is the most critical section for finding:

-   API Endpoints, hardcoded URLs, and internal IP addresses.
    
-   Encryption keys, analytics tokens, or Firebase configurations.
    
-   Internal function names and variable names (if not fully minified).
    

### 2. Opcodes (The Logic)

The core app logic is represented in Hermes assembly. Common v98 instructions include:

-   GetGlobalObject: Used to access global APIs like fetch, XMLHttpRequest, or Storage.
    
-   LoadConstString: Used whenever the app pulls a value from the String Table into a register.
    
-   Call: The execution of a function or method.
    
