# 04.asm - 32-Bit Stack Frames and Local Variables

## Overview
This program demonstrates how a stack frame operates in a 32-bit (x86) architecture. It is the exact 32-bit equivalent of `02.asm`. Instead of using 64-bit registers (`RSP`, `RBP`, `RAX`) and 8-byte stack shifts, it uses the `BITS 32` directive, 32-bit registers (`ESP`, `EBP`, `EAX`), and shifts the stack in 4-byte increments. It also uses the traditional 32-bit Linux interrupt (`int 0x80`) for system calls rather than the 64-bit `syscall` instruction.

## Stack Mechanics and Execution Flow
From the moment execution begins at `_start`, the stack operates in the following sequence:

1. **The Function Call:**
   * `call work`: The CPU pushes the 32-bit return address onto the stack. Because this is a 32-bit program, the Stack Pointer (`ESP`) decreases by exactly 4 bytes. Execution jumps to the `work` label.

2. **Function Prologue (Setting up the 32-bit Stack Frame):**
   * `push ebp`: The caller's 32-bit Base Pointer is saved onto the stack. `ESP` decreases by 4 bytes.
   * `mov ebp, esp`: The Base Pointer (`EBP`) is locked to the current top of the stack to serve as the frame pointer.
   * `sub esp, 8`: The stack pointer is moved down by 8 bytes. This allocates space for exactly two 32-bit (4-byte) local variables.

3. **Using Local Variables:**
   * `mov dword [ebp - 4], 100`: The decimal value 100 is written as a 32-bit double-word (`dword`) into the stack memory, 4 bytes below the base pointer.
   * `mov dword [ebp - 8], 200`: The decimal value 200 is written into the stack memory, 8 bytes below the base pointer.
   * `mov eax, [ebp - 4]` and `mov ebx, [ebp - 8]`: The program reads the `dword` values back out of the stack memory and loads them into the EAX and EBX registers.

4. **Function Epilogue (Tearing down the Stack Frame):**
   * `mov esp, ebp`: The stack pointer is instantly reset to the base pointer, wiping out the 8 bytes of allocated local variable space.
   * `pop ebp`: The original Base Pointer is popped off the stack and restored to the `EBP` register. `ESP` increases by 4 bytes.

5. **Returning and Exiting:**
   * `ret`: The CPU pops the 32-bit return address off the stack and into the Instruction Pointer (EIP). `ESP` increases by 4 bytes, restoring the stack to its original state.
   * Execution resumes at `_start`. The program sets EAX to 1 (the 32-bit syscall number for `sys_exit`), zeroes out EBX for a return code of 0, and triggers the kernel with `int 0x80`.

## Step-by-Step ESP Changes
Every push, pop, and return in this 32-bit architecture modifies the Stack Pointer (`ESP`) by 4 bytes:
* Initial State at `_start`: `ESP = [Base Address]`
* After `call work`: `ESP = ESP - 4` (Return address pushed)
* After `push ebp`: `ESP = ESP - 8` (Old EBP pushed)
* After `sub esp, 8`: `ESP = ESP - 16` (8 bytes allocated for two dword variables)
* After `mov esp, ebp`: `ESP = ESP - 8` (Local variables deallocated)
* After `pop ebp`: `ESP = ESP - 4` (Old EBP restored)
* After `ret`: `ESP = [Base Address]` (Original state, return address popped)

## Observations
* **Architecture Differences:** This highlights the strict differences between 32-bit and 64-bit assembly. 64-bit uses `qword` (8 bytes), `RSP/RBP`, and `syscall 60` for exit. 32-bit uses `dword` (4 bytes), `ESP/EBP`, and `int 0x80` with EAX=1 for exit. 
* **Data Sizing:** When manipulating memory directly in 32-bit mode, the `dword` (Double Word) keyword is explicitly required so the assembler knows exactly how many bytes of the stack to overwrite.