# _**IN CONSTRUCTION**_ #
# Introduction #

  * this page gathers one line description of opcodes, and a few specific details.

  * UsermodeTest is the practice counterpart of this page. It uses each opcode, and check the result.


  * all exceptions triggers and all calls can be used to get current IP.
> > for exception triggers:
```
structured_exception_handler:
    mov eax, [esp + 0ch]
    mov eax, dword [eax + 0b8h] 
=> eax = eip of the trigger
```

  * Conditional jumps can have a 2e/3e BRANCH TAKEN/NOT TAKEN prefix. there is no official way to assemble nor disassemble them.

  * Calls, `[`conditional`]` jumps, loops, returns, can operate on EIP or IP, with a 66 prefix. there is no standard way to assemble nor disassemble them.
# General #

## standard ##

### nop ###
does nothing.
```
nop
```

maybe exchange `*`ax with `*`ax :)

### fwait ###
waits for fpu stuff to be finished

**NOP**

### Xfence ###
**NOP**
sfence, mfence, lfence

serializing stuff.


### prefetchnta ###
**NOP**

cpu hint

### hint nop ###
cpu hint, nop with operand

never triggers exceptions

### mov ###
move
```
mov eax, 3
=> eax = 3
```
privileged when with dr`*` and cr`*`

### cmovXX ###
mov on condition
```
start: CF, eax, ebx = 0, 0, 3
cmovc eax, ebx
=> eax = 0
```

### lea ###
lea x, `[`y`]` = mov x,y
```
start: eax = 3
lea eax, [eax * 4 + 203A]
=> eax = 2046
```

**64 bits only** can be used to get IP directly
```
48 8d 05 00000000: lea rax, [rip]
```

### movzx ###
mov and extend with zeroes
```
start: al = -1
movzx ecx, al
=> ecx = ff
```

### movsx ###
mov and extend with the sign
```
start: al = -3
movsx ecx, al
=> ecx = -3
```

#### movsxd ####
movsx's **64 bit only** counterpart
```
start: eax = -3
movsxd rcx, eax
=> rcx = -3
```

### xchg ###
swap contents
```
start: al, bl = 1, 2
xchg al, bl
=> al, bl = 2, 1
```

atomic operation. lock prefix not needed

### movs ###
mov ds:`[`edi`]`, es:`[`esi`]`, and inc (or dec) esi and edi

### lods ###
mov `*`ax, es:`[`esi`]`, and inc (or dec) esi

### stos ###
mov ds:`[`edi`]`, `*`ax and inc (or dec) edi

### add ###
addition
```
start: eax = 3
add eax, 3
=> eax = 6
```

### adc ###
add, with carry
```
start: CF, eax = 1, 3
adc eax, 3
=> CF, eax = 0, 7
```

### xadd ###
add and exchange
```
start: al, bl = 1, 2
xadd al, bl
=> al, bl = 3, 1
```

### sub ###
substraction
```
start: eax = 6
sub eax, 3
=> eax = 3
```

### fstenv ###
**GET EIP**
the FPU knows the address of the last executed fpu instruction. so, to get the current IP, use any FPU opcode - even FNOP - then store the FPU environment in memory via F(N)STENV:
```
@last_fpu:
    fnop
    fnstenv [fpuenv]
    mov edx,[fpuenv.DataPointer]
=> edx = @last_fpu
```

### sbb ###
sub, with borrow
```
start: CF, eax = 1, 6
sbb eax, 3
=> eax = 2
```

### inc ###
add 1
```
start: eax = 0
inc eax
=> eax = 1
```

### dec ###
sub 1
```
start: eax = 7
dec eax
=> eax = 6
```

### neg ###
negative
```
start: al = 1
neg al
=> al = -1
```

### div ###
divide ax/dx:ax/edx:eax by operand. Quo/Rem are in al:ah/ax:dx/eax:edx
```
start: ax, bl = 35, 11
div bl
=> ax = 0203
```

### idiv ###
same, with sign

### mul ###
multiply

same registers as div
```
start: al, bl = 11, 3
mul bl
=> ax = 33
```

### imul ###
signed mul. has a 3 operands version
```
start: eax = 11
imul eax, eax, 3
=> eax = 33
```

### aaa ###
ascii adjust after BCD addition
```
start: ax, bx = 304, 307
add ax, bx
aaa
=> ax = 701
```

### aas ###
ascii adjust after substraction
```
start: ax, bx = 1, 4
sub al, bl
aas
=> ax = 7
```

### aam ###
decimal to BCD
```
start: ax = 35
aam
=> ax = 305
```

### aad ###
BCD to decimal:
```
start: ax = 305
aad
=> ax = 35
```

### daa ###
decimal adjust after addition
```
start: ax, bx = 1234, 537
add ax, bx
daa
=> ax = 1771
```

### das ###
decimal adjust after substraction
```
start: ax, bx = 1771, 1234
sub ax, bx
das
=> ax = 537
```

### or ###
or
```
start: eax = 1010b
or eax, 0110b
=> eax = 1110b
```

