# 03.asm - Subroutines and Return Addresses

## Overview
This program demonstrates the core mechanism of subroutines in assembly. It shows how the `call` and `ret` instructions use the stack to implicitly save and restore the execution path. Unlike `02.asm` which built a full stack frame, this program is lightweight and only uses the stack to manage the return address.

## Stack Mechanics and Execution Flow
From the moment execution begins at `_start`, the stack operates in the following sequence:

1. **Initialization:**
   * `mov rax, 10` loads the decimal value 10 into the RAX register.

2. **The Function Call (Pushing the Return Address):**
   * `call calculate`: This single instruction does two things automatically:
     1. It pushes the memory address of the very next instruction (the label `after_call`) onto the stack. The Stack Pointer (`RSP`) decreases by 8 bytes.
     2. It jumps execution to the `calculate` label.

3. **Inside the Subroutine:**
   * `add rax, 5`: The CPU adds 5 to the existing value in RAX (10). RAX now holds 15. The stack is completely untouched during this operation.

4. **Returning (Popping the Return Address):**
   * `ret`: This instruction looks at the top of the stack (pointed to by `RSP`) and pops the 8-byte return address off of it. It loads this address directly into the Instruction Pointer (RIP). `RSP` increases by 8 bytes, removing the address from the stack.
   * Execution immediately resumes at the `after_call` label.

5. **Program Exit:**
   * The program hits a `nop` buffer, and then executes a clean system exit (`syscall 60`).

## Step-by-Step RSP Changes
Because this program only uses the stack for execution flow (not for data storage), the RSP changes are minimal:
* Initial State at `_start`: `RSP = [Base Address]`
* After `call calculate`: `RSP = RSP - 8` (Return address `after_call` is pushed)
* Inside `calculate`: `RSP = RSP - 8` (Unchanged)
* After `ret`: `RSP = [Base Address]` (Original state, return address popped)

## Observations
* **Hidden Stack Usage:** The words `push` and `pop` do not appear anywhere in this code, yet the stack is actively utilized. The `call` and `ret` instructions are basically specialized push and pop operations dedicated to the Instruction Pointer.
* **Shared Registers:** The `calculate` function modifies RAX directly. When the function returns, RAX retains the modified value of 15. This shows that without a stack frame or explicit `push`/`pop` operations to save registers, subroutines will permanently alter the caller's register states.
* **Debugging with NOP:** The `nop` placed after the return provides an excellent spot to set a breakpoint in GDB to verify that the subroutine executed correctly and RAX equals 15 before the program terminates.