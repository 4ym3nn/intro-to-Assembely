A programmer's daily life: suppose you write this C program as a developer. You will be working with a compiled language. As a programmer, there are three types of languages: (compiled, interpreted, virtual machine).

## 1-Compiled Languages
These are translated directly to machine code by a compiler. The CPU runs the final binary.

## 2-Interpreted Languages
These are read and executed line by line by an interpreter, without producing a binary.

`Python (CPython) — source → bytecode → Python VM executes`

```
Your code (.py)
       ↓ compiled to bytecode (.pyc)
Python Virtual Machine (PVM) interprets bytecode
       ↓
Executed step by step
```

The PVM executes the bytecode instruction by instruction (interpreted), not directly by the CPU.

## 3-Virtual Machine
These are compiled to an intermediate form called bytecode, then executed by a virtual machine.

`Assembly is just a human-readable form of machine-code`

Our interest is on compiled languages here:

```c
#include <stdio.h>
#include <stdlib.h>
int main(){
    int a = 5;
    int b = 7;
    int c = 8;
    int sum = a + b + c;
    return sum;
}
```

All you care about is writing the code. After you think you've finished the code "painfully", you will run:

```bash
gcc main.c -o main
```

What is `gcc main.c -o main`? This will preprocess (getting the code of the includes) and assemble the source code then link it (so it is ready for you to run).

Show them:
```bash
gcc -E main.c -o prep
```

Then show them:
```bash
gcc -S main.c -o asm
```

This is an example.

Do you think your CPU understands this? `int a = 4;`, `sum = a + b + c;`

Look at how the CPU looks:

![cpu](./cpu.png)

`./cpu.png`

![cpu](./CPU-registers.png)

`./CPU-registers.png`

![mmu](./mmu.png)

`./mmu.png`

How can it understand `sum = a + b + c`?

So we need to find a syntax that uses the CPU resources (RAM, registers, ....). Let's find it together. Note that we will find it for the shown CPU, because CPUs differ (this is what we call CPU architecture).

The CPU needs someone to tell it to move the value (local variable) at this address (memory location) to another local variable (sum). `MOV` - keep it in mind.

The CPU needs a place to store the moved value so it can access it quickly (not go to the memory and... `MOV` again). Let's call this place `GENERAL USE REGISTERS` if we say places.

The CPU needs to store the address of the pointer to this memory, so we call that `LEA`.

