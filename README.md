a programer daily live suppose you write this c program as a devloper you will
a compiled language (programer ) there is (compiled , interpreted , virtual machine ) languages
1-Compiled Languages
These are translated directly to machine code by a compiler. The CPU runs the final binary.
2-Interpreted Languages
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

3- Virtual Machine
These are compiled to an intermediate form called bytecode, then executed by a virtual machine.
`
`Assembly is just a human-readable form of machine-code`
our interest is on compiled language here :
```c
include <stdio.h>
include <stdlib.h>
int main(){
 int a= 5 ;
int b = 7 ;
int c = 8 ;
int sum = a + b + c ;
    return sum
```
all what you care about is writing the code after you think that you make the code done "painfully " you will
```gcc main.c -o main``` what is ``` gcc main.c -o main ``` this will preprocess (getting the code of the includes ) and assemble the source code then link it (so it is ready for you to run)
show them `gcc -E main.c -o prep`
then show them `gcc -S main.c -o asm`
this is example
do you think your CPU understand this ? `int a = 4 ;` , ` sum = a + b +c ;`
look how the cpu looks like
`./cpu.png`
`./CPU-registers.png`
`./mmu.png`
how can that  to understand that ` sum = a +  b +c `
so we need to find syntax that uses the cpu resources (ram , registers ,....) , let us find it together , note that we will find it to the shown cpu , because cpus differ (this what we call cpu architecture )
cpu needs someone to tell him to mov the value (local variable ) at this address (memory case ) to this another local variable (sum) `MOV` keep it in your eyes .
cpu needs a place to store the moved value so he can access it fastly (not go to the memory and ... `MOV ` again ) let us call this place `GENERAL USE REGISTERS ` if we say places .
cpu needs to store the address of the pointer to this memory so we call that `LEA `
cpu needs to `+` `ADD` and needs to `-` SUB ,needs to `*` , cpu needs to `/` ,  (cpu can't ADD two memory addresses or SUB two memory addresses ) instead ADD SUB MUL IMUL works with registers (why ? near )
cpu needs to do a loooot of instructions ..

