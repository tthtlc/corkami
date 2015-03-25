this page is based on this blog [post](http://corkami.blogspot.com/2010/03/si-cest-ton-corps-qui-bouge-cest-ton.html)
# Introduction #

While standard code starts at a fixed address, there are several cases when your code needs to know its current IP:
  * after a vulnerability has been triggered, shellcodes can't know in advance where they are executing exactly
  * packers often allocates a buffer and decompress their next layer of code, which will likely need to locate itself at some point
  * relocating code is a good way to avoid breakpoints: same code, somewhere else
## Call/Pop ##

Since CALLs push the next address on the stack, you just need to grab it with a POP and will get the current address.

Since a standard E8 call is encoded on 5 bytes, such a 'next line' call is often written CALL $ + 5, which is encoded as E8 00000000
```
    call $ + 5
after_call:
    pop edx
    cmp edx, after_call
    jnz bad
```
## FPU ##

the FPU knows the address of the last executed fpu instruction. so, to get the current IP, use any FPU opcode - even FNOP - then store the FPU environment in memory via F(N)STENV:
```
_fpu:
    fnop
    fnstenv [fpuenv]
    mov edx,[fpuenv.DataPointer]
    cmp edx, _fpu
    jnz bad
```
## Interrupts ##
(Win32 only)
Interrupts 2C and 2E will put into EDX the next address. If you step on it with a debugger, it will probably not work correctly
```
    int 02eh
after_int:
    cmp edx, after_int
    jnz bad
```
## Exceptions ##
When an exception is triggered, the context of the trigger will be put on the stack, so it's possible to know the address of the trigger this way:
```
handler:
    mov eax, [esp + 0ch]
    cmp dword [eax + 0b8h], address
    jnz bad
```
### before ###
Most exceptions are triggered before executing an incorrect line:
```
    xor eax, eax
_on_the_instruction:
    mov [eax], eax
```
### after ###
Some exceptions are triggered AFTER executing an instruction that launched them (on purpose):
```
    db 0f1h ; IceBP
_trigger_after_execution:
```
### next ###
And then some exceptions are triggered the instruction after, to enable stepping:
```
    push 302h
    popf
    jmp bad
_after bad
```