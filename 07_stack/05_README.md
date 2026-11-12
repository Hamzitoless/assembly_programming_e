# 05.asm - Preserving Register States (Symmetric Push and Pop)

## Overview
This program demonstrates the standard method for saving and restoring register states using the stack's Last-In, First-Out (LIFO) architecture. Unlike `01.asm` which deliberately swapped values by popping them into different registers, this program uses symmetric ordering (Push A, Push B ➔ Pop B, Pop A) to ensure each register gets its original value back. It also highlights how 64-bit stack alignment handles varying data sizes.

## Stack Mechanics and Execution Flow
From the moment execution begins at `_start`, the stack operates in the following sequence:

1. **Initialization:**
   * `mov rax, 0x1122334455667788`: Loads a massive 64-bit hexadecimal value into RAX.
   * `mov rbx, 0x1234`: Loads a much smaller hexadecimal value into RBX. The CPU automatically pads this with leading zeros to fill the 64-bit register (`0x0000000000001234`).

2. **Pushing to the Stack (Saving States):**
   * `push rax`: The Stack Pointer (`RSP`) decreases by 8 bytes. The 64-bit hex value `0x1122334455667788` is written to the stack.
   * `push rbx`: `RSP` decreases by another 8 bytes. The zero-padded value `0x0000000000001234` is written to the new top of the stack.

3. **Popping from the Stack (Restoring States):**
   * `pop rbx`: Because `rbx` was the *last* register pushed, it must be the *first* one popped to get its original value back. The CPU reads the top 8 bytes (`0x1234`) back into RBX, and adds 8 bytes to `RSP`.
   * `pop rax`: The CPU reads the next 8 bytes (`0x1122334455667788`) back into RAX, and adds another 8 bytes to `RSP`. 

4. **Program Exit:**
   * Both registers have successfully recovered their exact original data.
   * The program executes a clean system exit (`syscall 60`).

## Step-by-Step RSP Changes
Regardless of how large or small the actual hex number is, the 64-bit architecture forces every push and pop to shift `RSP` by exactly 8 bytes:
* Initial State at `_start`: `RSP = [Base Address]`
* After `push rax`: `RSP = RSP - 8`
* After `push rbx`: `RSP = RSP - 16`
* After `pop rbx`: `RSP = RSP - 8`
* After `pop rax`: `RSP = [Base Address]` (Original state restored)

## Observations
* **Symmetric LIFO Order:** To preserve data safely without mixing it up, you must always `pop` in the exact reverse order that you `push`. 
* **64-bit Stack Padding:** Even though `0x1234` mathematically only requires 2 bytes of memory, the `push rbx` instruction still allocates a full 8 bytes (64 bits) on the stack to maintain structural alignment.
* **Debugging with NOP:** Placing a breakpoint at the `nop` instruction in GDB will let you inspect the memory at `RSP` and `RSP+8` to see exactly how these hex values are laid out in memory (which will be in Little-Endian byte order) before they are popped back out.