### and ###
and
```
start: eax = 1010b
and eax, 0110b
=> eax = 0010b
```

### xor ###
exclusive or
```
start: eax = 1010b
xor eax, 0110b
=> eax = 1100b
```

### not ###
logical not:
```
start: al = 1010b
not al
=> al = 11110101b
```

### rol ###
left rotation
```
start: eax = 1010b
rol eax, 3
=> eax = 1010000b
```

### ror ###
right rotation
```
start: al= 1010b
ror al, 3
=> al = 1000001b
```

### rcl ###
rol over carry
```
start: CF, al = 1, 1010b
rcl al, 3
=> al = 1010100b
```

### rcr ###
ror over carry
```
start: CF, al = 1, 1010b, 1
rcr al, 3
=> al = 10100001b
```

### shl ###
shift left (=sal)
```
start: al = 1010b
shl al, 2
=> al = 101000b
```

### shr ###
shift right
```
start: al = 1010b
shr al, 2
=> al = 10b
```

### sar ###
arithmetic shr (propagates sign)
```
start: al = -8
sar al, 2
=> al = -2
```

### shld ###
shift and concatenate
```
start: ax, bx = 1111b, 010...0b
shld ax, bx, 3
=> ax = 1111010b
```

### shrd ###
same as shld, in the right direction
```
start: ax, bx = 1101001b, 101b
shrd ax, bx, 3
=> ax = 10100...001101b
```

### l\*s ###
lds/lss/les/lfs/lgs

loads register and segment
```
[ebx] = 12345678, 0
lds eax, [ebx]
=> ds = 0; eax = 12345678%ds x, [y]
=> ds = [y+4] and x = [y]
```

### loopCC ###
dec ecx. jump if ecx is 0 and extra condition

### repCC ###
repeat operation, decrease counter. stop if condition met or counter is 0

### jecxz ###
jump if `*`cx is null

### jmp ###
eip = operand

### jmpf ###
cs:eip = operands

### jCC ###
jump on condition

### enter ###
= (push ebp/mov ebp, esp) op2+1 times, then esp -= op1

  * $enter$ 4,1 = push ebp/mov ebp, esp/ push ebp/ mov ebp, esp/ esp -= 4}

### leave ###
= mov esp, ebp/pop ebp

### cmp ###
comparison by sub, discard result

### cmps ###
cmp es:`[`esi`]`, ds:`[`edi`]` and inc (or dec) esi and edi

### scas ###
cmp `*`ax, es:`[`edi`]` and inc (or dec) edi

### test ###
comparison by and, discard result

### push ###
push on stack:
```
start: push 12345678
=> esp -= 4
[esp] = 12345678
```

### pushf ###
push EFLAGS on stack

### pusha ###
push eax/ecx/edx/ebx/(original) esp/ebp/esi/edi

### pop ###
pop from stack
```
start: [esp] = 12345678
pop eax
=> esp += 4 , eax = 12345678
```

### popf ###
pop EFLAGS from stack
  * can be used to set TF, the trap flag. It will then trigger an exception after the next instruction.

### popa ###
pop edi/esi/ebp/.../ebx/edx/ecx/eax

### smsw ###
eax=cr0 (non privileged)

officially on ax only, 'undocumented' on eax, but in reality, reliable on eax.
#### anti-debug ####

### lahf ###
ah=flag  (only the flags CF PF AF ZF SF)

### sahf ###
flag=ah

### in ###
read port

privileged

#### anti-vmware ####
vmware backdoor

### ins ###
in es:`[`edi`]`, dx, inc (or dec) edi

### out ###
write port - privileged

### outsd ###
out dx, `[`esi`]`; inc (or dec) esi

### calls ###
#### call ####
push eip of next instruction/eip = `<`operand`>`

works in 16b

#### callf ####
push cs, eip of next instruction/cs:eip = `<`operands`>`

### returns ###
work in 16b

### ret ###
pop eip / esp += `<`operand`>`

#### retf ####
pop eip / pop cs

#### iret ####
pop eip / pop cs + pop eflags

### cbw ###
extend signed value from al to ax
```
start: al = 3
cbw
=> ax = 3
```

### cwd ###
extend signed value from ax to dx:ax
```
start: ax = 3
cwd
=> dx = 0
```

### cwde ###
extend signed value from ax to eax
```
start: ax = 3
cwde
=> eax = 3
```

### cdqe ###
extend signed value from eax to rax

64 bits only
```
start: eax = 012345678h
cdqe
=> rax =  012345678h
```

### bsf ###
scan for the first bit set:
```
start: eax = 0010100b
bsf ebx, eax
=> ebx = 2
```

#### bsr ####
same but from highest bit to lowest bit

### bt ###
copy a specific bit to CF
```
start: ax, bx = 00100b, 2
bt ax,bx
=> CF = 1
```

#### bts/btr/btc ####
same as bt + set/reset/complement that bit

### stc/d/i ###
set CF/DF (rep prefix)/IF (privileged)

