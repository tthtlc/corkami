[CoST (Corkami Standard Test)](http://code.google.com/p/corkami/downloads/list?can=1&q=cost) is a program that tests opcodes, [x86 oddities](x86oddities.md), in a weird PE file.

You can use it to test your tools or your skills.

It helped to find bugs in many disassemblers and PE tools (all that I tested):
  * OllyDbg, WinDbg, IDA, XED, BeaEngine, libdasm, HT, Hiew, diStorm, Metasm, pefile, and other private projects...

**WARNING** The whole binary, including the PE structure, is hand-made in assembly, so your favorite security program might trigger a false positive warning.

# PE features #
  * PE footer (header at the end of the file)
  * NULL Entrypoint
  * no section
  * invalid values everywhere, and randomized at each build as much as possible
  * TLS
    * non standard structure
    * entry added on the fly
    * patch via AddressOfIndex
    * non returning code

# ASM features #
  * execution of 64b in 32b process
  * allocation of the null page.

# Download #
see the [downloads](http://code.google.com/p/corkami/downloads/list?can=1&q=cost) or the **[latest nightly build](http://xchg.info/corkami/latest/cost.rar)**

# Details #

jumps opcodes:
  * call to word
  * jump:
    * short, near, to word, to reg32, to reg16, far
  * return:
    * near, near word, far, interrupt

classic opcodes:
  * mov movzx movsx lea xchg add sub sbb adc inc dec or and xor
  * not neg rol ror rcl rcr shl shr shld shrd div mul imul enter leave
  * setXX cmovXX bsf bsr bt btr btc bswap cbw cwde cwd

rare opcodes:
  * xadd aaa daa aas das aad aam lds bound arpl inc jcxz xlatb (on ebx and bx) lar
  * verr cmpxchg cmpxchg8b sldt lsl

undocumented opcodes:
  * aam xx, salc, aad xx, bswap reg16, smsw reg32

cpu specific opcodes:
  * popcnt movbe crc32 rdtscp

undocumented encodings:
  * test, 'sal'

os-dependant results:
  * smsw, sidt, sgdt, str, sysenter

nops-equivalent:
  * nop, pause, sfence, mfence, lfence, prefetch`*`, 'hint nop', into, fpu, lock + operators

anti-debuggers:
  * gs, smsw, rdtsc, pushf, pop ss, int 2A (XP)

Get EIP:
  * call, call far, fstenv

Exception triggers:
  * generic int 00-FF , int 2d, illegal instruction,
  * into, int4, int3, int 3, IceBP, TF, bound, lock, in, hlt

documented but frequent disassembly mistakes:
  * bswap, smsw, str, branch hints, word calls/rets/loops, FS:movsd, xlatb, ud

64 bits opcodes:
  * cwde, cmpxchg16, lea RIP, movsxd, 15 bit opcode

32+64b code:
  * the same hex code is executed automatically, once in 32b, then in 64b, producing different opcodes


# Use #
It has currently no options. Just run it from command line, all tests will run after each other. It sends more output to DbgPrint.

this is the output you could get under Windows 7, 64bit, i7
```
CoST - Corkami Standard Test BETA 2011/08/16
Ange Albertini, BSD Licence, 2009-2011 - http://corkami.com


Info: Windows 7 found
testing jumps opcodes...
testing classic opcodes...
testing rare opcodes...
testing undocumented opcodes...
testing cpu-specific opcodes...
Info: CPUID GenuineIntel
Info[cpu]: MOVBE (Atom only) not supported
testing undocumented encodings...
testing os-dependant opcodes...
testing 'nop' opcodes...
testing opcode-based anti-debuggers...
testing opcode-based GetIPs...
testing opcode-based exception triggers...
testing 64 bits opcodes...

...completed!
```
and under XP SP3, on a netbook:
```
CoST - Corkami Standard Test BETA 2011/08/16
Ange Albertini, BSD Licence, 2009-2011 - http://corkami.com


Info: Windows XP found
testing jumps opcodes...
testing classic opcodes...
testing rare opcodes...
testing undocumented opcodes...
testing cpu-specific opcodes...
Info: CPUID GenuineIntel
Info[cpu]: POPCNT not supported
Info[cpu]: CRC32 not supported
Info[cpu]: RDTSCP not supported
testing undocumented encodings...
testing os-dependant opcodes...
testing 'nop' opcodes...
testing opcode-based anti-debuggers...
testing opcode-based GetIPs...
testing opcode-based exception triggers...
testing 64 bits opcodes...
Info: 64 bits not supported

...completed!
```


Intermediate messages are displayed then removed, so if it crashes in the middle of the execution, you'll see instantly where.


# Building #
you need YASM, pefile, and python:
then just type
```
make
```

## EASY DEBUG ##
a optional #define is added, to build an executable easier to debug (less PE features, less randomization). Such a build will show it during execution.

# History #
  * 2011/04/01 - version 0.1
    * everything (first official release)
  * (in development)
    * more prefetchnta/multi-byte nop encoding
    * 64bit: CQO, lock add (15 bytes)
    * specifics: RDTSCP
    * encodings: SLDT, FS:XLATB
    * for reference: PCMPISTRM, 16b LOCK ADD


---

Acknowledgements:
  * BeatriX
  * Eugeny Suslikov
  * Gil Dabah
  * Igor Skochinsky
  * Moritz Kroll
  * Oleh Yuschuk
  * Peter Ferrie
  * Sebastian Biallas