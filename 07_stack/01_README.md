# 01.asm - Basic Stack Operations (Push and Pop)

## Overview
This program demonstrates the fundamental Last-In, First-Out (LIFO) structure of the x86_64 stack. It pushes two 64-bit register values onto the stack and then pops them back out into different registers, effectively swapping their order due to the stack's LIFO architecture.

## Stack Mechanics and Execution Flow
From the moment execution begins at `_start`, the stack operates in the following sequence:

1. **Initialization:** 
   * `mov rax, 100` loads the decimal value 100 into the RAX register.
   * `mov rbx, 200` loads the decimal value 200 into the RBX register.

2. **Pushing to the Stack:**
   * `push rax`: The CPU subtracts 8 bytes from the Stack Pointer (`RSP`), growing the stack downward in memory. The value 100 is written to the memory address now pointed to by `RSP`.
   * `push rbx`: The CPU subtracts another 8 bytes from `RSP`. The value 200 is written to the new top of the stack. 
   * *Stack State:* The top of the stack (pointed to by `RSP`) now holds `200`. The memory address 8 bytes above it holds `100`.

3. **Popping from the Stack (LIFO):**
   * `pop rcx`: The CPU reads the value at the current `RSP` (which is 200) and places it into the RCX register. It then adds 8 bytes to `RSP` to "remove" the item from the stack.
   * `pop rdx`: The CPU reads the new value at `RSP` (which is 100) and places it into the RDX register. It adds another 8 bytes to `RSP`.

4. **Program Exit:**
   * The stack pointer (`RSP`) has now returned to its exact original starting address.
   * The program executes a clean system exit (`syscall 60`) with a return code of 0.

## Step-by-Step RSP Changes
Because this is a 64-bit architecture, every `push` and `pop` modifies the 64-bit Stack Pointer (`RSP`) by 8 bytes:
* Initial State: `RSP = [Base Address]`
* After `push rax`: `RSP = RSP - 8`
* After `push rbx`: `RSP = RSP - 16`
* After `pop rcx`: `RSP = RSP - 8`
* After `pop rdx`: `RSP = [Base Address]` (Original state)

## Observations
* **No Terminal Output:** Running `./01` produces no visible output on the terminal because there are no print system calls (`syscall 1`). 
* **Debugging with NOPs:** The inclusion of the `nop` (No Operation) instructions acts as a buffer. In a debugger like GDB, these `nop` lines provide convenient places to set breakpoints so you can inspect the registers (`info registers`) and watch the values of RAX and RBX successfully transfer into RCX and RDX.
* **LIFO Verification:** The program proves the Last-In, First-Out concept. RBX (200) was the last item pushed, but it was the first item popped out, landing in RCX.