#### clc/d/i ####
clear those flags

#### cmc ####
complement CF: CF = !CF

### int ###
trigger interrupt `<`operand`>`

### into ###
trigger interrupt 4 if OF is set

### xlat ###
al = `[`ebx + al`]`
```
start: al, [ebx + 35] = 35, 75
xlatb
=> al = 75
```

segment overridable, but the segment might not be displayed in short form.

#### xlatb ####
xlat's rarer 16b counterpart al = `[`bx + al`]`. it might not be shown in short form.

### bound ###
int5 if op1 < `[`op2`]` & op1 > `[`op2 + size`]`
```
start: eax, [ebx] = 136, [135, 143]
bound eax, ebx
=> nothing
```

a nop or an exception trigger => easy loop with a SEH.

### opsize ###
turns dword operand into word
```
start: ecx = -1
66: inc ecx (=inc cx)
=> ecx = ffff0000
```

### addsize ###
use 16b addressing mode
```
67:add [eax], eax
=> add [bx + si], eax
```

### bswap ###
endian swapping
```
start: eax = 12345678h
bswap eax
=> eax = 78563412
```

**undocumented** on 16b: clears the register.
```
66 0FC8 bswap ax
=> ax = 0
```


### cmpxchg ###
if (op1 == `*`ax) op1 = op2 else `*`ax = op1
```
start: al = 3; bl = 6
cmpxchg bl,cl
=> al = bl
```

### cmpxchg8b ###
compare and exchange - memory based

```
start: eax:edx, ecx:ebx, [_cmpxchg8b] = 0a0a0a0a:d0d0d0d0, 99aabbcc:ddeeff00, 0d0d0d0d0:00a0a0a0a
cmpxchg8b [_cmpxchg8b]
=> [_cmpxchg8b] = ecx:ebx
```

source of the famous pentium bug

### cmpxchg16b ###
cmpxchg8b's 64b counterpart
```
start: rax:rdx, rcx:rbx, [_cmpxchg16b] = 0a0a0a0a0a0a0a0a:d0d0d0d0d0d0d0d0, 99aabbcc99aabbcc:ddeeff00ddeeff00, 0d0d0d0d00d0d0d0d0:00a0a0a0a00a0a0a0a
cmpxchg16b [_cmpxchg16b]
=> [_cmpxchg16b] = rcx:rbx
```

### rdtsc ###
=> edx:eax = timestamp counter.

#### anti-debug ####
```
rdtsc
mov ebx, eax
mov ecx, edx
rdtsc
cmp eax, ebx
jle bad
cmp edx, ecx
jnz bad
```

### popcnt ###
`<`target`>` =  number of bits set in `<`source`>`
```
start: ebx = 0100101010010b
popcnt eax, ebx
=> eax = 5
```
  * not supported by all cpus

### sidt ###
store idt
```
sidt [eax]
=> [eax] = 7fff (XP) / 0fffh (W7)
```

### crc32 ###
updates a crc32c on the operand
```
start: eax , [ebx] = 0, 12345678h
crc32 eax, [ebx]
=> eax, 0fa745634h
```
  * not the standard crc32
  * not supported by all cpus
### sgdt ###
store gdt
```
sgdt [eax]
=> [eax] = 3fff (XP) / (7fh)
```

#### detector ####

### sldt ###
store ldt

```
sldt eax
=> eax = 0
```

always 0 under standard OS, not under virtual machines.

### cpuid ###
get cpu info (brand, features, ...)

### lsl ###
get segment limit
```
start: cx = cs
lsl eax, ecx
=> eax= -1
```

### str ###
store task register
```
str ax
=> ax = 28 (XP) / 4000 (vmware)
```

special encoding. 16b or 32b bit on register, 16b on mem

### arpl ###
compares lower 2 bits and copy if inferior
```
start: ax, bx = 1100b, 11b
arpl ax,bx
=> ax = 1111b
```

### lar ###
check descriptor and get some parameter if existing
```
start: cx = cs
lar$ eax, ecx
=> eax = cff300
```

### ver`*` ###
check segment accessibility (and readability or writability)
```
start: cx = cs
verr cx
=> cf = 1
```

### sysenter ###
gateway to kernel
```
start: eax, [esp], edx = 0, @return, esp
sysenter
=> eip = return;...
```

### setCC ###
operand = condition ? 1 : 0
```
start: CF = 1
setc al
=> al = 1
```

### setalc ###
al = cf ? -1 : 0
```
cf = 1
setalc
=> al = ff
```

## OS specifics ##

## exception triggers ##
### int3 ###
trigger interrupt 3, like int 3

special one byte encoding, for software breakpoint implementation

### hlt ###
stops cpu. usually used to trigger PRIVILEGED\_INSTRUCTION

### IceBp ###
triggers SINGLE\_STEP exception

### ud1-ud2 ###
invalid opcodes. used as exceptions triggers for ILLEGAL\_INSTRUCTION

### lock ###
preserve memory content. picky prefix, mostly used to trigger exceptions