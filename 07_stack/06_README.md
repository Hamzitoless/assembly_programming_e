# 06.asm - 32-Bit Register Preservation (Symmetric Push and Pop)

## Overview
This program demonstrates the standard Last-In, First-Out (LIFO) method for preserving and restoring register values, specifically within a 32-bit (x86) architecture. It is the direct 32-bit equivalent of `05.asm`. By using the `BITS 32` directive, the program interacts with 32-bit registers (`EAX`, `EBX`), shifts the stack in 4-byte increments, and exits using the traditional Linux kernel interrupt (`int 0x80`).

## Stack Mechanics and Execution Flow
From the moment execution begins at `_start`, the stack operates in the following sequence:

1. **Initialization:**
   * `mov eax, 0x11223344`: Loads a 32-bit hexadecimal value into the EAX register.
   * `mov ebx, 0x1234`: Loads a smaller hex value into EBX. The CPU automatically pads this to fill the 32-bit register (`0x00001234`).

2. **Pushing to the Stack (Saving States):**
   * `push eax`: The Stack Pointer (`ESP`) decreases by 4 bytes. The 32-bit value `0x11223344` is written to the stack memory.
   * `push ebx`: `ESP` decreases by another 4 bytes. The padded value `0x00001234` is written to the new top of the stack.

3. **Popping from the Stack (Restoring States):**
   * `pop ebx`: To restore the registers to their original states without swapping their data, the program must pop them in reverse order. The CPU reads the top 4 bytes (`0x1234`) back into EBX, and adds 4 bytes to `ESP`.
   * `pop eax`: The CPU reads the next 4 bytes (`0x11223344`) back into EAX, and adds another 4 bytes to `ESP`.

4. **Program Exit:**
   * Both registers have recovered their exact original data.
   * The program executes a clean system exit by setting EAX to 1 (sys_exit), zeroing out EBX, and calling `int 0x80`.

## Step-by-Step ESP Changes
Because this is explicitly a 32-bit program, every push and pop modifies the Stack Pointer (`ESP`) by exactly 4 bytes (32 bits):
* Initial State at `_start`: `ESP = [Base Address]`
* After `push eax`: `ESP = ESP - 4`
* After `push ebx`: `ESP = ESP - 8`
* After `pop ebx`: `ESP = ESP - 4`
* After `pop eax`: `ESP = [Base Address]` (Original state restored)

## Observations
* **Architecture Scaling:** This program perfectly illustrates the hardware difference between 32-bit and 64-bit modes. While `05.asm` pushed 8 bytes per instruction to accommodate `RAX/RBX`, this program strictly pushes 4 bytes per instruction for `EAX/EBX`. 
* **LIFO Symmetry:** Just like in 64-bit mode, successfully saving and restoring multiple registers requires perfectly symmetric LIFO ordering (Push A, Push B ➔ Pop B, Pop A). 
* **Debugging with NOP:** Placing a breakpoint at the `nop` instruction in GDB allows you to inspect the top 8 bytes of the stack to observe how the two 32-bit hex values are stacked in memory before they are popped off.