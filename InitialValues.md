# Initial values #
this page gathers the initial values of x86 registers:
  * general registers, at specific execution points
  * special registers

Note: some values may change slightly, depending on your exact configuration and settings.

The values on this page have been dumped with [regdump](http://code.google.com/p/corkami/downloads/list?can=2&q=registers+dumper).



# Windows #
## NT 4 ##
  * OS Version
| Version | Platform | ServicePack | Suite | Product |
|:--------|:---------|:------------|:------|:--------|
| 4.0.1381 | 2 | 6.0 | 0000 | 1 |

  * selectors
| CS | DS | ES | FS | SS | GS |
|:---|:---|:---|:---|:---|:---|
| 1B | 23 | 23 | 3B | 23 | 0 |

  * system registers
| CR0 | LDT | GDT | IDT | Task Register |
|:----|:----|:----|:----|:--------------|
| 80010031| 00000000 | 8003600003FF | 8003640007FF | 00000028 |

  * general registers
| **execution point** | | Flags | | EDI | ESI | EBP | ESP | EBX | EDX | ECX | EAX |
|:--------------------|:|:------|:|:----|:----|:----|:----|:----|:----|:----|:----|
|  | | | | | | | | | | | |
| TLS |  | 0246 |  | 03EC1200 | 03EC0000 | 0012FAF8 | 0012FAB4 | 00132458 | 000011EC | 00000000 | 03EC103C |
| EntryPoint |  | 0216 |  | F9A70030 | 0012FBBC | 0012FFF0 | 0012FFC0 | 7FFDF000 | FFFFFFFF | 00000001 | 00000000 |


## Win2k ##
  * OS Version
| Version | Platform | ServicePack | Suite | Product |
|:--------|:---------|:------------|:------|:--------|
| 5.0.2195 | 2 | 4.0 | 0000 | 1 |

  * selectors
| CS | DS | ES | FS | SS | GS |
|:---|:---|:---|:---|:---|:---|
| 1B | 23 | 23 | 3B | 23 | 0 |

  * system registers
| CR0 | LDT | GDT | IDT | Task Register |
|:----|:----|:----|:----|:--------------|
| 80010031| 00000000 | 8003600003FF | 8003640007FF | 00000028 |

  * general registers
| **execution point** | | Flags | | EDI | ESI | EBP | ESP | EBX | EDX | ECX | EAX |
|:--------------------|:|:------|:|:----|:----|:----|:----|:----|:----|:----|:----|
|  | | | | | | | | | | | |
| TLS |  | 0246 |  | 03EC0000 | 0006F8AC | 0006F8B8 | 0006F898 | 00000000 | 000011EC | 0000E465 | 03EC103C |
| EntryPoint |  | 0246 |  | 7FFDE000 | 7FFDE000 | 0006FFF0 | 0006FFC0 | 7FFDF000 | FFFFFFFF | 00000101 | 00000000 |
| DLL EntryPoint |  | 0246 |  | 05EC1000 | 0006FBB8 | 0006FBC4 | 0006FBA4 | 00000000 | 000725D0 | 00000000 | 7FFDF000 |

## XP SP1 + VmWare ##

(Executable info: TLS callback RVA 03EC103C, TLS DD RVA:11EC Size: A3B1, ImageBase: 03EC0000)

  * OS Version
| Version | Platform | ServicePack | Suite | Product |
|:--------|:---------|:------------|:------|:--------|
| 5.1.2600 | 2 | 1.0 | 0100 | 1 |

  * selectors
| CS | DS | ES | FS | SS | GS |
|:---|:---|:---|:---|:---|:---|
| 1B | 23 | 23 | 3B | 23 | 0 |

  * system registers
| CR0 | LDT | GDT | IDT | Task Register |
|:----|:----|:----|:----|:--------------|
| 80010031| 00000000 | 8003F00003FF | 8003F40007FF | 00000028 |

  * general registers
| **execution point** | | Flags | EDI | ES | EBP | ESP | EBX | EDX | ECX | EAX |
|:--------------------|:|:------|:----|:---|:----|:----|:----|:----|:----|:----|
|  | | | | | | | | | | |
| TLS |  | 0246 | 03EC0000 | 0012F9E4 | 0012F9F0 | 0012F9D0 | 00000000 | 000011EC | 0000A3B1 | 03EC103C |
| EntryPoint |  | 0286 | 00000000 | 00000002 | 0012FFF0 | 0012FFC0 | 7FFDF000 | 7FFE0304 | 0012FFB0 | 00000000 |
| DLL EntryPoint |  | 0202 | 00000001 | 0012F8F8 | 0012F904 | 0012F8E4 | 05EC1000 | 00181EAC | 00000001 | 0012F958 |

### + OllyDbg ###
```
TLS:        EDI:00400000 ESI:0006F9E4 EBP:0006F9F0 ESP:0006F9D0 EBX:00000000 EDX:000010EC ECX:00000000 EAX:004010C8
EntryPoint  EDI:00000141 ESI:00000000 EBP:0006FFF0 ESP:0006FFC0 EBX:7FFDF000 EDX:7FFE0304 ECX:0006FFB0 EAX:00000000

CR0(after):80010031  CR0(before):8001003B
GDT:8003F00003FF IDT:8003F40007FF STR:00000028
```


## XP SP2 32b ##
```
TLS         EDI:00400000 ESI:0012F9C0 EBP:0012F9CC ESP:0012F9AC EBX:00000000 EDX:000010EC ECX:00000000 EAX:004010C8
EntryPoint  EDI:1EB10AA2 ESI:01CC55D3 EBP:0012FFF0 ESP:0012FFC0 EBX:7FFDB000 EDX:7C90E514 ECX:0012FFB0 EAX:00000000

CR0(after):80010031  CR0(before):80010031
GDT:BA34419003FF IDT:BA34459007FF STR:00000028
```

### + VmWare ###
```
TLS:        EDI:00400000 ESI:0012F9C0 EBP:0012F9CC ESP:0012F9AC EBX:00000000 EDX:000010EC ECX:00000000 EAX:004010C8
EntryPoint  EDI:40C85EAA ESI:01CC55CF EBP:0012FFF0 ESP:0012FFC0 EBX:7FFDF000 EDX:7C90EB94 ECX:0012FFB0 EAX:00000000

CR0(after):80010031  CR0(before):80010031
GDT:8003F00003FF IDT:8003F40007FF STR:00000028
```
## XP SP2 64b ##
### + VirtualBox ###
  * OS Version
| Version | Platform | ServicePack | Suite | Product |
|:--------|:---------|:------------|:------|:--------|
| 5.2.3790 | 2 | 2.0 | 0100 | 1 |

  * selectors
| CS | DS | ES | FS | SS | GS |
|:---|:---|:---|:---|:---|:---|
| 23 | 2B | 2B | 53 | 2B | 2B |

  * system registers
| CR0 | LDT | GDT | IDT | Task Register |
|:----|:----|:----|:----|:--------------|
| 80050031| 00000000 | 0020F000006F | 0020F0700FFF | 00000040 |

  * general registers
| **execution point** | | Flags | | EDI | ESI | EBP | ESP | EBX | EDX | ECX | EAX |
|:--------------------|:|:------|:|:----|:----|:----|:----|:----|:----|:----|:----|
|  | | | | | | | | | | | |
| TLS |  | 0246 |  | 03EC0000 | 0020F9EC | 0020F9F8 | 0020F9D8 | 00000000 | 03EC11EC | 0000E465 | 03EC103C |
| EntryPoint |  | 0246 |  | 00000000 | 00000000 | 0020FFF0 | 0020FFC0 | 7EFDF000 | 00000000 | ED2B0000 | 00000000 |
| DLL EntryPoint |  | 0202 |  | 00000001 | 0020F8EC | 0020F8F8 | 0020F8D8 | 05EC1000 | 00000000 | 0020F980 | 00000000 |

## XP SP3 32b ##
| Version | Platform | ServicePack | Suite | Product |
|:--------|:---------|:------------|:------|:--------|
| 5.1.2600 | 2 | 3.0 | 0100 | 1 |

  * selectors
| CS | DS | ES | FS | SS | GS |
|:---|:---|:---|:---|:---|:---|
| 1B | 23 | 23 | 3B | 23 | 0 |

  * system registers
| CR0 | LDT | GDT | IDT | Task Register |
|:----|:----|:----|:----|:--------------|
| 80010031| 00000000 | 8003F00003FF | 8003F40007FF | 00000028 |

  * general registers
| **execution point** | | Flags | | EDI | ESI | EBP | ESP | EBX | EDX | ECX | EAX |
|:--------------------|:|:------|:|:----|:----|:----|:----|:----|:----|:----|:----|
|  | | | | | | | | | | | |
| TLS |  | 0246 |  | 03EC0000 | 0012F9C0 | 0012F9CC | 0012F9AC | 00000000 | 000011EC | 0000E465 | 03EC103C |
| EntryPoint |  | 0246 |  | 0000000A | 0013F918 | 0012FFF0 | 0012FFC0 | 7FFD5000 | 7C90E514 | 0012FFB0 | 00000000 |
| DLL EntryPoint |  | 0202 |  | 00000001 | 0012F8B8 | 0012F8C4 | 0012F8A4 | 05EC1000 | 00000040 | FFFFFFFF | 0012F930 |

## XP SP3 Home 32b ##
(Executable info: TLS callback RVA 03EC103C, TLS DD RVA:11EC Size: E465, ImageBase: 03EC0000)
  * OS Version
| Version | Platform | ServicePack | Suite | Product |
|:--------|:---------|:------------|:------|:--------|
| 5.1.2600 | 2 | 3.0 | 0300 | 1 |

  * selectors
| CS | DS | ES | FS | SS | GS |
|:---|:---|:---|:---|:---|:---|
| 1B | 23 | 23 | 3B | 23 | 0 |

  * system registers
| CR0 | LDT | GDT | IDT | Task Register |
|:----|:----|:----|:----|:--------------|
| 80010031| 00000000 | 8003F00003FF | 8003F40007FF | 00000028 |

  * general registers
| **execution point** | | Flags | | EDI | ESI | EBP | ESP | EBX | EDX | ECX | EAX |
|:--------------------|:|:------|:|:----|:----|:----|:----|:----|:----|:----|:----|
|  | | | | | | | | | | | |
| TLS |  | 0246 |  | 03EC0000 | 0012F9C0 | 0012F9CC | 0012F9AC | 00000000 | 000011EC | 0000E465 | 03EC103C |
| EntryPoint |  | 0246 |  | 00000016 | 00000000 | 0012FFF0 | 0012FFC0 | 7FFD7000 | 7C90E514 | 0012FFB0 | 00000000 |
| DLL EntryPoint |  | 0202 |  | 00000001 | 0012F8B8 | 0012F8C4 | 0012F8A4 | 05EC1000 | 00000040 | FFFFFFFF | 0012F930 |

## 2003 [r2](https://code.google.com/p/corkami/source/detail?r=2) 64b ##
### + VirtualBox ###
  * OS Version
| Version | Platform | ServicePack | Suite | Product |
|:--------|:---------|:------------|:------|:--------|
| 5.2.3790 | 2 | 2.0 | 0112 | 3 |

  * selectors
| CS | DS | ES | FS | SS | GS |
|:---|:---|:---|:---|:---|:---|
| 23 | 2B | 2B | 53 | 2B | 2B |

  * system registers
| CR0 | LDT | GDT | IDT | Task Register |
|:----|:----|:----|:----|:--------------|
| 80050031| 00000000 | 001F9000006F | 001F90700FFF | 00000040 |

  * general registers
| **execution point** | | Flags | | EDI | ESI | EBP | ESP | EBX | EDX | ECX | EAX |
|:--------------------|:|:------|:|:----|:----|:----|:----|:----|:----|:----|:----|
|  | | | | | | | | | | | |
| TLS |  | 0246 |  | 03EC0000 | 0020F9EC | 0020F9F8 | 0020F9D8 | 00000000 | 03EC11EC | 0000E465 | 03EC103C |
| EntryPoint |  | 0246 |  | 00000000 | 00000000 | 0020FFF0 | 0020FFC0 | 7EFDF000 | 00000000 | F4F90000 | 00000000 |
| DLL EntryPoint |  | 0202 |  | 00000001 | 0020F8EC | 0020F8F8 | 0020F8D8 | 05EC1000 | 00000000 | 0020F980 | 00000000 |

## Vista SP1 32b (x64 cpu) ##
```
TLS         EDI:002E3DC8 ESI:000CF9F8 EBP:000CFA04 ESP:000CF9E4 EBX:004010C8 EDX:77F02080 ECX:00000011 EAX:00000000
EntryPoint  EDI:00000000 ESI:00000000 EBP:000CFF94 ESP:000CFF88 EBX:7EFDE000 EDX:00401010 ECX:00000000 EAX:76623665

CR0(after):80050033  CR0(before):80050033
GDT:B95000007F IDT:B950800FFF STR:00000040
```

## Vista SP2 32bit ##
(Executable info: TLS callback RVA 03EC103C, TLS DD RVA:11EC Size: E465, ImageBase: 03EC0000)
  * OS Version
| Version | Platform | ServicePack | Suite | Product |
|:--------|:---------|:------------|:------|:--------|
| 6.0.6002 | 2 | 2.0 | 0300 | 1 |

  * selectors
| CS | DS | ES | FS | SS | GS |
|:---|:---|:---|:---|:---|:---|
| 1B | 23 | 23 | 3B | 23 | 0 |

  * system registers
| CR0 | LDT | GDT | IDT | Task Register |
|:----|:----|:----|:----|:--------------|
| 80010031| 00000000 | 836DA00003FF | 836DA40007FF | 00000028 |

  * general registers
| **execution point** | | Flags | | EDI | ESI | EBP | ESP | EBX | EDX | ECX | EAX |
|:--------------------|:|:------|:|:----|:----|:----|:----|:----|:----|:----|:----|
|  | | | | | | | | | | | |
| TLS |  | 0246 |  | 03EC1200 | 0006F9FC | 0006FA08 | 0006F9E8 | 03EC103C | 778A53D8 | 001C2728 | 00000000 |
| EntryPoint |  | 0246 |  | 00000000 | 00000000 | 0006FF94 | 0006FF88 | 7FFDC000 | 03EC1000 | 00000000 | 7725D0D7 |
| DLL TLS |  | 0246 |  | 05EC1070 | 0006F8A0 | 0006F8AC | 0006F88C | 05EC102C | 778A53D8 | 001D9C10 | 00000000 |
| DLL EntryPoint |  | 0246 |  | 0006F998 | 0006F8DC | 0006F8E8 | 0006F8C8 | 00000001 | 00770BBC | 777EF751 | 0000006E |

## Vista SP2 64bit ##
### + VirtualBox ###
  * OS Version
| Version | Platform | ServicePack | Suite | Product |
|:--------|:---------|:------------|:------|:--------|
| 6.0.6002 | 2 | 2.0 | 0300 | 1 |

  * selectors
| CS | DS | ES | FS | SS | GS |
|:---|:---|:---|:---|:---|:---|
| 23 | 2B | 2B | 53 | 2B | 2B |

  * system registers
| CR0 | LDT | GDT | IDT | Task Register |
|:----|:----|:----|:----|:--------------|
| 80050031| 00000000 | 02AAD000006F | 02AAD0700FFF | 00000040 |

  * general registers
| **execution point** | | Flags | | EDI | ESI | EBP | ESP | EBX | EDX | ECX | EAX |
|:--------------------|:|:------|:|:----|:----|:----|:----|:----|:----|:----|:----|
|  | | | | | | | | | | | |
| TLS |  | 0246 |  | 03EC1200 | 000BF9DC | 000BF9E8 | 000BF9C8 | 03EC103C | 77BC0360 | 00512B68 | 00000000 |
| EntryPoint |  | 0246 |  | 00000000 | 00000000 | 000BFF94 | 000BFF88 | 7EFDE000 | 03EC1000 | 00000000 | 75DFECBD |
| DLL TLS |  | 0246 |  | 05EC1070 | 000BF8A0 | 000BF8AC | 000BF88C | 05EC102C | 77BC0360 | 005133F0 | 00000000 |
| DLL EntryPoint |  | 0246 |  | 000BF998 | 000BF8DC | 000BF8E8 | 000BF8C8 | 00000001 | 001527D5 | 77B1B455 | 0000006E |

## W7 32b ##
  * OS Version
| Version | Platform | ServicePack | Suite | Product |
|:--------|:---------|:------------|:------|:--------|
| 6.1.7601 | 2 | 1.0 | 0100 | 1 |

  * selectors
| CS | DS | ES | FS | SS | GS |
|:---|:---|:---|:---|:---|:---|
| 1B | 23 | 23 | 3B | 23 | 0 |

  * system registers
| CR0 | LDT | GDT | IDT | Task Register |
|:----|:----|:----|:----|:--------------|
| 8001003B (after FPU:80010031)| 00000000 | 80B9700003FF |
80B9740007FF | 00000028 |

  * general registers
| **execution point** | | Flags | | EDI | ESI | EBP | ESP | EBX | EDX | ECX | EAX |
|:--------------------|:|:------|:|:----|:----|:----|:----|:----|:----|:----|:----|
|  | | | | | | | | | | | |
| TLS |  | 0246 |  | 00241CD0 | 0006FA10 | 0006FA1C | 0006F9FC | 03EC103C | 76F4CD48 | 00000011 | 00000000 |
| EntryPoint |  | 0246 |  | 00000000 | 00000000 | 0006FF94 | 0006FF88 | 7FFD7000 | 03EC1000 | 00000000 | 75D9ED5A |
| DLL TLS |  | 0246 |  | 002430E8 | 0006FC28 | 0006FC34 | 0006FC14 | 05EC102C | 76F4CD48 | 00000011 | 00000000 |
| DLL EntryPoint |  | 0246 |  | 0006FD20 | 0006FC64 | 0006FC70 | 0006FC50 | 00000001 | 00020180 | 76E8F56A | 0000006E |
## 2008 64b ##
### + VirtualBox ###
  * OS Version
| Version | Platform | ServicePack | Suite | Product |
| 6.1.7601 | 2 | 1.0 | 0110 | 3 |

  * selectors
| CS | DS | ES | FS | SS | GS |
|:---|:---|:---|:---|:---|:---|
| 23 | 2B | 2B | 53 | 2B | 2B |

  * system registers
| CR0 | LDT | GDT | IDT | Task Register |
|:----|:----|:----|:----|:--------------|
| 80050031| 00000000 | 01513000007F | 015130800FFF | 00000040 |

  * general registers
| **execution point** | | Flags | | EDI | ESI | EBP | ESP | EBX | EDX | ECX | EAX |
|:--------------------|:|:------|:|:----|:----|:----|:----|:----|:----|:----|:----|
|  | | | | | | | | | | | |
| TLS |  | 0246 |  | 00292CC0 | 000CF9F4 | 000CFA00 | 000CF9E0 | 03EC103C | 77B42080 | 00000011 | 00000000 |
| EntryPoint |  | 0246 |  | 00000000 | 00000000 | 000CFF94 | 000CFF88 | 7EFDE000 | 03EC1000 | 00000000 | 75CC3388 |
| DLL TLS |  | 0246 |  | 00294088 | 000CFC24 | 000CFC30 | 000CFC10 | 05EC102C | 77B42080 | 00000011 | 00000000 |
| DLL EntryPoint |  | 0246 |  | 000CFD1C | 000CFC60 | 000CFC6C | 000CFC4C | 00000001 | 00282B35 | 77AA03A3 | 0000006E |

## W7 SP0 64b ##
(Executable info: TLS callback RVA 03EC103C, TLS DD RVA:11EC Size: E465, ImageBase: 03EC0000)
  * OS Version
| Version | Platform | ServicePack | Suite | Product |
|:--------|:---------|:------------|:------|:--------|
| 6.1.7600 | 2 | 0.0 | 0300 | 1 |

  * selectors
| CS | DS | ES | FS | SS | GS |
|:---|:---|:---|:---|:---|:---|
| 23 | 2B | 2B | 53 | 2B | 2B |

  * system registers
| CR0 | LDT | GDT | IDT | Task Register |
|:----|:----|:----|:----|:--------------|
| 80050033| 00000000 | 00B95000007F | 00B950800FFF | 00000040 |

  * general registers
| **execution point** | | Flags | | EDI | ESI | EBP | ESP | EBX | EDX | ECX | EAX |
|:--------------------|:|:------|:|:----|:----|:----|:----|:----|:----|:----|:----|
|  | | | | | | | | | | | |
| TLS |  | 0246 |  | 002A3DC8 | 000CF9F8 | 000CFA04 | 000CF9E4 | 03EC103C | 77552080 | 00000011 | 00000000 |
| EntryPoint |  | 0246 |  | 00000000 | 00000000 | 000CFF94 | 000CFF88 | 7EFDE000 | 03EC1000 | 00000000 | 753B3665 |
| DLL TLS |  | 0246 |  | 002A7F10 | 000CFC24 | 000CFC30 | 000CFC10 | 05EC102C | 77552080 | 00000011 | 00000000 |
| DLL EntryPoint |  | 0246 |  | 000CFD1C | 000CFC60 | 000CFC6C | 000CFC4C | 00000001 | 00492905 | 7749E9C3 | 0000006E |

## W7 SP1 64b ##
(Executable info: TLS callback RVA 03EC103C, TLS DD RVA:11EC Size: E465, ImageBase: 03EC0000)
  * OS Version
| Version | Platform | ServicePack | Suite | Product |
|:--------|:---------|:------------|:------|:--------|
| 6.1.7601 | 2 | 1.0 | 0100 | 1 |

  * selectors
| CS | DS | ES | FS | SS | GS |
|:---|:---|:---|:---|:---|:---|
| 23 | 2B | 2B | 53 | 2B | 2B |

  * system registers
| CR0 | LDT | GDT | IDT | Task Register |
|:----|:----|:----|:----|:--------------|
| 80050031| 00000000 | 0316F640007F | 0316F6C00FFF | 00000040 |
| 80050031| 00000000 | 04060000007F | 040600800FFF | 00000040 |

  * general registers
| **execution point** | | Flags | | EDI | ESI | EBP | ESP | EBX | EDX | ECX | EAX |
|:--------------------|:|:------|:|:----|:----|:----|:----|:----|:----|:----|:----|
|  | | | | | | | | | | | |
| TLS |  | 0246 |  | 003329C0 | 000CF9F4 | 000CFA00 | 000CF9E0 | 03EC103C | 77C02080 | 00000011 | 00000000 |
| TLS |  | 0246 |  | 00413C30 | 000CF9F4 | 000CFA00 | 000CF9E0 | 03EC103C | 77F12080 | 00000011 | 00000000 |
| EntryPoint |  | 0246 |  | 00000000 | 00000000 | 000CFF94 | 000CFF88 | 7EFDE000 | 03EC1000 | 00000000 | 76F83388 |
| EntryPoint |  | 0246 |  | 00000000 | 00000000 | 000CFF94 | 000CFF88 | 7EFDE000 | 03EC1000 | 00000000 | 77093388 |
| DLL TLS |  | 0246 |  | 00333D80 | 000CFC24 | 000CFC30 | 000CFC10 | 05EC102C | 77C02080 | 00000011 | 00000000 |
| DLL TLS |  | 0246 |  | 00414FF0 | 000CFC24 | 000CFC30 | 000CFC10 | 02EC1034 | 77F12080 | 00000011 | 00000000 |
| DLL EntryPoint |  | 0246 |  | 000CFD1C | 000CFC60 | 000CFC6C | 000CFC4C | 00000001 | 0008E3C8 | 77B61BCB | 0000006E |
| DLL EntryPoint |  | 0246 |  | 000CFD1C | 000CFC60 | 000CFC6C | 000CFC4C | 00000001 | 00000000 | 77E71BCB | 00000000 |

### +OllyDbg ###
```
TLS:        EDI:001C3A48 ESI:000CF9F4 EBP:000CFA00 ESP:000CF9E0 EBX:004010C8 EDX:778B2080 ECX:00000011 EAX:00000000
EntryPoint  EDI:00000000 ESI:00000000 EBP:000CFF94 ESP:000CFF88 EBX:7EFDE000 EDX:00401010 ECX:00000000 EAX:75293388

CR0(after):80050031  CR0(before):80050031
GDT:316F640007F IDT:316F6C00FFF STR:00000040
```

### +WinDbg ###
```
TLS:        EDI:00273940 ESI:000CF9F4 EBP:000CFA00 ESP:000CF9E0 EBX:004010C8 EDX:778B2080 ECX:00000011 EAX:00000000
EntryPoint  EDI:00000000 ESI:00000000 EBP:000CFF94 ESP:000CFF88 EBX:7EFDE000 EDX:00401010 ECX:00000000 EAX:75293388

CR0(after):80050031  CR0(before):80050031
GDT:9BD640007F IDT:9BD6C00FFF STR:00000040
```
## Windows 8 preview 32b ##
### + VirtualBox ###
  * OS Version
| Version | Platform | ServicePack | Suite | Product |
|:--------|:---------|:------------|:------|:--------|
| 6.2.8400 | 2 | 0.0 | 0100 | 1 |

  * selectors
| CS | DS | ES | FS | SS | GS |
|:---|:---|:---|:---|:---|:---|
| 1B | 23 | 23 | 3B | 23 | 0 |

  * system registers
| CR0 | LDT | GDT | IDT | Task Register |
|:----|:----|:----|:----|:--------------|
| 80010031| 00000000 | 80ADF00003FF | 80ADF40007FF | 00000028 |

  * general registers
| **execution point** | | Flags | | EDI | ESI | EBP | ESP | EBX | EDX | ECX | EAX |
|:--------------------|:|:------|:|:----|:----|:----|:----|:----|:----|:----|:----|
|  | | | | | | | | | | | |
| TLS |  | 0246 |  | 03EC0000 | 0007FA7C | 0007FA88 | 0007FA68 | 03EC103C | 00000000 | 00000011 | 0007FAC0 |
| EntryPoint |  | 0246 |  | 00000000 | 00000000 | 0007FF98 | 0007FF8C | 7F634000 | 03EC1000 | 00000000 | 75131797 |
| DLL TLS |  | 0246 |  | 05EC0000 | 0007FB54 | 0007FB60 | 0007FB40 | 05EC102C | 00000000 | 00000011 | 0007FB98 |
| DLL EntryPoint |  | 0246 |  | 05EC0000 | 0007FB8C | 0007FB98 | 0007FB78 | 000DA098 | 0000006E | 05EC1000 | 0007FBD0 |

## Windows 8 preview 64b ##
### + VirtualBox ###
  * OS Version
| Version | Platform | ServicePack | Suite | Product |
|:--------|:---------|:------------|:------|:--------|
| 6.2.8400 | 2 | 0.0 | 0100 | 1 |

  * selectors
| CS | DS | ES | FS | SS | GS |
|:---|:---|:---|:---|:---|:---|
| 23 | 2B | 2B | 53 | 2B | 2B |

  * system registers
| CR0 | LDT | GDT | IDT | Task Register |
|:----|:----|:----|:----|:--------------|
| 80050031| 00000000 | AD8FB000007F | AD8FB0800FFF | 00000040 |

  * general registers
| **execution point** | | Flags | | EDI | ESI | EBP | ESP | EBX | EDX | ECX | EAX |
|:--------------------|:|:------|:|:----|:----|:----|:----|:----|:----|:----|:----|
|  | | | | | | | | | | | |
| TLS |  | 0246 |  | 03EC103C | 000CFA4C | 000CFA58 | 000CFA38 | 002F2DE8 | 770C9120 | 00000011 | 000CFA90 |
| EntryPoint |  | 0246 |  | 00000000 | 00000000 | 000CFF90 | 000CFF84 | 7EF0C000 | 03EC1000 | 00000000 | 762483CD |
| DLL TLS |  | 0246 |  | 05EC102C | 000CFBA4 | 000CFBB0 | 000CFB90 | 002FAC80 | 770C9120 | 00000011 | 000CFBE8 |
| DLL EntryPoint |  | 0246 |  | 000CFC84 | 000CFBE4 | 000CFBF0 | 000CFBD0 | 00000000 | 0000006E | 76FF93FA | 000CFC28 |

## Windows Server 2012 RC 64b ##
### + VirtualBox ? ###
  * OS Version
| Version | Platform | ServicePack | Suite | Product |
|:--------|:---------|:------------|:------|:--------|
| 6.2.8400 | 2 | 0.0 | 0131 | 3 |

  * selectors
| CS | DS | ES | FS | SS | GS |
|:---|:---|:---|:---|:---|:---|
| 23 | 2B | 2B | 53 | 2B | 2B |

  * system registers
| CR0 | LDT | GDT | IDT | Task Register |
|:----|:----|:----|:----|:--------------|
| 80050031| 00000000 | 009C4640007F | 009C46C00FFF | 00000040 |

  * general registers
| **execution point** | | Flags | | EDI | ESI | EBP | ESP | EBX | EDX | ECX | EAX |
|:--------------------|:|:------|:|:----|:----|:----|:----|:----|:----|:----|:----|
|  | | | | | | | | | | | |
| TLS |  | 0246 |  | 03EC103C | 000CFA4C | 000CFA58 | 000CFA38 | 002322C0 | 77DE9120 | 00000011 | 000CFA90 |
| EntryPoint |  | 0246 |  | 00000000 | 00000000 | 000CFF90 | 000CFF84 | 7F89E000 | 03EC1000 | 00000000 | 762383CD |
| DLL TLS |  | 0246 |  | 05EC102C | 000CFBA4 | 000CFBB0 | 000CFB90 | 0023AFC8 | 77DE9120 | 00000011 | 000CFBE8 |
| DLL EntryPoint |  | 0246 |  | 000CFC84 | 000CFBE4 | 000CFBF0 | 000CFBD0 | 00000000 | 0000006E | 77D193FA | 000CFC28 |

## Windows Server 2012 64b ##
### + VirtualBox ###
  * OS Version
| Version | Platform | ServicePack | Suite | Product |
|:--------|:---------|:------------|:------|:--------|
| 6.2.8400 | 2 | 0.0 | 0190 | 3 |

  * selectors
| CS | DS | ES | FS | SS | GS |
|:---|:---|:---|:---|:---|:---|
| 23 | 2B | 2B | 53 | 2B | 2B |

  * system registers
| CR0 | LDT | GDT | IDT | Task Register |
|:----|:----|:----|:----|:--------------|
| 80050031| 00000000 | 825E9000007F | 825E90800FFF | 00000040 |

  * general registers
| **execution point** | | Flags | | EDI | ESI | EBP | ESP | EBX | EDX | ECX | EAX |
|:--------------------|:|:------|:|:----|:----|:----|:----|:----|:----|:----|:----|
|  | | | | | | | | | | | |
| TLS |  | 0246 |  | 03EC103C | 000CFA4C | 000CFA58 | 000CFA38 | 001B21F8 | 776A9120 | 00000011 | 000CFA90 |
| EntryPoint |  | 0246 |  | 00000000 | 00000000 | 000CFF90 | 000CFF84 | 7F024000 | 03EC1000 | 00000000 | 772D83CD |
| DLL TLS |  | 0246 |  | 05EC102C | 000CFBA4 | 000CFBB0 | 000CFB90 | 001B46E8 | 776A9120 | 00000011 | 000CFBE8 |
| DLL EntryPoint |  | 0246 |  | 000CFC84 | 000CFBE4 | 000CFBF0 | 000CFBD0 | 00000000 | 0000006E | 775D93FA | 000CFC28 |

## Windows 8 RTM ##
### + VirtualBox ? ###
  * OS Version
| Version | Platform | ServicePack | Suite | Product |
|:--------|:---------|:------------|:------|:--------|
| 6.2.9200 | 2 | 0.0 | 0100 | 1 |

  * selectors
| CS | DS | ES | FS | SS | GS |
|:---|:---|:---|:---|:---|:---|
| 23 | 2B | 2B | 53 | 2B | 2B |

  * system registers
| CR0 | LDT | GDT | IDT | Task Register |
|:----|:----|:----|:----|:--------------|
| 80050031| 00000000 | 009C4600007F | 009C46800FFF | 00000040 |

  * general registers
| **execution point** | | Flags | | EDI | ESI | EBP | ESP | EBX | EDX | ECX | EAX |
|:--------------------|:|:------|:|:----|:----|:----|:----|:----|:----|:----|:----|
|  | | | | | | | | | | | |
| TLS |  | 0246 |  | 004F1518 | 000CFA54 | 000CFA60 | 000CFA40 | 03EC103C | 77ECA120 | 00000011 | 00000000 |
| EntryPoint |  | 0246 |  | 00000000 | 00000000 | 000CFF90 | 000CFF84 | 7FFDE000 | 03EC1000 | 00000000 | 77598535 |
| DLL TLS |  | 0246 |  | 00500C98 | 000CFBB4 | 000CFBC0 | 000CFBA0 | 05EC102C | 77ECA120 | 00000011 | 00000000 |
| DLL EntryPoint |  | 0246 |  | 000CFC8C | 000CFBEC | 000CFBF8 | 000CFBD8 | 00000000 | 0000006E | 77E00780 | 00000000 |

# Common #
(under construction)

Slide from [content BerlinSide's presentation](http://code.google.com/p/corkami/wiki/BerlinSidesX2?show=)
![https://corkami.googlecode.com/svn/wiki/pics/bsx2/img40.png](https://corkami.googlecode.com/svn/wiki/pics/bsx2/img40.png)

PoC `initvals.exe` downloadable [here](http://xchg.info/corkami/latest/asm.rar)
## Flags ##
always 246 (except NT4 EP), on EntryPoint or TLS.

## Selectors ##
Standard for 32b:
| CS | DS | ES | FS | SS | GS |
|:---|:---|:---|:---|:---|:---|
| 001B | 0023 | 0023 | 003B | 0023 |0000 |

Exception: FS is `38` under NT4/2k/XPSP1, but `3B` on any XP sp>=2/Vista/7

for 64b (CPU, not OS => even on 32b os on 64b cpu):
| CS | DS | ES | FS | SS | GS |
|:---|:---|:---|:---|:---|:---|
|23|2B|2B|53|2B|2B|

## Registers ##
  * at Entrypoint:
    * XP or older ? EAX = 0
    * Vista or more recent ? EAX = 7xxxxxxxxh

  * TLS
    * ECX = `DataDirectory[TLS].size`

  * STR
    * CPU = 64b ? 40h
    * CPU = 32b ? 28h

# Wine #
tested under Debian

Detect Wine:
  * GS (3B/6B)
  * FS (33/63)
  * CR0 (80050033)
  * ...

## Windows XP ##
### 32b ###
  * OS Version
| Version | Platform | ServicePack | Suite | Product |
|:--------|:---------|:------------|:------|:--------|
| 5.1.2600 | 2 | 3.0 | 0000 | 1 |

  * selectors
| CS | DS | ES | FS | SS | GS |
|:---|:---|:---|:---|:---|:---|
| 73 | 7B | 7B | 33 | 7B | 3B |

  * system registers
| CR0 | LDT | GDT | IDT | Task Register |
|:----|:----|:----|:----|:--------------|
| 80050033| 00000000 | F248200000FF | C153300007FF | 00000080 |

  * general registers
| **execution point** | | Flags | | EDI | ESI | EBP | ESP | EBX | EDX | ECX | EAX |
|:--------------------|:|:------|:|:----|:----|:----|:----|:----|:----|:----|:----|
|  | | | | | | | | | | | |
| TLS |  | 0246 |  | 00000000 | 00110678 | 0032FDF8 | 0032FCF8 | 7BCA6FF4 | 03EC0000 | 03EC11FC | 00000001 |
| EntryPoint |  | 0212 |  | 03EC1000 | 7FFDF000 | 0032FE88 | 0032FE70 | 7B894FF4 | 0032FEF0 | 0032FEF0 | 00000000 |

### 64b ###
  * OS Version
| Version | Platform | ServicePack | Suite | Product |
|:--------|:---------|:------------|:------|:--------|
| 5.1.2600 | 2 | 2.0 | 0000 | 1 |

  * selectors
| CS | DS | ES | FS | SS | GS |
|:---|:---|:---|:---|:---|:---|
| 23 | 2B | 2B | 63 | 2B | 6B |

  * system registers
| CR0 | LDT | GDT | IDT | Task Register |
|:----|:----|:----|:----|:--------------|
| 80050033| 00000050 | 2FC04000007F | 817240000FFF | 00000040 |

  * general registers
| **execution point** | | Flags | | EDI | ESI | EBP | ESP | EBX | EDX | ECX | EAX |
|:--------------------|:|:------|:|:----|:----|:----|:----|:----|:----|:----|:----|
|  | | | | | | | | | | | |
| TLS |  | 0246 |  | 00000000 | 00110960 | 0033FDE8 | 0033FCD8 | 7BC89444 | 03EC0000 | 03EC11FC | 00000001 |
| EntryPoint |  | 0246 |  | 03EC1000 | 7FFDF000 | 0033FFE8 | 0033FF08 | 7B8B4884 | 0033FF20 | 853B72CE | 00000000 |

## Windows Vista ##
### 32b ###
  * OS Version
| Version | Platform | ServicePack | Suite | Product |
|:--------|:---------|:------------|:------|:--------|
| 6.0.6002 | 2 | 2.0 | 0000 | 1 |

  * selectors
| CS | DS | ES | FS | SS | GS |
|:---|:---|:---|:---|:---|:---|
| 73 | 7B | 7B | 33 | 7B | 3B |

  * system registers
| CR0 | LDT | GDT | IDT | Task Register |
|:----|:----|:----|:----|:--------------|
| 80050033| 00000000 | F248200000FF | C153300007FF | 00000080 |

  * general registers
| **execution point** | | Flags | | EDI | ESI | EBP | ESP | EBX | EDX | ECX | EAX |
|:--------------------|:|:------|:|:----|:----|:----|:----|:----|:----|:----|:----|
|  | | | | | | | | | | | |
| TLS |  | 0246 |  | 00000000 | 00110678 | 0032FDF8 | 0032FCF8 | 7BCA6FF4 | 03EC0000 | 03EC11FC | 00000001 |
| EntryPoint |  | 0212 |  | 03EC1000 | 7FFDF000 | 0032FE88 | 0032FE70 | 7B894FF4 | 0032FEF0 | 0032FEF0 | 00000000 |

### 64b ###
  * OS Version
| Version | Platform | ServicePack | Suite | Product |
|:--------|:---------|:------------|:------|:--------|
| 6.0.6001 | 2 | 0.0 | 0000 | 3 |

  * selectors
| CS | DS | ES | FS | SS | GS |
|:---|:---|:---|:---|:---|:---|
| 23 | 2B | 2B | 63 | 2B | 6B |

  * system registers
| CR0 | LDT | GDT | IDT | Task Register |
|:----|:----|:----|:----|:--------------|
| 80050033| 00000050 | 2FC04000007F | 817240000FFF | 00000040 |

  * general registers
| **execution point** | | Flags | | EDI | ESI | EBP | ESP | EBX | EDX | ECX | EAX |
|:--------------------|:|:------|:|:----|:----|:----|:----|:----|:----|:----|:----|
|  | | | | | | | | | | | |
| TLS |  | 0246 |  | 00000000 | 00110960 | 0033FDE8 | 0033FCD8 | 7BC89444 | 03EC0000 | 03EC11FC | 00000001 |
| EntryPoint |  | 0246 |  | 03EC1000 | 7FFDF000 | 0033FFE8 | 0033FF08 | 7B8B4884 | 0033FF20 | 7B013D8C | 00000000 |

## Windows 7 ##
### 32b ###
  * OS Version
| Version | Platform | ServicePack | Suite | Product |
|:--------|:---------|:------------|:------|:--------|
| 6.1.7601 | 2 | 1.0 | 0000 | 1 |

  * selectors
| CS | DS | ES | FS | SS | GS |
|:---|:---|:---|:---|:---|:---|
| 73 | 7B | 7B | 33 | 7B | 3B |

  * system registers
| CR0 | LDT | GDT | IDT | Task Register |
|:----|:----|:----|:----|:--------------|
| 80050033| 00000000 | F248200000FF | C153300007FF | 00000080 |

  * general registers
| **execution point** | | Flags | | EDI | ESI | EBP | ESP | EBX | EDX | ECX | EAX |
|:--------------------|:|:------|:|:----|:----|:----|:----|:----|:----|:----|:----|
|  | | | | | | | | | | | |
| TLS |  | 0246 |  | 00000000 | 00110678 | 0032FDF8 | 0032FCF8 | 7BCA6FF4 | 03EC0000 | 03EC11FC | 00000001 |
| EntryPoint |  | 0212 |  | 03EC1000 | 7FFDF000 | 0032FE88 | 0032FE70 | 7B894FF4 | 0032FEF0 | 0032FEF0 | 00000000 |

### 64b ###
  * OS Version
| Version | Platform | ServicePack | Suite | Product |
|:--------|:---------|:------------|:------|:--------|
| 6.1.7601 | 2 | 1.0 | 0000 | 1 |

  * selectors
| CS | DS | ES | FS | SS | GS |
|:---|:---|:---|:---|:---|:---|
| 23 | 2B | 2B | 63 | 2B | 6B |

  * system registers
| CR0 | LDT | GDT | IDT | Task Register |
|:----|:----|:----|:----|:--------------|
| 80050033| 00000000 | 2FC04000007F | 817240000FFF | 00000040 |

  * general registers
| **execution point** | | Flags | | EDI | ESI | EBP | ESP | EBX | EDX | ECX | EAX |
|:--------------------|:|:------|:|:----|:----|:----|:----|:----|:----|:----|:----|
|  | | | | | | | | | | | |
| TLS |  | 0246 |  | 00000000 | 00110678 | 0033FDE8 | 0033FCD8 | 7BCA87EC | 03EC0000 | 03EC11FC | 00000001 |
| EntryPoint |  | 0212 |  | 03EC1000 | 7FFDF000 | 0033FE88 | 0033FE70 | 7B89CA50 | 0033FEF0 | 0033FEF0 | 00000000 |

# DOS #
## XP ##
  * general registers
| Flags | DI | SI | BP | SP | BX | DX | CX | AX |
|:------|:---|:---|:---|:---|:---|:---|:---|:---|
| 3202 | fffe | 0100 | 091e | fffe | 0000 | 06bc | 00ff | 0000 |

  * selectors
| CS | DS | ES | FS | SS | GS |
|:---|:---|:---|:---|:---|:---|
| 06bc | 06bc | 06bc | 0000 | 06bc | 0000 |

### + Debug ###
  * general registers
| Flags | DI | SI | BP | SP | BX | DX | CX | AX |
|:------|:---|:---|:---|:---|:---|:---|:---|:---|
| 7202 | 0000 | 0000 | 0000 | fffe | 0000 | 0000 | 01d6 | 0000 |

  * selectors
| CS | DS | ES | FS | SS | GS |
|:---|:---|:---|:---|:---|:---|
| 0cb5 | 0cb5 | 0cb5 | 0000 | 0cb5 | 0000 |

## DOSBox 0.74 ##
  * general registers
| Flags | DI | SI | BP | SP | BX | DX | CX | AX |
|:------|:---|:---|:---|:---|:---|:---|:---|:---|
| 7202 | fffe | 0100| 091c| fffe | 0000|0192  | 00ff | 0000 |

  * selectors
| CS | DS | ES | FS | SS | GS |
|:---|:---|:---|:---|:---|:---|
| 0192 | 0192 | 0192 | 0000 | 0192 | 0000 |

# Linux #
## Ubuntu + VmWare ##
  * general registers
| Flags | EDI | ESI | EBP | ESP | EBX | EDX | ECX | EAX |
|:------|:----|:----|:----|:----|:----|:----|:----|:----|
| 0212 | 00000000 | 00000000 | 00000000 | bff19160 | 00000000 | 00000000 | 00000000 | 00000000 |

  * selectors
| CS | DS | ES | FS | SS | GS |
|:---|:---|:---|:---|:---|:---|
| 0073 | 007b | 007b | 0000 | 007b | 0000 |

  * system registers
| CR0 | LDT | GDT | IDT | Task Register |
|:----|:----|:----|:----|:--------------|
| 8005003b | 00004060 | ffc07000412f | ffc1800007ff | 00004000 |

# Acknowledgements #
  * **Fabian Sauter**

  * Adam Blaszczyk
  * Markus Hinderhofer
  * Moritz Kroll
  * Peter Ferrie