The CPU needs to `+` `ADD` and needs to `-` `SUB`, needs to `*`, the CPU needs to `/`. (The CPU can't ADD two memory addresses or SUB two memory addresses) instead ADD SUB MUL IMUL work with registers (why? coming soon).

CPU needs to do a loooot of instructions..
### General Purpose Registers

| 64-bit | 32-bit | 16-bit | 8 high bits (AH, etc.) | 8-bit | Description |
|--------|--------|--------|------------------------|--------|-------------|
| RAX    | EAX    | AX     | AH                     | AL     | Accumulator |
| RBX    | EBX    | BX     | BH                     | BL     | Base |
| RCX    | ECX    | CX     | CH                     | CL     | Counter |
| RDX    | EDX    | DX     | DH                     | DL     | Data (extends accumulator) |
| RSI    | ESI    | SI     | N/A                    | SIL    | Source index for string ops |
| RDI    | EDI    | DI     | N/A                    | DIL    | Destination index for string ops |
| RSP    | ESP    | SP     | N/A                    | SPL    | Stack Pointer |
| RBP    | EBP    | BP     | N/A                    | BPL    | Base Pointer (stack frames) |
| R8     | R8D    | R8W    | N/A                    | R8B    | General purpose |
| R9     | R9D    | R9W    | N/A                    | R9B    | General purpose |
| R10    | R10D   | R10W   | N/A                    | R10B   | General purpose |
| R11    | R11D   | R11W   | N/A                    | R11B   | General purpose |
| R12    | R12D   | R12W   | N/A                    | R12B   | General purpose |
| R13    | R13D   | R13W   | N/A                    | R13B   | General purpose |
| R14    | R14D   | R14W   | N/A                    | R14B   | General purpose |
| R15    | R15D   | R15W   | N/A                    | R15B   | General purpose |
### Pointer Registers

| 64-bit | 32-bit | 16-bit | Description |
|--------|--------|--------|-------------|
| RIP    | EIP    | IP     | Instruction Pointer |


Assembly is not universal. It is specific to the processor architecture (the CPU family). Each CPU has its own instruction set, registers, and assembly syntax.

| CPU Architecture             | Assembly Example     |
|-----------------------------|----------------------|
| x86 (32-bit Intel/AMD)      | mov eax, 5           |
| x86_64 (64-bit Intel/AMD)   | mov rax, 5           |
| ARM (phones, Raspberry Pi)  | MOV R0, #5           |
| RISC-V                      | addi x1, x0, 5       |
| MIPS                        | addi $t0, $zero, 5   |

so our interest here will be in x86_64 (64 bit ) architecture :

| Register | Name            | Purpose                                                                                                                                                                               |
| -------- | --------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **CS**   | Code Segment    | Points to the segment that contains the program instructions (machine code). The CPU uses CS together with the Instruction Pointer (IP/RIP) to fetch the next instruction to execute. |
| **DS**   | Data Segment    | Points to the segment that contains program data like variables and constants. Used by default for most memory access instructions.                                                   |
| **SS**   | Stack Segment   | Points to the segment that contains the stack, which stores function return addresses, local variables, and parameters. Used with the stack pointer (SP/RSP).                         |
| **ES**   | Extra Segment   | An additional data segment, mainly used by string operations (like `MOVS`, `LODS`, `STOS`) as a destination segment.                                                                  |
| **FS**   | Extra Segment 2 | General-purpose segment, often used in modern systems for thread-local storage (TLS).                                                                                                 |
| **GS**   | Extra Segment 3 | Also general-purpose, commonly used in 64-bit Linux for accessing process or CPU-specific data structures.                                                                            |

Are these still used today?

In 32-bit mode (x86): Segmentation is still supported but rarely used except for special purposes.

In 64-bit mode (x86_64): Segmentation is almost disabled. Only FS and GS are still useful, mainly for thread-local storage and system structures.

CS, DS, SS, and ES are mostly ignored or set to fixed values in 64-bit mode.

Data movement Instructions :
| Instruction | Purpose                                                  |
| ----------- | -------------------------------------------------------- |
| `mov`       | Copy data from one place to another                      |
| `lea`       | Load address computation (useful for pointer arithmetic) |
| `push`      | Push a value onto the stack                              |
| `pop`       | Pop a value from the stack                               |

Aritchmetic Instructions :
| Instruction | Purpose                |
| ----------- | ---------------------- |
| `add`       | Addition               |
| `sub`       | Subtraction            |
| `inc`       | Increment (add 1)      |
| `dec`       | Decrement (subtract 1) |
| `imul`      | Signed multiplication  |
| `idiv`      | Signed division        |

Logic & Bitwise Instructions :
| Instruction   | Purpose                                                        |
| ------------- | -------------------------------------------------------------- |
| `and`         | Bitwise AND                                                    |
| `or`          | Bitwise OR                                                     |
| `xor`         | Bitwise XOR (also used to clear registers like `xor rax, rax`) |
| `not`         | Bitwise NOT                                                    |
| `shl` / `shr` | Bit shifting left or right                                     |

Comparision & Control Flow :
| Instruction   | Purpose                                                             |
| ------------- | ------------------------------------------------------------------- |
| `cmp`         | Compare two values                                                  |
| `test`        | Bitwise test (like `and` but doesn’t store result, only sets flags) |
| `jmp`         | Unconditional jump                                                  |
| `je` / `jne`  | Jump if equal / not equal                                           |
| `jg` / `jl`   | Jump if greater / less                                              |
| `jge` / `jle` | Jump if greater/equal or less/equal                                 |
| `call`        | Call a function                                                     |
| `ret`         | Return from function                                                |

Stack Procdeure Basics :
| Instruction | Purpose                                  |
| ----------- | ---------------------------------------- |
| `push`      | Save data/return address to stack        |
| `pop`       | Restore data from stack                  |
| `call`      | Jump to a function and save return point |
| `ret`       | Return to caller                         |
| `leave`     | Clean up stack frame                     |

Syscalls :
| Instruction | Purpose                       |
| ----------- | ----------------------------- |
| `syscall`   | Perform a system call (Linux) |

More :

| Instruction | Purpose                                       |
| ----------- | --------------------------------------------- |
| `movzx`     | Move with zero extension                      |
| `movsx`     | Move with sign extension                      |
| `cmov`      | Conditional move (fast branching alternative) |
| `nop`       | Do nothing (used in alignment/optimization)   |

