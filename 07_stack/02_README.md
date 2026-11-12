# 02.asm - Stack Frames and Local Variables

## Overview
This program demonstrates how the stack is used to manage function calls and local variables. Instead of simply pushing and popping values, it introduces the concept of a **Stack Frame** (or Activation Record) using the Base Pointer (`RBP`) and the Stack Pointer (`RSP`). It shows how memory is allocated on the stack for local variables during a function call and properly deallocated before returning.

## Stack Mechanics and Execution Flow
From the moment execution begins at `_start`, the stack operates in the following sequence:

1. **The Function Call:**
   * `call work`: The CPU automatically pushes the memory address of the next instruction (the return address) onto the stack so it knows where to go back to later. `RSP` decreases by 8 bytes. Execution jumps to the `work` label.

2. **Function Prologue (Setting up the Stack Frame):**
   * `push rbp`: The current (caller's) Base Pointer is saved onto the stack so it isn't lost. `RSP` decreases by another 8 bytes.
   * `mov rbp, rsp`: The Base Pointer (`RBP`) is updated to point to the current top of the stack. This establishes a fixed reference point (the "frame pointer") for the `work` function.
   * `sub rsp, 16`: The stack pointer is manually moved down by 16 bytes. This effectively reserves (allocates) 16 bytes of space on the stack for local variables.

3. **Using Local Variables:**
   * `mov qword [rbp - 8], 100`: The decimal value 100 is written directly into the allocated stack memory, 8 bytes below the base pointer.
   * `mov qword [rbp - 16], 200`: The decimal value 200 is written into the stack memory, 16 bytes below the base pointer (which happens to be exactly where `RSP` is currently pointing).
   * `mov rax, [rbp - 8]` and `mov rbx, [rbp - 16]`: The program reads those values back out of the stack memory and loads them into the RAX and RBX registers, respectively.

4. **Function Epilogue (Tearing down the Stack Frame):**
   * `mov rsp, rbp`: The stack pointer is instantly moved back to where `RBP` is pointing. This effectively deallocates the 16 bytes of local variable space in one instruction.
   * `pop rbp`: The original Base Pointer value is popped off the stack and restored to the `RBP` register. `RSP` increases by 8 bytes.

5. **Returning and Exiting:**
   * `ret`: The CPU pops the return address (saved during the `call` instruction) off the stack and into the Instruction Pointer (RIP). `RSP` increases by 8 bytes, returning to its exact original state before the `call`.
   * Execution resumes at `_start`, and the program gracefully exits using `syscall 60`.

## Step-by-Step RSP Changes
* Initial State at `_start`: `RSP = [Base Address]`
* After `call work`: `RSP = RSP - 8` (Return address pushed)
* After `push rbp`: `RSP = RSP - 16` (Old RBP pushed)
* After `sub rsp, 16`: `RSP = RSP - 32` (16 bytes allocated for local variables)
* After `mov rsp, rbp`: `RSP = RSP - 16` (Local variables deallocated)
* After `pop rbp`: `RSP = RSP - 8` (Old RBP restored)
* After `ret`: `RSP = [Base Address]` (Original state, return address popped)

## Observations
* **Relative Addressing:** Unlike `01.asm` which used `push` and `pop` to move data, this program accesses the stack directly using memory offsets like `[rbp - 8]`. This allows the program to read or write to local variables in any order, overcoming the strict Last-In, First-Out limitation of `push`/`pop`.
* **No Terminal Output:** Running `./02` produces no visible output because the program merely manipulates memory and registers before exiting cleanly.
* **Debugging with NOP:** The `nop` instruction is placed immediately after the local variables are loaded into RAX and RBX. Setting a breakpoint here in GDB allows you to verify that RAX contains 100 and RBX contains 200 before the stack frame is destroyed.