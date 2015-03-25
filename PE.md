[<< index](http://code.google.com/p/corkami/) [Android/Java/x86/... opcodes tables](http://opcodes.corkami.com) [PDF tricks](http://pdf.corkami.com) [Portable Executable](http://pe.corkami.com) [x86 oddities](http://x86.corkami.com)

# PE #
This page deals with the PE format, or more specifically, x86/x64 Windows (from XP to W7) binaries (ie, not other OSes or systems, not OBJ format, etc...)

if you're not familiar with this format, check [PE 101 - a Windows executable walkthrough](http://pe101.corkami.com).

It deals with the **reality** of the loader, not the theory of the format itself, as specified in _Microsoft PE and COFF Specification_. Many malwares or packers actually run without a problem, while they theoretically shouldn't.

All proof of concepts are included with source:
  * They're all working and clean.
  * They're all generated in asssembly, by hand, from scratch, so no superfluous information is included (they might be wrongly detected as suspicious for that).
  * links
    * this page: [printable version](http://code.google.com/p/corkami/wiki/PE?show=content) [wiki source](http://corkami.googlecode.com/svn/wiki/PE.wiki)
    * sources: [repository directory](https://corkami.googlecode.com/svn/trunk/src/PE)
    * binaries with sources: [official builds](http://code.google.com/p/corkami/downloads/list?q=CPC) **[nightly (latest) builds](https://www.dropbox.com/sh/kojbmbb6hm64mcg/suVxNVV0XY)**


# required elements #
a standard (high aligments, sections, imports) PE such as `normal` can be defined with only the following structure elements:
```
    at IMAGE_DOS_HEADER.e_magic, db 'MZ'
    at IMAGE_DOS_HEADER.e_lfanew, dd NT_Signature - IMAGEBASE
...
    at IMAGE_NT_HEADERS.Signature, db 'PE', 0, 0
...
    at IMAGE_FILE_HEADER.Machine,               dw IMAGE_FILE_MACHINE_I386
    at IMAGE_FILE_HEADER.NumberOfSections,      dw NUMBEROFSECTIONS
    at IMAGE_FILE_HEADER.SizeOfOptionalHeader,  dw SIZEOFOPTIONALHEADER
    at IMAGE_FILE_HEADER.Characteristics,       dw IMAGE_FILE_EXECUTABLE_IMAGE
...
    istruc IMAGE_OPTIONAL_HEADER32
    at IMAGE_OPTIONAL_HEADER32.Magic,                     dw IMAGE_NT_OPTIONAL_HDR32_MAGIC
    at IMAGE_OPTIONAL_HEADER32.AddressOfEntryPoint,       dd EntryPoint - IMAGEBASE
    at IMAGE_OPTIONAL_HEADER32.ImageBase,                 dd IMAGEBASE
    at IMAGE_OPTIONAL_HEADER32.SectionAlignment,          dd SECTIONALIGN
    at IMAGE_OPTIONAL_HEADER32.FileAlignment,             dd FILEALIGN
    at IMAGE_OPTIONAL_HEADER32.MajorSubsystemVersion,     dw 4
    at IMAGE_OPTIONAL_HEADER32.SizeOfImage,               dd 2 * SECTIONALIGN
    at IMAGE_OPTIONAL_HEADER32.SizeOfHeaders,             dd SIZEOFHEADERS
    at IMAGE_OPTIONAL_HEADER32.Subsystem,                 dw IMAGE_SUBSYSTEM_WINDOWS_CUI
    at IMAGE_OPTIONAL_HEADER32.NumberOfRvaAndSizes,       dd 16
...
    at IMAGE_DATA_DIRECTORY_16.ImportsVA,   dd Import_Descriptor - IMAGEBASE
...
    at IMAGE_SECTION_HEADER.VirtualSize,      dd 1 * SECTIONALIGN
    at IMAGE_SECTION_HEADER.VirtualAddress,   dd 1 * SECTIONALIGN
    at IMAGE_SECTION_HEADER.SizeOfRawData,    dd 1 * FILEALIGN
    at IMAGE_SECTION_HEADER.PointerToRawData, dd 1 * FILEALIGN
    at IMAGE_SECTION_HEADER.Characteristics,  dd IMAGE_SCN_MEM_EXECUTE | IMAGE_SCN_MEM_WRITE
```
so everything else is not required.

a more extreme PE can rely on even less elements:
  * `mini` is a working PE with the minimum amount of defined elements.
```
    at IMAGE_NT_HEADERS.Signature, db 'PE', 0, 0
...
    at IMAGE_FILE_HEADER.Machine,         dw IMAGE_FILE_MACHINE_I386
    at IMAGE_FILE_HEADER.Characteristics, dw IMAGE_FILE_EXECUTABLE_IMAGE | IMAGE_FILE_32BIT_MACHINE
...
    at IMAGE_OPTIONAL_HEADER32.Magic,                 dw IMAGE_NT_OPTIONAL_HDR32_MAGIC
    at IMAGE_OPTIONAL_HEADER32.AddressOfEntryPoint,   dd EntryPoint - IMAGEBASE ; not strictly required
    at IMAGE_OPTIONAL_HEADER32.ImageBase,             dd IMAGEBASE ; not required under XP
    at IMAGE_OPTIONAL_HEADER32.SectionAlignment,      dd SECTIONALIGN
    at IMAGE_OPTIONAL_HEADER32.FileAlignment,         dd FILEALIGN
    at IMAGE_OPTIONAL_HEADER32.MajorSubsystemVersion, dw 4
    at IMAGE_OPTIONAL_HEADER32.SizeOfImage,           dd SIZEOFIMAGE
    at IMAGE_OPTIONAL_HEADER32.SizeOfHeaders,         dd SIZEOFIMAGE - 1 ; required for XP
    at IMAGE_OPTIONAL_HEADER32.Subsystem,             dw IMAGE_SUBSYSTEM_WINDOWS_CUI
```

as most elements are actually unused, they can be used for other reasons.
  * `hdrcode` contains a maximum of executed code in its header, and calculate fibonacci numbers via FPU.
> > ![https://corkami.googlecode.com/svn/wiki/pics/PE_hdrcode.png](https://corkami.googlecode.com/svn/wiki/pics/PE_hdrcode.png)
  * TBC's 1kb Traceless demo also contains an [executable header](https://corkami.googlecode.com/svn/trunk/asm/misc/traceless.bat)
> > <a href='http://www.youtube.com/watch?feature=player_embedded&v=EDc0qmDrjtM' target='_blank'><img src='http://img.youtube.com/vi/EDc0qmDrjtM/0.jpg' width='425' height=344 /></a>

## 32b and 64b differences ##
  * Magic constants:
| | Intel **32** bits | AMD **64** bits |
|:|:------------------|:----------------|
| IMAGE\_FILE\_HEADER.Machine  | IMAGE\_FILE\_MACHINE\_I386 (0014ch) | IMAGE\_FILE\_MACHINE\_AMD64 (8664h) |
| IMAGE\_OPTIONAL\_HEADER32/64.Magic| IMAGE\_NT\_OPTIONAL\_HDR32\_MAGIC (0010bh) | IMAGE\_NT\_OPTIONAL\_HDR64\_MAGIC (0020bh)|

  * from Double word to Quad word:
    * IMAGE\_OPTIONAL\_HEADER32/64
||
|:|
| ImageBase          |
|  |
| SizeOfStackReserve |
| SizeOfStackCommit  |
| SizeOfHeapReserve  |
| SizeOfHeapCommit   |
|  |

  * IMAGE\_TLS\_DIRECTORY32/64
| StartAddressOfRawData |
|:----------------------|
| EndAddressOfRawData   |
| AddressOfIndex        |
| AddressOfCallBacks    |
|  |

  * IMAGE\_LOAD\_CONFIG\_DIRECTORY32/64
||
|:|
| DeCommitFreeBlockThreshold  |
| DeCommitTotalFreeThreshold  |
| LockPrefixTable             |
| MaximumAllocationSize       |
| VirtualMemoryThreshold      |
| ProcessAffinityMask         |
|  |
| EditList        |
| SecurityCookie  |
| SEHandlerTable   |
| SEHandlerCount   |

  * IMAGE\_THUNK\_DATA32/64
| ForwarderString/Function/Ordinal/AddressOfData    |
|:--------------------------------------------------|

  * structure alterations:
    * IMAGE\_OPTIONAL\_HEADER32/64.**BaseOfData**, Dword in 32 bits, is absent in 64 bits
    * IMAGE\_LOAD\_CONFIG\_DIRECTORY32/64 : **ProcessHeapFlags** and **ProcessAffinityMask** are swapped

# structure by structure #

## DOS Header ('MZ Header') ##
  * starts at offset 0
  * most values only matters to the DOS stub.
    * `compiled` contains a DOS stub
> > > <wiki:gadget url=https://corkami.googlecode.com/svn/wiki/gadgets/flash.xml up\_File=https://corkami.googlecode.com/svn/wiki/pics/wink/PE\_dosstub.swf up\_FlashHeight=157 up\_FlashWidth=380 up\_ContainerCol="#d1dae3" height=157 width=380 title="" border=0/>
```
   DOS_HEADER:
...
    .e_cparhdr     dw (dos_stub - DOS_HEADER) >> 4 ; defines MZ stub entry point
...

align 010h, db 0

dos_stub:
bits 16
    push    cs
    pop     ds
    mov     dx, dos_msg - dos_stub
    mov     ah, 9
    int     21h
    mov     ax, 4c01h
    int     21h
dos_msg
    db 'This program cannot be run in DOS mode.', 0dh, 0dh, 0ah, '$'
```
  * While it's usually just printing a string and terminating, the dos stub can do everything: open, modify files, and even executes PE.

### exe2pe ###
<wiki:gadget url=https://corkami.googlecode.com/svn/wiki/gadgets/flash.xml up\_File=https://corkami.googlecode.com/svn/wiki/pics/wink/PE\_exe2pe.swf up\_FlashHeight=276 up\_FlashWidth=747 up\_ContainerCol="#d1dae3" height=276 width=747 title="" border=0/>

this file is a broken 32b PE that is fixed (on disk), then launched by its (16b) dos stub. What's the most surprising is that a 16b process can launch the 32b part of a PE: if you were in a DOS environment, the 16b stub would be executed.

## e\_magic ##
  * = `MZ`
  * stands for [Mark Zbikowski](http://en.wikipedia.org/wiki/Mark_Zbikowski)
  * it can be `ZM` on an (non-PE) EXE. These executables still work under XP via ntvdm.
    * `dosZMXP` is a non-PE EXE with a ZM signature

> > <wiki:gadget url=https://corkami.googlecode.com/svn/wiki/gadgets/flash.xml up\_File=https://corkami.googlecode.com/svn/wiki/pics/wink/PE\_doszmxp.swf up\_FlashHeight=208 up\_FlashWidth=207 up\_ContainerCol="#d1dae3" height=208 width=207 title="" border=0/>
```
at IMAGE_DOS_HEADER.e_magic, db 'ZM'
```

### e\_cparhrd ###
  * points to the dos stub, shifted by 4 (count of paragraphs)

### e\_lfanew ###
  * is the only required element (besides the signature) of the DOS HEADER to turn the EXE into a PE.
  * is a relative offset to the NT Headers.
  * can't be null (signatures would overlap)
  * can be 4 at minimum
    * `tiny` has a `e_lfanew` of 4, which means the NT Headers is overlapping the DOS Header.
```
DOS_HEADER:
.e_magic dw 'MZ'

align 4, db 0

istruc IMAGE_NT_HEADERS
    at IMAGE_NT_HEADERS.Signature, db 'PE',0,0
...
    at IMAGE_OPTIONAL_HEADER32.SectionAlignment,          dd 4      ; also sets e_lfanew
    at IMAGE_OPTIONAL_HEADER32.FileAlignment,             dd 4
```
    * `appendedhdr` and `apphdrW7` have both a `e_lfanew` of 400h, which puts the NT Headers in appended data. However, it's required under XP that the Header is extended till there via SizeOfHeaders.

## Rich header ##
  * is an unofficial structure generated by compilers.
  * has no consequence on PE execution
  * is not documented officially, but documented by _lifewire_ and _Daniel Pistelli_.
    * `compiled` contains a Rich Header
```
align 16, db 0
RichHeader:
RichKey EQU 092033d19h
dd "DanS" ^ RichKey     , 0 ^ RichKey, 0 ^ RichKey       , 0 ^ RichKey
dd 0131f8eh ^ RichKey   , 7 ^ RichKey, 01220fch ^ RichKey, 1 ^ RichKey
dd "Rich", 0 ^ RichKey  , 0, 0
align 16, db 0
```
## NT Headers ('PE Header') ##
  * must start with 'PE', 0, 0
    * this is the smallest requirement for a valid PE. see
  * has to be dword-aligned.

### Machine ###
  * specifies the used CPU
    * `014c` for 32b, which officially correspond to 'Intel 386 or later'. `014d`/`014e` are unofficially corresponding to 486 and Pentium, however such Machine types are rejected for execution by Windows.
    * `8664` for 64b (AMD64 only, not Itaniums, with `0200`)
  * is not required when no code should be executed (see [data or resource-only PEs](#Data_Files.md))

### NumberOfSections ###
  * can be null with low alignment PEs
    * `nosection*.exe`, `tiny*.exe`
    * and in this case, the values are just checked but not really used (under XP)
> > <wiki:gadget url=https://corkami.googlecode.com/svn/wiki/gadgets/flash.xml up\_File=https://corkami.googlecode.com/svn/wiki/pics/wink/PE\_maxsecxp.swf up\_FlashHeight=234 up\_FlashWidth=509 up\_ContainerCol="#d1dae3" height=234 width=509 title="" border=0/>
  * can be up to 96 under XP:
    * `96emptysections.exe` (all identical), `96workingsections.exe` (all physically different), `maxsecXP.exe` (garbage table)
  * can be up to 65535 under Vista and later:
    * `65535sects.exe` has 65535 sections, that are all virtually executed.
> > > <wiki:gadget url=https://corkami.googlecode.com/svn/wiki/gadgets/flash.xml up\_File=https://corkami.googlecode.com/svn/wiki/pics/wink/PE\_65535.swf up\_FlashHeight=417 up\_FlashWidth=510 up\_ContainerCol="#d1dae3" height=417 width=510 title="" border=0/>

### TimeDateStamp ###
  * has a different meaning whether it's a Borland or a Microsoft compiler
  * is used for bound imports check

##### PointerToSymbolTable/NumberOfSymbols #####
no importance whatsoever for the loader

### SizeOfOptionalHeader ###
  * is not the size of the optional header, but the delta between the top of the Optional header and the start of the section table.

> Thus, it can be null (the section table will overlap the Optional Header, or can be null when no sections are present), or bigger than the file (the section table will be in virtual space, full of zeroes), but can't be negative.
    * `nullSOH-XP` is a PE with a null SizeOfHeaders. the section table is thus overlapping the optional header. (XP only).
> > <wiki:gadget url=https://corkami.googlecode.com/svn/wiki/gadgets/flash.xml up\_File=https://corkami.googlecode.com/svn/wiki/pics/wink/PE\_nullSOH-xp.swf up\_FlashHeight=211 up\_FlashWidth=511 up\_ContainerCol="#d1dae3" height=211 width=511 title="" border=0/>
```
...
SECTIONALIGN equ 4
FILEALIGN equ 4
...
OptionalHeader:                                                                                     ; Section Table
istruc IMAGE_OPTIONAL_HEADER32
    at IMAGE_OPTIONAL_HEADER32.Magic,                     dw IMAGE_NT_OPTIONAL_HDR32_MAGIC
    at IMAGE_OPTIONAL_HEADER32.SizeOfInitializedData,     dd EntryPoint - IMAGEBASE                 ; VirtualSize (duplicate value to accept loading)
    at IMAGE_OPTIONAL_HEADER32.AddressOfEntryPoint,       dd EntryPoint - IMAGEBASE                 ; SizeOfRawData
...
```
    * `virtsectblXP` is a PE with its 82 sections of empty information, with its section table in virtual space.
> > <wiki:gadget url=https://corkami.googlecode.com/svn/wiki/gadgets/flash.xml up\_File=https://corkami.googlecode.com/svn/wiki/pics/wink/PE\_virtsectblxp.swf up\_FlashHeight=273 up\_FlashWidth=548 up\_ContainerCol="#d1dae3" height=273 width=548 title="" border=0/>
```
...
    at IMAGE_FILE_HEADER.NumberOfSections,      dw 82 ; 0 <= NumberOfSections <= 82 (varies with SizeOfOptionalHeader)
...
SIZEOFOPTIONALHEADER equ 10h + $ - IMAGEBASE ; bigger than the file itself !
; section table starts here...
<EOF>
```
  * standard value: `E0`

### Characteristics ###
  * 0x2 IMAGE\_FILE\_EXECUTABLE\_IMAGE is required when code is executed.
  * 0x2000 / IMAGE\_FILE\_DLL is required in for DLLs, in most cases, except:
    * if it's not set, the DLLMain will not be called, but the DLL is loaded and exports are usable. if it was dynamically called, the imports of the DLL won't be resolved
      * `dllnomain.dll` is staticly-loaded, has no valid DllMain, but its export is executed.
> > > ![https://corkami.googlecode.com/svn/wiki/pics/PE_dllnomain.png](https://corkami.googlecode.com/svn/wiki/pics/PE_dllnomain.png)
```
...
    at IMAGE_FILE_HEADER.Characteristics,       dw IMAGE_FILE_EXECUTABLE_IMAGE
...
    at IMAGE_OPTIONAL_HEADER32.AddressOfEntryPoint,       dd 31415926h
...
```
      * `dllnomain2.dll` is an import-less dynamically loaded dll with no DllMain
    * IMAGE\_FILE\_DLL should NOT be set for a non-DLL PE.
      * `maxvalues.exe` has all its characteristics set excepted IMAGE\_FILE\_DLL.
> > > ![https://corkami.googlecode.com/svn/wiki/pics/PE_maxvals2.png](https://corkami.googlecode.com/svn/wiki/pics/PE_maxvals2.png)
  * 0x100 / IMAGE\_FILE\_32BIT\_MACHINE is **not** required, even in 32b.
    * it can be set for a 64b PE32+, and causes no problem.
  * nothing else is required

## Optional Header ##
  * is anything but optional, as soon as execution is required.

### Magic ###
  * specifies the exact format of OptionalHeader
    * `10b` for 32b
    * `20b` for 64b

##### MajorLinkerVersion/MinorLinkerVersion #####
no particular importance whatsoever

if LinkerVersion < 2.5, Microsoft AppLocker might wrongly report `is not a valid Win32 application. (Exception from HRESULT: 0x800700C1)` for no apparent reason. Changing these fields might fix the problem.

##### SizeOfCode/SizeOfInitializedData/SizeOfUninitializedData #####
no particular importance whatsoever

### AddressOfEntryPoint ###
  * Under Windows 8, AddressOfEntryPoint is not allowed to be smaller than SizeOfHeaders, except if it's null.
  * can be null in DLLs: in this case, DllMain is just not called.
  * can be null
    * `nullEP.exe` has a null EntryPoint: Execution starts at ImageBase, executing 'MZ' as 'dec ebp/pop edx'

> > <wiki:gadget url=https://corkami.googlecode.com/svn/wiki/gadgets/flash.xml up\_File=https://corkami.googlecode.com/svn/wiki/pics/wink/PE\_nullep.swf up\_FlashHeight=247 up\_FlashWidth=557 up\_ContainerCol="#d1dae3" height=247 width=557 title="" border=0/>
```
EntryPoint:
istruc IMAGE_DOS_HEADER
    at IMAGE_DOS_HEADER.e_magic, db 'MZ'

    push Msg
    call [__imp__printf]
    add esp, 1 * 4
    push 0
    call [__imp__ExitProcess]

    at IMAGE_DOS_HEADER.e_lfanew, dd NT_Signature - IMAGEBASE
```
  * can be absent/bypassed (see TLS)
  * can be negative
    * `dllextep-ld` has an external EntryPoint that is located in `dllextep.dll` (a dll with no relocations)
> > <wiki:gadget url=https://corkami.googlecode.com/svn/wiki/gadgets/flash.xml up\_File=https://corkami.googlecode.com/svn/wiki/pics/wink/PE\_dllextep.swf up\_FlashHeight=238 up\_FlashWidth=608 up\_ContainerCol="#d1dae3" height=238 width=608 title="" border=0/>
      * `dllext-ep`:
```
IMAGEBASE equ 1000000h
```
      * `dllextep-ld`:
```
IMAGEBASE equ 33000000h
...
    at IMAGE_OPTIONAL_HEADER32.AddressOfEntryPoint,       dd 1001008h - IMAGEBASE
```
  * can be virtual
    * `virtEP.exe` code starts one byte before the start of the section. This virtual space will be full of 0 on loading, so the first opcode will be made from one virtual byte and one physical byte.
> > <wiki:gadget url=https://corkami.googlecode.com/svn/wiki/gadgets/flash.xml up\_File=https://corkami.googlecode.com/svn/wiki/pics/wink/PE\_virtEP.swf up\_FlashHeight=268 up\_FlashWidth=358 up\_ContainerCol="#d1dae3" height=268 width=358 title="" border=0/>
```
    at IMAGE_OPTIONAL_HEADER32.AddressOfEntryPoint,       dd EntryPoint - IMAGEBASE - 1 ; -1 to start in virtual space
...
; actual EntryPoint starts here...
; there will be a virtual 00 before, so 00 C0 will be executed as `add al, al`
EntryPoint:
    db 0c0h
...
```
    * `tls_virtEP` has an 'invalid' EntryPoint, but the TLS allocates the memory space and generates some codes, so the EntryPoint works fine.
> > <wiki:gadget url=https://corkami.googlecode.com/svn/wiki/gadgets/flash.xml up\_File=https://corkami.googlecode.com/svn/wiki/pics/wink/PE\_tlsvirtEP.swf up\_FlashHeight=284 up\_FlashWidth=532 up\_ContainerCol="#d1dae3" height=284 width=532 title="" border=0/>
  * is not used when a staticly loaded DLL doesn't have IMAGE\_FILE\_DLL set as IMAGE\_FILE\_HEADER.Characteristics
  * when a static DLL's DllMain is executed, the context of the not-executed-yet PE is available via lpvReserved. Thus, a static DLL can freely modify the value of the future EntryPoint to be executed
    * `ctxt` and `ctxt-ld` are an example of such context modification via lpvReserved.
> > > ![https://corkami.googlecode.com/svn/wiki/pics/PE_lpvreserved.png](https://corkami.googlecode.com/svn/wiki/pics/PE_lpvreserved.png)

##### BaseOfCode/BaseOfData #####
no particular importance whatsoever

### ImageBase ###
  * is a multiple of 10000h
  * can be null, under XP. In this case, the binary will be relocated to 10000h
    * `ibnullXP.exe` has a null imagebase, and relocations
> > > <wiki:gadget url=https://corkami.googlecode.com/svn/wiki/gadgets/flash.xml up\_File=https://corkami.googlecode.com/svn/wiki/pics/wink/PE\_ibnullxp.swf up\_FlashHeight=155 up\_FlashWidth=380 up\_ContainerCol="#d1dae3" height=155 width=380 title="" border=0/>
  * can be any value as long as `ImageBase + 'SizeOfImage' < 80000000h`
    * `bigib.exe` has an ImageBase of 7ffd0000h, and no relocations
> > > ![https://corkami.googlecode.com/svn/wiki/pics/PE_bigib.png](https://corkami.googlecode.com/svn/wiki/pics/PE_bigib.png)
  * ImageBase can't collide with ntdll.dll or kernel32, even if relocations are present (in the loaded PE), because they can't be relocated.
    * in this case, it gives a unique error message, under Windows 7: `the subsystem needed to support the image type is not present.` even if it has nothing to do with the subsystem.
  * if the ImageBase is bigger than that, the binary will be relocated to 10000h
    * `ibkernel.exe` has an ImageBase of 0FFFF0000h and relocations.

> > ![https://corkami.googlecode.com/svn/wiki/pics/PE_ibkernel.png](https://corkami.googlecode.com/svn/wiki/pics/PE_ibkernel.png)

### SectionAlignment/FileAlignment ###
  * both are power of 2 (4, 8, 16...)
  * standard mode: `200 <= FileAlignment <= SectionAlignment` and `1000 <= SectionAlignment`
    * `normal.exe` has aligments of 1000/200
    * `bigalign.exe` has alignments of 20000000/10000
> > ![https://corkami.googlecode.com/svn/wiki/pics/PE_bigalign.png](https://corkami.googlecode.com/svn/wiki/pics/PE_bigalign.png)
  * in low alignment: `1 <= FileAlignment = SectionAlignment <= 800`
    * all `nosection*.exe` have alignments of 1/1

##### MajorOperatingSystemVersion/MinorOperatingSystemVersion/MajorImageVersion/MinorImageVersion #####
no particular importance whatsoever

### MajorSubsystemVersion/MinorSubsystemVersion ###
  * MajorSubsystemVersion.MinorSubsystemVersion has to be at least 3.10
    * `dll-ld.exe` has a version of 3.10
  * this effects heap flags
  * in DLLs, MajorSubsystemVersion is ignored until Windows 8. It can have any value. Under Windows 8, it needs a standard value (3.10 < 6.30)
  * if SubsystemVersion is 6.30, the loader enforces the presence of the LoadConfig entry, with a valid cookie, unless GuardFlags are set to IMAGE\_GUARD\_SECURITY\_COOKIE\_UNUSED.
    * `ss63.exe` has a Subsystem version of 6.30, and the minimum amount of information to get it running.
```
...
    at IMAGE_OPTIONAL_HEADER32.MajorSubsystemVersion, dw 6      ; <=
    at IMAGE_OPTIONAL_HEADER32.MinorSubsystemVersion, dw 3      ; <=
...
    at IMAGE_DATA_DIRECTORY_16.Load,      dd LoadConfig - IMAGEBASE, LOADCONFIGSIZE
...
    at IMAGE_LOAD_CONFIG_DIRECTORY32.Size,           dd IMAGE_LOAD_CONFIG_DIRECTORY32_size
    at IMAGE_LOAD_CONFIG_DIRECTORY32.SecurityCookie, dd cookie
...
```
    * `ss63nocookie.exe` has a Subsystem version of 6.30, and no cookie
```
...
istruc IMAGE_LOAD_CONFIG_DIRECTORY32
    at IMAGE_LOAD_CONFIG_DIRECTORY32.Size,       dd IMAGE_LOAD_CONFIG_DIRECTORY32_size
    at IMAGE_LOAD_CONFIG_DIRECTORY32.GuardFlags, dd IMAGE_GUARD_SECURITY_COOKIE_UNUSED
iend
...
```

### Reserved1 (Win32VersionValue) ###
  * officially defined as ''reserved'' and should be null
  * if non null, it overrides MajorVersion/MinorVersion/BuildNumber/PlatformId OperatingSystem Versions values located in the PEB, after loading.
    * `winver.exe` has a non-null Win32VersionValue to shows altered values, via GetVersionExA
```
OSMAJOR equ 31
OSMINOR equ 41
BUILD equ 5926
ID equ 3 ; [0;3]

	at IMAGE_OPTIONAL_HEADER32.Win32VersionValue, dd OSMAJOR | (OSMINOR << 8) | ((BUILD & 03fffh) << 16) | (((ID & 3) ^ 02h) << 30)
```


> ![https://corkami.googlecode.com/svn/wiki/pics/PE_winver.png](https://corkami.googlecode.com/svn/wiki/pics/PE_winver.png)

### SizeOfImage ###
  * normally equal the total virtual size of all sections + headers
    * `65535sects.exe` has a SizeOfImage of 0x7027A000 (W7 only)
    * `tinyXP` has a SizeOfImage of 0x2e. it only covers up to the EntryPoint value.

### SizeOfHeaders ###
  * can be extended to the whole file, and sometimes be smaller than the header itself.
    * `foldedhdrW7.exe` is a PE with standard alignments and a SizeOfHeaders of 1.
    * `maxsec_lowaligW7.exe` has a SizeOfHeaders of 27570879.

### CheckSum ###
  * [simple algorithm](http://code.google.com/p/pefile/source/browse/trunk/pefile.py#4659)
  * required for drivers only

### Subsystem ###
From a technical perspective, drivers and gui/console PEs are identical, except that:
  * DRIVER need low alignments, a correct checksum, and to be signed under Vista or later (if not running in debug mode).
  * a CONSOLE PE is exactly like a GUI PE except that it comes with a pre-attached console.

  * `multiss` is a multi subsystem PE that will work correctly under all 3 subsystems, and display a message.
> > <wiki:gadget url=https://corkami.googlecode.com/svn/wiki/gadgets/flash.xml up\_File=https://corkami.googlecode.com/svn/wiki/pics/wink/PE\_multiss.swf up\_FlashHeight=245 up\_FlashWidth=397 up\_ContainerCol="#d1dae3" height=245 width=397 title="" border=0/>
    1. determines the level of execution by checking CS
    1. resolve NTOSKRNL/KERNEL32 imports manually (by checksum, as it can't have any static import)
  * in Dlls, Subsystem is ignored - it can have any value.
    * `dllfakess.dll` has a Subsystem value of -1.

##### DllCharacteristics #####
  * not necessary
    * `dll.dll` is a working DLL with this value set to 0
  * 0080h `IMAGE_DLLCHARACTERISTICS_FORCE_INTEGRITY` enforces that the executable is signed before execution
  * 0100h `IMAGE_DLLCHARACTERISTICS_NX_COMPAT` prevents code execution on the stack
    * `no_dep` executes code on the stack, with no NX\_COMPAT flag set
    * `dep` executes code on the stack, which is forbidden as NX\_COMPAT flag is set, and catches the exception
  * 0400h `IMAGE_DLLCHARACTERISTICS_NO_SEH` (0400h) prevents the executable to uses Structured Exception Handler. Vectored Exception Handler still work.
    * `no_seh` has `NO_SEH` set, triggers an exception, handled via its VEH.
  * 1000h `IMAGE_DLLCHARACTERISTICS_APPCONTAINER` is only to be used by Metro Apps in Windows 8. A standard PE can't run under Windows 8 if this flag is set.
  * 4000h `IMAGE_DLLCHARACTERISTICS_GUARD_CF` indicates that the file supports the Control Flow guard (data is in the LoadConfig entry), introduced in Windows 8.1.


##### SizeOfStackReserve/SizeOfStackCommit/SizeOfHeapReserve/SizeOfHeapCommit #####
  * can be zero, but not any value
    * `normal.exe` has these value set to zero.
##### LoaderFlags #####

### NumberOfRvaAndSizes ###
  * rounded down to 16 if bigger
    * `maxvals` has a maximum of PE structures set to FF, including its NumberOfRvaAndSizes which is set to FFFF
> > ![https://corkami.googlecode.com/svn/wiki/pics/PE_maxvals.png](https://corkami.googlecode.com/svn/wiki/pics/PE_maxvals.png)

  * can be 0
    * `no_dd` has no data directory (see `imports`)
  * .Net loaders ignores this value, even if it requires relocations and a COM data directory, and will parse them. Thus, a .Net PE can work with a NumberOfRvaAndSizes of 2.
> > ![https://corkami.googlecode.com/svn/wiki/pics/PE_tinynet_2dd.png](https://corkami.googlecode.com/svn/wiki/pics/PE_tinynet_2dd.png)

## data directories ##

### exports ###
  * like many data directories, Exports' size are not necessary, except for forwarding (see below).
  * Characteristics, TimeDateStamp, MajorVersion and MinorVersion are not necessary.
  * Base is only used for ordinal imports
    * `dll.dll` has a small export table, for one named entry:
```
    at IMAGE_DATA_DIRECTORY_16.ExportsVA,  dd Exports_Directory - IMAGEBASE
...
Exports_Directory:
  Characteristics       dd 0
  TimeDateStamp         dd 0
  MajorVersion          dw 0
  MinorVersion          dw 0
  Name                  dd aDllName - IMAGEBASE
  Base                  dd 0
  NumberOfFunctions     dd NUMBER_OF_FUNCTIONS
  NumberOfNames         dd NUMBER_OF_NAMES
  AddressOfFunctions    dd address_of_functions - IMAGEBASE
  AddressOfNames        dd address_of_names - IMAGEBASE
  AddressOfNameOrdinals dd address_of_name_ordinals - IMAGEBASE
...
aDllName db 'dll.dll', 0
...
address_of_functions:
    dd __exp__Export - IMAGEBASE
NUMBER_OF_FUNCTIONS equ ($ - address_of_functions) / 4
...
address_of_names:
    dd a__exp__Export - IMAGEBASE
NUMBER_OF_NAMES equ ($ - address_of_names) / 4
...
address_of_name_ordinals:
    dw 0
...
a__exp__Export db 'export', 0
```
  * the Export Name is not necessary, and can be anything.
    * `dllweirdexp.dll` is correctly exported, with a corrupted Export Name
> > ![https://corkami.googlecode.com/svn/wiki/pics/PE_dllweirdexp.png](https://corkami.googlecode.com/svn/wiki/pics/PE_dllweirdexp.png)
```
Exports_Directory:
...
  Name                  dd VDELTA + aDllName - IMAGEBASE
...
aDllName db 'completely unrelated dll name', 1, 2, 3, 4, 0
```
  * AddressOfNames is lexicographically-ordered (like a dictionary): the pointer to a name starting with A shouldn't be after a name starting with B, and so on...
    * `export_order` has some exports wrongly ordered, which makes them unusable with the official loader.
```
...
    push a_export
...
    call [__imp__GetProcAddress]
    jmp eax
...

    push a_export2
...
    call [__imp__GetProcAddress] ; we assume EAX==0 because it should fail
    add eax, end_ - 07fh         ; expected error code: 7fh
...
address_of_names:
    dd a_export - IMAGEBASE
    dd a_zz - IMAGEBASE
    dd a_export2 - IMAGEBASE
```
  * export names can have any value (even null or more than 65536 characters long, with unprintable characters), just null terminated.
    * `dllemptyexp-ld.exe` loads `dllemptyexp.dll` with a null export
> > > ![https://corkami.googlecode.com/svn/wiki/pics/PE_dllemptyexp.png](https://corkami.googlecode.com/svn/wiki/pics/PE_dllemptyexp.png)
    * `dllweirdexp-ld.exe` loads `dllweirdexp.dll` with a 131102-long non-Ascii export.
  * an .EXE can have exports (no need of relocation nor DLL flag), and can use them normally
    * `ownexports.exe` uses its own exports
    * or they can be not used for execution, but for documenting the internal code
      * `PE_exports_doc` has internal exports used as address symbols
> > > > ![https://corkami.googlecode.com/svn/wiki/pics/PE_exports_doc.png](https://corkami.googlecode.com/svn/wiki/pics/PE_exports_doc.png)
    * or they can have external or virtual addresses (but no address of 0, (un?)surprisingly)
      * `ownexports2` has a -1 export, to which it jumps (after adding 1). It also has an export in virtual space, which is executed.
> > > > ![https://corkami.googlecode.com/svn/wiki/pics/PE_virtual_exports.gif](https://corkami.googlecode.com/svn/wiki/pics/PE_virtual_exports.gif)
  * exports at fixed value can be used to encode data, when their values are filled in the Import Address Table (their actual value doesn't matter as long as they are not executed, it's just a copied dword)
    * `exportsdata.exe`'s code is stored as dodgy exports RVAs, and is restored when imports are resolved
```
EntryPoint:
ownexports.exe_iat:
    dd 80000000h ; => dd 000101468h => 68 14100010   push 10001014
    dd 80000001h ; => dd 04015FF10h => FF15 40100010 call [10001040]
    dd 80000002h ; => dd 083100010h => 83C4 04       add esp,4
    dd 80000003h ; => dd 0CCC304C4h => C3            retn
    dd 0
...
Exports_Directory:
...
address_of_functions:
dd 000101468h - IMAGEBASE
dd 04015FF10h - IMAGEBASE
dd 083100010h - IMAGEBASE
dd 0CCC304C4h - IMAGEBASE
NBFUNCTIONS equ ($ - address_of_functions) / 4
...
```
  * fake exports values can disrupt proper disassembly
    * `exportobf.exe` contains fake exports to make disassembly harder
  * ordinals-only exports can make the structure even smaller (no NumberOfFunctions/NumberOfNames/AddressOfNames/AddressOfNameOrdinals). Fake entries can be also present in exports as long as Base + Ordinal matches the wanted export.
    * `impbyord.exe` calls its own imports by ordinals
    * `dllord-ld.exe` is importing export #314 from `dllord.dll`
      * `dllord-ld.exe`
```
dll.dll_iat:
__imp__export:
    dd 1 << 31 | 314h ; highest bit set to indicate imports by ordinal
```
      * `dllord.dll`
```
 Exports_Directory:
...
  Base                  dd 313h
...
  AddressOfFunctions    dd address_of_functions - IMAGEBASE
...

address_of_functions:
    dd -1                                   ; bogus entry that will be 313h
    dd __exp__Export - IMAGEBASE
```
  * exports can be used just to forward imports. in this case, the forwarded import is written as `dll.API` and must be within exportsVA and exportsVA+Size.
    * `dllfw.dll` forwards imports from msvcrt
```
address_of_functions:
    dd amsvcrt_printf - IMAGEBASE
...
address_of_names:
    dd a__exp__Export - IMAGEBASE
...
amsvcrt_printf db "msvcrt.printf", 0 ; forwarding string can only be within the official export directory bounds
a__exp__Export db 'ExitProcess', 0

EXPORTS_SIZE equ $ - Exports_Directory
```
  * exports can also forward each other and create loops
    * `dllfwloop.dll` forwards its own imports until the right API is called.

> ![https://corkami.googlecode.com/svn/wiki/pics/PE_dllfwloop.png](https://corkami.googlecode.com/svn/wiki/pics/PE_dllfwloop.png)
```
...
address_of_functions:
    dd adllfwloop_loophere  - IMAGEBASE
    dd adllfwloop_looponceagain  - IMAGEBASE
    dd amsvcrt_printf - IMAGEBASE
    dd adllfwloop_GroundHogDay - IMAGEBASE
    dd adllfwloop_Yang - IMAGEBASE
    dd adllfwloop_Ying - IMAGEBASE
NUMBER_OF_FUNCTIONS equ ($ - address_of_functions) / 4
_d

address_of_names:
    dd a__exp__ExitProcess - IMAGEBASE
    dd a__exp__LoopHere - IMAGEBASE
    dd a__exp__LoopOnceAgain - IMAGEBASE
    dd a__exp__GroundHogDay - IMAGEBASE
    dd a__exp__Ying - IMAGEBASE
    dd a__exp__Yang - IMAGEBASE
NUMBER_OF_NAMES equ ($ - address_of_names) / 4
...
```
### imports ###
  * Imports size is not used.
  * ImportAddressTable is typically not required (excepted in low alignments, under XP)
    * `dll-ld.exe` imports `dll.dll` with a small import table:
```
    at IMAGE_DATA_DIRECTORY_16.ImportsVA,   dd Import_Descriptor - IMAGEBASE
...
EntryPoint:
    call [__imp__export]
...

Import_Descriptor:
...
dll.dll_DESCRIPTOR:
    dd dll.dll_hintnames - IMAGEBASE
    dd 0, 0
    dd dll.dll - IMAGEBASE
    dd dll.dll_iat - IMAGEBASE
;terminator
    dd 0, 0, 0, 0, 0
...
dll.dll_hintnames:
    dd hndllexport - IMAGEBASE
    dd 0
...
hndllexport:
    dw 0
    db 'export', 0
...
dll.dll_iat:
__imp__export:
    dd hndllexport - IMAGEBASE
    dd 0
...
dll.dll db 'dll.dll', 0
```
  * under XP and later, a DLL can be called by his filename only (no extension). Extension is required under Win2K.
    * `imports_noext.exe` imports DLLs without extensions.
```
kernel32.dll db 'kernel32', 0
msvcrt.dll db 'msvcrt', 0
```
  * under XP, W8, but not W7, a file can be imported even if the imports name has trailing spaces/dots
    * `importsdotXP` uses standard APIs via names with trailing characters
```
kernel32.dll db 'kernel32.dll...', 0
msvcrt.dll db 'msvcrt.dll.', 0
```
  * importing your own exports with trailing characters might generate a crash in debuggers, and fail at loading.
    * `ownexportsdot.exe` imports himself, but as `ownexportsdot.exe. ...`
  * a DLL can be dynamically loaded via ANSI (LoadLibraryA) or UNICODE (LoadLibraryW).
  * like in any filename, case of the DLL can be mixed
    * `imports_mixed.exe` imports DLLs with mixed case names.
```
kernel32.dll db 'KernEl32', 0
msvcrt.dll db 'mSVCrT', 0
```
  * the Import Lookup Table is not required and can be replaced by the Import Adress Table, or it can be just null
    * `imports_noint.exe` contains no ILT, the import descriptor are linking twice to the IAT.
```
...
Import_Descriptor:
kernel32.dll_DESCRIPTOR:
    dd kernel32.dll_iat - IMAGEBASE
    dd 0, 0
    dd kernel32.dll - IMAGEBASE
    dd kernel32.dll_iat - IMAGEBASE
...
```
    * because it can be null, it can be put in virtual space, like the next 2 dwords (timestamp, forwarderchain)
      * `imports_virtdesc` has an import descriptor starting in virtual space over its 3 first dwords
> > > > ![https://corkami.googlecode.com/svn/wiki/pics/PE_imports_virtdesc.png](https://corkami.googlecode.com/svn/wiki/pics/PE_imports_virtdesc.png)
  * if the Import Lookup Table is present, then it also determins the length of the Import Address Table, as they are parsed in parallel. In this case, the Import Address Table doesn't have to be null-terminated.
    * `dllmaxvals` has a non null-terminated IAT but a null-terminated ILT
  * if the IAT is linked in the descriptor but empty, then the DLL is not loaded, and the file is loaded even with an invalid dll name - the descriptor is skipped.
    * `imports_nothunk` has a bogus descriptor with an empty IAT, and a bogus DLL name, between real descriptors.
  * the imports directory is not compulsory (XP or later). either don't call any API (ex, resource placeholders), or locate Kernel32 and its exports by hand (see below, `no_dd`)
  * the imports descriptor terminator just need to have null values. So, it can be placed in virtual space.
    * `imports_vterm.exe` has a virtual terminator
```
  ;terminator is partially in virtual space
    dd msvcrt.dll_hintnames - IMAGEBASE
    dd 0, 0
    <EOF>
```
  * the terminator of imports descriptor only needs to have its Name offset null, or its IAT. Then, the other values are not important.
    * `imports_badterm` has a bad terminator, that looks almost like a valid one:
```
...
;disguised terminator - dll address is 0
dd msvcrt.dll_hintnames - IMAGEBASE
dd 0, 0
dd 0 ;msvcrt.dll - IMAGEBASE
dd msvcrt.dll_iat - IMAGEBASE
...
```
  * under Vista and Windows 7, the dll names can be redirected from `API-MS-*` to standard system DLLs by `c:\windows\system32\apisecschema.dll`, as documented by [deroko](http://xchg.info/wiki/index.php?title=ApiMapSet)
    * `imports_apimsW7` imports ExitProcess via `API-MS-Win-Core-Localization-L1-1-0.dll` which is redirected during loading to `kernel32.dll`


> ![https://corkami.googlecode.com/svn/wiki/pics/PE_imports_apimsw7.png](https://corkami.googlecode.com/svn/wiki/pics/PE_imports_apimsw7.png)
  * as most fields of a descriptor are not necessary, it's possible to squeeze an overlapping IAT in it.
    * `imports_iatindesc.exe` has IATs in descriptors
```
Import_Descriptor:
;kernel32.dll_DESCRIPTOR:
    dd 0 ; can't put the IAT over this one
    msvcrt.dll_iat:
        __imp__printf:
            dd hnprintf - IMAGEBASE
            dd 0
    dd kernel32.dll - IMAGEBASE
    dd kernel32.dll_iat - IMAGEBASE

;msvcrt.dll_DESCRIPTOR:
    dd 0
    kernel32.dll_iat:
        __imp__ExitProcess:
            dd hnExitProcess - IMAGEBASE
            dd 0
    dd msvcrt.dll - IMAGEBASE
    dd msvcrt.dll_iat - IMAGEBASE
```

  * `imports_tiny` combines a lot of these techniques with ordinals, to make a working imports structure as small as possible (40 bytes for 2 imports from 2 dlls)
> ![https://corkami.googlecode.com/svn/wiki/pics/PE_imports_tiny.png](https://corkami.googlecode.com/svn/wiki/pics/PE_imports_tiny.png)
```
  Import_Descriptor:
;kernel32.dll_DESCRIPTOR:
    dd 0
    msvcrt.dll_iat:
        __imp__printf:
            dd 80000000h + 742 ; printf
            dd 0
    dd kernel32.dll - IMAGEBASE
    dd kernel32.dll_iat - IMAGEBASE
;msvcrt.dll_DESCRIPTOR:
    dd 0
    kernel32.dll_iat:
        __imp__ExitProcess:
            dd 80000000h + 183 ; ExitProcess
            dd 0
    dd msvcrt.dll - IMAGEBASE
    dd msvcrt.dll_iat - IMAGEBASE
;terminator
kernel32.dll db 'kernel32' ,0 ; not W2k compatible
msvcrt.dll:
dd 'msvcrt',0
align 4, db 0 ; <= imports terminator NULL
```

  * imports' Data Directory is not required to load imports: by locating kernel32 manually then parsing its exports manually, it's possible to resolve LoadLibraryA, then it's possible to manually load any DLL and resolve any of its exports (manually or via GetProcAddress).
    * `no_dd` has no data directory, and loads its imports manually.

> ![https://corkami.googlecode.com/svn/wiki/pics/PE_no_dd.png](https://corkami.googlecode.com/svn/wiki/pics/PE_no_dd.png)

  * .Net requires an import to `mscoree.dll._CoreExeMain`, and an import size to be at least 0x28.
> > It doesn't accept to import to `mscoree` (without extension).

### resources ###
  * directory-like structure.
```
...
at IMAGE_DATA_DIRECTORY_16.ResourceVA, dd Directory_Entry_Resource - IMAGEBASE
...
push SOME_TYPE              ; lpType
push SOME_NAME              ; lpName
push 0                      ; hModule
call [__imp__FindResourceA]
...
Directory_Entry_Resource:
; root directory
istruc IMAGE_RESOURCE_DIRECTORY
    at IMAGE_RESOURCE_DIRECTORY.NumberOfIdEntries, dw 1
iend
istruc IMAGE_RESOURCE_DIRECTORY_ENTRY
    at IMAGE_RESOURCE_DIRECTORY_ENTRY.NameID, dd SOME_TYPE    ; .. resource type of that directory
    at IMAGE_RESOURCE_DIRECTORY_ENTRY.OffsetToData, dd (1<<31) | (resource_directory_type - Directory_Entry_Resource)
iend

resource_directory_type:
istruc IMAGE_RESOURCE_DIRECTORY
    at IMAGE_RESOURCE_DIRECTORY.NumberOfIdEntries, dw 1
iend
istruc IMAGE_RESOURCE_DIRECTORY_ENTRY
    at IMAGE_RESOURCE_DIRECTORY_ENTRY.NameID, dd SOME_NAME ; name of the underneath resource
    at IMAGE_RESOURCE_DIRECTORY_ENTRY.OffsetToData, dd (1<<31) | (resource_directory_language - Directory_Entry_Resource)
iend

resource_directory_language:
istruc IMAGE_RESOURCE_DIRECTORY
    at IMAGE_RESOURCE_DIRECTORY.NumberOfIdEntries, dw 1
iend
istruc IMAGE_RESOURCE_DIRECTORY_ENTRY
at IMAGE_RESOURCE_DIRECTORY_ENTRY.OffsetToData, dd resource_entry - Directory_Entry_Resource
iend

resource_entry:
istruc IMAGE_RESOURCE_DATA_ENTRY
    at IMAGE_RESOURCE_DATA_ENTRY.OffsetToData, dd resource_data - IMAGEBASE
    at IMAGE_RESOURCE_DATA_ENTRY.Size1, dd RESOURCE_SIZE
iend

resource_data:
Msg db " * message stored in resources", 0ah, 0
RESOURCE_SIZE equ $ - resource_data
```
  * in standard, 3 levels: `Root/Type/Name/Language` but anything else is possible.

  * loops are possible
    * `resourceloop.exe` contains several loops between resource directories
```
resource_directory_type:
...
    at IMAGE_RESOURCE_DIRECTORY_ENTRY.OffsetToData,  dd (1<<31) | (resource_directory_loop - Directory_Entry_Resource)
...

resource_directory_loop:
...
; double level recursivity
...
    at IMAGE_RESOURCE_DIRECTORY_ENTRY.OffsetToData,  dd (1<<31) | (resource_directory_type - Directory_Entry_Resource)
...
; direct recursivity
...
    at IMAGE_RESOURCE_DIRECTORY_ENTRY.OffsetToData,  dd (1<<31) | (resource_directory_loop - Directory_Entry_Resource)
```

  * Names and Types of a resource can be used:
    * immediate integers (aka IDs) - like `resource.exe`
> > ![https://corkami.googlecode.com/svn/wiki/pics/PE_resources.png](https://corkami.googlecode.com/svn/wiki/pics/PE_resources.png)
```
SOME_TYPE equ 315h
SOME_NAME equ 7354h
...
push SOME_TYPE              ; lpType
push SOME_NAME              ; lpName
push 0                      ; hModule
call [__imp__FindResourceA]
...
at IMAGE_RESOURCE_DIRECTORY_ENTRY.NameID, dd SOME_TYPE    ; .. resource type of that directory
...
at IMAGE_RESOURCE_DIRECTORY_ENTRY.NameID, dd SOME_NAME ; name of the underneath resource
```
    * immediate integers (aka IDs) converted as string for loading - like `resource2.exe`
```
push atype                  ; lpType
push ares                   ; lpName
push 0                      ; hModule
call [__imp__FindResourceA]
...
atype db '#315', 0
ares db "#7354", 0
...
SOME_TYPE equ 315
SOME_NAME equ 7354
...
at IMAGE_RESOURCE_DIRECTORY_ENTRY.NameID, dd SOME_TYPE    ; .. resource type of that directory
...
at IMAGE_RESOURCE_DIRECTORY_ENTRY.NameID, dd SOME_NAME ; name of the underneath resource
...
```
    * strings (aka Names). the Name in resource directory is with the format `length + widestring` - like `namedresource.exe`
```
push arestype               ; lpType
push aresname               ; lpName
push 0                      ; hModule
call [__imp__FindResourceA]
...
aresname db 'RES', 0
arestype db 'TYPE', 0
...
    .NumberOfNamedEntries dw 0
    .NumberOfIdEntries    dw 1
    .ID dd (1 << 31) | (alrestype - resource_directory)    ; .. resource type of that directory
...
    .NumberOfNamedEntries dw 0
    .NumberOfIdEntries    dw 1
    .ID dd (1 << 31) | (alresname - resource_directory)  ; name of the underneath resource
...
; length + widestring
alresname dw 3, "R", "E", "S", 0
alrestype dw 4, "T", "Y", "P", "E", 0
```
  * the resource structures are relative to the start of the Data Directory, but the resource data can be anywhere in the file (RVA)
    * `reshdr.exe` has its resource data in the PE header
> > ![https://corkami.googlecode.com/svn/wiki/pics/PE_reshdr.png](https://corkami.googlecode.com/svn/wiki/pics/PE_reshdr.png)
```
at IMAGE_DOS_HEADER.e_magic, db 'MZ'
at IMAGE_DOS_HEADER.e_lfanew, dd NT_Signature - IMAGEBASE
...
resource_data:
Msg db " * resource stored in header and shuffled resource structure", 0ah, 0
RESOURCE_SIZE equ $ - resource_data
```
  * resource strings have their own awkward format, in which 16 strings are stored in each block, and a null string is equivalent to no string, as all strings of the same block are stored consecutively as `<length16><string>`.
    * `resource_string` stores and access its message via resource string
> > > ![https://corkami.googlecode.com/svn/wiki/pics/PE_resource_string.png](https://corkami.googlecode.com/svn/wiki/pics/PE_resource_string.png)

#### Version information ####
  * version information is a resource of type `RT_VERSION` 0x10 (16)
    * thus, parsing version informations requires Data directories and resource parsing, which is not-trivial in extreme cases (folded headers, resource loops...). It might be preferable to just check the `FILE_HEADER`'s TimeDateStamp (which is straightforward, see `hard_imports`) if you just require some minor version check.
  * under XP, the resource DataDirectory has to start at the beginning of its own section, or the properties dialog fails to parse the resources.
  * the `VS_FIXEDFILEINFO` structure is required as well as it's signature, even if not used.

> > `version_cust` contains minimal version informations, which generates the version tab but doesn't show any information
```
...
    at IMAGE_RESOURCE_DIRECTORY_ENTRY.NameID, dd RT_VERSION    ; .. resource type
...
resource_data:
VS_VERSION_INFO:
    .wLength dw VERSIONLENGTH
    .wValueLength dw VALUELENGTH
    .wType dw 0 ; 0 = bin, 1 = text
    WIDE 'VS_VERSION_INFO'
        align 4, db 0
    Value:
        istruc VS_FIXEDFILEINFO
            at VS_FIXEDFILEINFO.dwSignature, dd 0FEEF04BDh
        iend
    VALUELENGTH equ $ - Value
        align 4, db 0
    ; no children
VERSIONLENGTH equ $ - VS_VERSION_INFO

RESOURCE_SIZE equ $ - resource_data
```
  * `version_mini` implements a children StringFileInfo structure, which actually displays some minimal version information (and crashes Windows XP explorer).
```
; children
StringFileInfo:
    dw STRINGFILEINFOLEN
    dw 0 ; no value
    dw 0 ; type
    WIDE 'StringFileInfo'
        align 4, db 0
    ; children
    StringTable:
        dw STRINGTABLELEN
        dw 0 ; no value
        dw 0
        WIDE '040904b0' ; required correct
            align 4, db 0
        ;children
            ; required or won't be displayed by explorer
            __string 'FileVersion', ''
    STRINGTABLELEN equ $ - StringTable
STRINGFILEINFOLEN equ $ - StringFileInfo
```
  * the FixedFileInfo structure can contain any value besides its signature. `version_std` contains only FFs in it.
```
istruc VS_FIXEDFILEINFO
    at VS_FIXEDFILEINFO.dwSignature, dd 0FEEF04BDh
    times 6 dd 0ffffffffh
iend
```
  * the StringFileInfo structure accepts standard values, but also unknown strings, empty strings and duplicates.
```
__string 'FileVersion', 'compulsory for version tab'
__string 'LegalCopyright', 'corkami.com'
__string 'StringFileInfo', ''
__string 'Comments', ''
__string 'CompanyName', ''
__string 'InternalName', ''
__string 'LegalTrademarks', ''
__string 'OriginalFilename', ''
__string 'PrivateBuild', ''
__string 'ProductName', ''
__string 'ProductVersion', ''
__string 'SpecialBuild', ''
__string '', ''
__string ' ', ''
__string ' ** EAT AT JOE"S **', 'best hamburger in town'
__string 'FileVersion', 'duplicates are authorized'
```

#### Manifest ####
  * Manifest are XML resource that store informations about the executable.
  * Manifest presence can be checked by `kernel32.CreateActCtxA`
  * the minimal Manifest is `<assembly xmlns='urn:schemas-microsoft-com:asm.v1' manifestVersion='1.0'/>`
    * XP SP2 used to BSOD with a simple manifest PE
  * a PE with an incorrect manifest resource won't run
    * a manifest with an invalid type will be ignored, thus the file will run.
      * `manifest_broken` has an invalid manifest with an invalid type, so it's ignored altogether
```
...
MYMAN equ 3 ; incorrect - this file wouldn't run if it's set to 1 or 2
...
    at IMAGE_RESOURCE_DIRECTORY_ENTRY.NameID, dd MYMAN
...
resource_data:
db "<assembly xmlns='urn:schemas-microsoft-com:asm.v1' manifestVersion='1.0'>" ; broken end
RESOURCE_SIZE equ $ - resource_data
...
```

### exception ###
  * used by 64b binaries for structured exception handling.
    * `exceptions` is a minimalist 64b PE that use exceptions.
> > > ![https://corkami.googlecode.com/svn/wiki/pics/PE_exceptions.png](https://corkami.googlecode.com/svn/wiki/pics/PE_exceptions.png)

  * the OS relies on the DataDirectory itself in memory.
    * `seh_change64` alterates the handler in memory, which is taken into account by the OS.
> > > ![https://corkami.googlecode.com/svn/wiki/pics/PE_seh_change64.png](https://corkami.googlecode.com/svn/wiki/pics/PE_seh_change64.png)

##### security #####
  * defines the offset and size of the digital certificate blob (as it can be appended **after** appended data itself).
  * the area covered by this data directory is not included in the signature checking. Thus, increasing the size of a certificate and appending some data that won't be checked is possible without breaking the signature. This leads to security problems if the file is an installer with a ZIP in appended data / last section, as ZIPs are parsed bottom-up.
  * moreover, it was possible to move the certificate so that its starts overlaps `IMAGE_DOS_HEADER.e_lfanew`, which could lead to an alternate `IMAGE_NT_HEADERS` being used - thus, a different PE - while the signature is still valid.

### relocations ###
  * relocations are used when the PE is loaded at a different ImageBase. Hardcoded Addresses such as `push <addr>` and `call [<addr>]` have to be adjusted.
  * at least 1 relocation is required and used for .Net files, on the EntryPoint jump `FF25 xxxxxxxx: jmp [_CorExeMain]`
  * relocations are not used (even with corrupted ones) if the PE can be loaded at the expected address.
    * `fakerelocs.exe` contains fake unused relocations:

> > <wiki:gadget url=https://corkami.googlecode.com/svn/wiki/gadgets/flash.xml up\_File=https://corkami.googlecode.com/svn/wiki/pics/wink/PE\_fakerelocs.swf up\_FlashHeight=289 up\_FlashWidth=331 up\_ContainerCol="#d1dae3" height=289 width=331 title="" border=0/>
```
    Directory_Entry_Basereloc:
    ...
        dw (IMAGE_REL_BASED_HIGHLOW << 12) | 0ffh
        dw 0
        dw (IMAGE_REL_BASED_HIGHLOW << 12) | (here - reloc01)
    here:
        dw (IMAGE_REL_BASED_HIGHLOW << 12) | (here - reloc01)
    ...
```
  * relocations size is compulsory, to tell the loader when to stop (when they're used).
    * `dll.dll` contains simple relocations:
```
    at IMAGE_DATA_DIRECTORY_16.FixupsVA,   dd Directory_Entry_Basereloc - IMAGEBASE
    at IMAGE_DATA_DIRECTORY_16.FixupsSize, dd DIRECTORY_ENTRY_BASERELOC_SIZE
...
reloc01:
    push detach
reloc22:
    call [__imp__printf]
...
Directory_Entry_Basereloc:
block_start0:
    .VirtualAddress dd reloc01 - IMAGEBASE
    .SizeOfBlock dd BASE_RELOC_SIZE_OF_BLOCK0
    dw (IMAGE_REL_BASED_HIGHLOW << 12) | (reloc01 + 1 - reloc01)
    dw (IMAGE_REL_BASED_HIGHLOW << 12) | (reloc22 + 2 - reloc01)
BASE_RELOC_SIZE_OF_BLOCK0 equ $ - block_start0
...
DIRECTORY_ENTRY_BASERELOC_SIZE  equ $ - Directory_Entry_Basereloc
```

  * TLS structures and callbacks have to be relocated as well
    * `tls_reloc.exe` has a relocated TLS:
> > ![https://corkami.googlecode.com/svn/wiki/pics/PE_tls_reloc.png](https://corkami.googlecode.com/svn/wiki/pics/PE_tls_reloc.png)
```
    ...
    reloc40:
        AddressOfIndex        dd SizeOfZeroFill
    reloc50:
        AddressOfCallBacks    dd CallBacks

    ...

    CallBacks:
    reloc60:
        dd tls
```

  * IMAGE\_REL\_BASED\_ABSOLUTE doesn't do anything, used for padding.
  * IMAGE\_REL\_BASED\_HIGHLOW is the 'standard' relocation, for which `<real base> - ImageBase` will be added to the pointed address
  * IMAGE\_REL\_BASED\_HIGH does the same but only the highest WORD of the delta is added. (in short, same operation one WORD earlier)
  * IMAGE\_REL\_BASED\_LOW does the same but only the lowest WORD of the delta is added, which makes no change, as both bases are 10000h-aligned.
  * IMAGE\_REL\_BASED\_HIGHADJ (type 4) is the only one to require a parameter. However, it's buggy, so the parameter is ignored. This behavior was fixed under Windows 8.
  * IMAGE\_REL\_BASED\_MIPS\_JMPADDR16 (type 9) affects 4 bytes under Windows XP, affects 16 bytes under Windows 7, and is not supported by Windows 8.
  * IMAGE\_REL\_BASED\_HIGH3ADJ (type 11) was only supported until Windows 2000.
  * Other sort of relocations such as Mips and 64 bits are still supported by Windows 7, even if the PE specifies an x86 CPU.
    * `reloccrypt.exe` uses MIPS relocations (thus, involving masking and shifting):
> > ![https://corkami.googlecode.com/svn/wiki/pics/PE_reloccrypt.png](https://corkami.googlecode.com/svn/wiki/pics/PE_reloccrypt.png)
```
...
block_start:
    .VirtualAddress dd VDELTA + relocated_reloc - IMAGEBASE
...
    dw (IMAGE_REL_BASED_MIPS_JMPADDR << 12)
...

relocated_reloc:
    .SizeOfBlock dd (BASE_RELOC_SIZE_OF_BLOCK0 - 40002h - (0fc000000h + (20000h >> 2))) & 0ffffffffh
```

  * forcing the binary to relocate to a known place (ex, with a null or kernel ImageBase) makes it possible for the relocations to have predictable behavior. Thus, they can be used to decrypt or clear some values in memory. Also, relocations may be used to modify themselves.
    * `reloccrypt.exe` has a 0ffff0000h ImageBase, and its relocations are, first, modifying themselves, using uncommon relocation types, then decrypting the code to be executed.
```
EntryPoint:
reloc01:            ;68h push VDELTA + msg
crypt168 db 0
    dd VDELTA + msg
...
Directory_Entry_Basereloc:
; this block will fix the SizeOfBlock of the next block
block_start:
    .VirtualAddress dd VDELTA + relocated_reloc - IMAGEBASE
    .SizeOfBlock dd BASE_RELOC_SIZE_OF_BLOCK
    dw (IMAGE_REL_BASED_HIGHLOW << 12) ; + 10000h
    dw (IMAGE_REL_BASED_ABSOLUTE << 12)
    dw (IMAGE_REL_BASED_HIGHLOW << 12) ; + 10000h
    dw (IMAGE_REL_BASED_HIGHADJ << 12)
    dw (IMAGE_REL_BASED_HIGH    << 12) ; + 00001h
    dw (IMAGE_REL_BASED_LOW     << 12) ; + 0
    dw (IMAGE_REL_BASED_SECTION << 12)
    dw (IMAGE_REL_BASED_REL32 << 12)
...
;this block is actually the genuine relocations
block_start0:
    .VirtualAddress dd VDELTA + reloc01 - IMAGEBASE
relocated_reloc:
    .SizeOfBlock dd BASE_RELOC_SIZE_OF_BLOCK0 - 40002h
...
%macro cryptblock 2
block_start%1:
    .VirtualAddress dd VDELTA + %1 - IMAGEBASE
    .SizeOfBlock dd BASE_RELOC_SIZE_OF_BLOCK%1
    dw (IMAGE_REL_BASED_ABSOLUTE << 12)
    times %2 / 2 dw (IMAGE_REL_BASED_HIGH << 12)
BASE_RELOC_SIZE_OF_BLOCK%1 equ $ - block_start%1
%endmacro

;these blocks are the ones to implement the decryption
cryptblock crypt168, 068h
...
```

  * if the PE is loaded at a different address, and no relocations are present, execution still happens
    * on 64b, code can be RIP-relative, which removes the need from relocations altogether.
      * some system files such as `TDI.SYS` still have a relocation Data directory, but with no relocation block inside.
      * `ibknoreloc64.exe` has a kernel-range ImageBase, which implies relocation will happen, but uses only RIP-relative code.
> > > > ![https://corkami.googlecode.com/svn/wiki/pics/PE_ibknoreloc64.png](https://corkami.googlecode.com/svn/wiki/pics/PE_ibknoreloc64.png)
```
IMAGEBASE equ 0fffffffffff0000h
...
lea ecx, [rel Msg]
call [rel __imp__printf]
```
    * if the file knows where it will be relocated, it can apply its relocation manually in advance. The code will look wrong on disk, but ok in memory.
      * `ibkmanual.exe` uses such a technique of pre-relocated code

> > <wiki:gadget url=https://corkami.googlecode.com/svn/wiki/gadgets/flash.xml up\_File=https://corkami.googlecode.com/svn/wiki/pics/wink/PE\_ibkmanual.swf up\_FlashHeight=151 up\_FlashWidth=464 up\_ContainerCol="#d1dae3" height=151 width=464 title="" border=0/>
```
    IMAGEBASE equ 0FFFF0000h
    ...
    push msg + 20000h
    call [__imp__printf +  20000h]
    ...
```
    * applying manually an extra relocations on the ImageBase field itself doesn't change the memory mapping, as the used value was read from disk, and relocations are done in memory. However, once the PE is mapped, relocations are performed, altering the ImageBase in memory, and thus influencing the value of the EntryPoint (for it to work, a writeable header is required, thus low alignments)
      * `ibreloc.exe` is a low alignment PE with a altered EntryPoint and an extra relocation on the ImageBase field.
> > > ![https://corkami.googlecode.com/svn/wiki/pics/PE_ibreloc.png](https://corkami.googlecode.com/svn/wiki/pics/PE_ibreloc.png)
```
...
IMAGEBASE equ 0FFFF0000h
DELTA equ 20000h
...
    at IMAGE_OPTIONAL_HEADER32.AddressOfEntryPoint,       dd EntryPoint - IMAGEBASE + DELTA
reloc00:
    at IMAGE_OPTIONAL_HEADER32.ImageBase,                 dd IMAGEBASE
...
Directory_Entry_Basereloc:
block_start1:
    .VirtualAddress dd reloc00 - IMAGEBASE
    .SizeOfBlock dd BASE_RELOC_SIZE_OF_BLOCK1
    dw (IMAGE_REL_BASED_HIGHLOW << 12) | 0
BASE_RELOC_SIZE_OF_BLOCK1 equ $ - block_start1
...
```

  * applying a relocation on `e_lfanew` alters the remaining part of the loading process: under Vista and later, the remaining DataDirectories such as imports are parsed after relocations are processed. If e\_lfanew is relocated, then a different PE Header (almost empty) can be used, thus an entirely different set of DataDirectories can be used.


> ![https://corkami.googlecode.com/svn/wiki/pics/lfanew_relocSCHEMA.png](https://corkami.googlecode.com/svn/wiki/pics/lfanew_relocSCHEMA.png)
    * `lfanew_relocW7.exe` implements such a trick:
> ![https://corkami.googlecode.com/svn/wiki/pics/lfanew_reloc.png](https://corkami.googlecode.com/svn/wiki/pics/lfanew_reloc.png)

  * combining a relocation type 4 on top of a relocation type 9 (to turn it into a type 10 under Windows 8) gives a different loader behavior under Windows XP, 7 and 8. `relocOSdet.exe` implements such a mechanism:
```
...
    dw (IMAGE_REL_BASED_HIGHADJ  << 12) | (reloc4 + 1 - relocbase), -1 ; +1 to modify the Type
...
reloc4:
    dw (IMAGE_REL_BASED_MIPS_JMPADDR16 << 12) | 0d00h ; => type,offset: 9,f00 under XP/W7 and a,000 under W8
```

### debug ###
  * used for storing debug symbols
  * its DataDirectory size tells the loader how many blocks are available. Thus, it is taken into account.
    * under XP, the value of the Debug directory **SIZE** can't be random - even if the RVA is null !

### copyright ###
  * Watcom compilers makes it point to a string. Useless whatsoever.

##### GlobalPtr #####
  * used in Itanium to store global pointers to variables. Useless whatsoever.

### TLS ###
  * most values of TLS structures are not required:
    * `tls.exe` executes TLS code with the following structure
```
    at IMAGE_DATA_DIRECTORY_16.TLSVA,       dd Image_Tls_Directory32 - IMAGEBASE
...
Image_Tls_Directory32:
    StartAddressOfRawData dd 0
    EndAddressOfRawData   dd 0
    AddressOfIndex        dd some_value
    AddressOfCallBacks    dd CallBacks
    SizeOfZeroFill        dd 0
    Characteristics       dd 0
...
some_value dd 012345h

CallBacks:
    dd tls
    dd 0
...
tls:
   <...>
```
  * The callbacks are VAs, not RVAs (ImageBase **is** included).
  * each callback is executed until an error happens or a null dword is next in the list. then, no matter what happened (error or not) the EntryPoint is executed:
    * a TLS doesn't need to return cleanly if it knows it's the last one
      * [CoST](x86oddities.md) uses this technique
    * an incorrect entry in the list doesn't trigger a visible error
      * `tls_obfuscation.exe` has many fake TLS callback entries to disrupt disassembly
> > > ![https://corkami.googlecode.com/svn/wiki/pics/PE_tls_obfuscation.png](https://corkami.googlecode.com/svn/wiki/pics/PE_tls_obfuscation.png)
  * like the entrypoint value, a callback VA is blindly called. It can be:
    * outside the PE, in a known in advance address
    * pointing an Import Address Table entry, which means an API will be called with ImageBase as parameter.
      * `tls_import.exe` executes `mz.exe` via a call to WinExec through a TLS callback in its IAT.
> > > ![https://corkami.googlecode.com/svn/wiki/pics/PE_tls_import.png](https://corkami.googlecode.com/svn/wiki/pics/PE_tls_import.png)
  * under XP, TLS are only executed with staticly loaded DLL, not dynamicly loaded ones.
  * on XP, TLS are executed twice, on process start and process termination. Thus, code **is** executed even after a call to ExitProcess.

> This is true even under Windows 7, however libraries such as user32.dll might be already unloaded, preventing code using it to work normally.
  * TLS callbacks are not executed on thread start if no DLL importing kernel32 is imported. Thus, only execution on thread stop if kernel32 is the only import.
    * `tls_k32` only has imports to kernel32, and a TLS that is ignored on thread start and used on thread stop, while the EntryPoint code is meaningless.
> > > ![https://corkami.googlecode.com/svn/wiki/pics/PE_tls_k32.png](https://corkami.googlecode.com/svn/wiki/pics/PE_tls_k32.png)
    * this behavior might be altered if the OS or debugger is loading an extra DLL.
  * TLS callbacks' list is updated at each callback execution. If a TLS or the EntryPoint code add or remove an entry, it will be taken into consideration
    * `tls_onthefly`'s first TLS adds a second one on the list that will be executed directly after the first one is over

> > ![https://corkami.googlecode.com/svn/wiki/pics/PE_tls_onthefly.png](https://corkami.googlecode.com/svn/wiki/pics/PE_tls_onthefly.png)
```
tls:
...
    mov dword [CallBacks + 4], tls2
    retn
...

tls2:
...
```
  * if a callback calls ExitProcess, the EntryPoint won't be called, however the callback will be executed after the ExitProcess call
    * `tls_exiting.exe` contains an exiting TLS callback, preventing the EntryPoint code to be ever called.
    * `tls_noEP.exe` contains no EntryPoint, its callback calls ExitProcess, then the first callback is called again.
> > ![https://corkami.googlecode.com/svn/wiki/pics/PE_tls_noep.png](https://corkami.googlecode.com/svn/wiki/pics/PE_tls_noep.png)
```
...
    at IMAGE_OPTIONAL_HEADER32.AddressOfEntryPoint,       dd 0 - IMAGEBASE
...
tls:
...
    mov dword [CallBacks], tls2
...
    call [__imp__ExitProcess]
...

tls2:
    push TLSEnd
    call [__imp__printf]
```

  * TLS AddressOfIndex is cleared on loading. Thus, it can be used to modify code execution.
    * `tls_aoi` patches the operand of a looping jump by pointing AddressOfIndex to it.
> > ![https://corkami.googlecode.com/svn/wiki/pics/PE_tls_aoi.png](https://corkami.googlecode.com/svn/wiki/pics/PE_tls_aoi.png)
```
AddressOfIndex equ $ + 1
    jmp long $
```

  * like DllMain, after TLS execution, only `ESI` needs to be correct. the rest doesn't matter, including ESP, which could easily crash an emulator.
    * `fakeregs` corrupts all registers except ESI, on DllMain and TLS execution.

##### LoadConfig #####
  * stores various OS related flags, including the SafeSEH structure, which prevents the execution of an exception handler by terminating the process if that handler is not in the Handler table. This is an OS mechanism, not specific to a compiler.
    * `safeseh.exe` defines a minimal SafeSEH structure manually
```
    at IMAGE_DATA_DIRECTORY_16.Load,      dd LoadConfig - IMAGEBASE, 40h ; fixed XP value?
    ...

LoadConfig:
istruc IMAGE_LOAD_CONFIG_DIRECTORY32
    at IMAGE_LOAD_CONFIG_DIRECTORY32.Size,           dd IMAGE_LOAD_CONFIG_DIRECTORY32_size
...
    at IMAGE_LOAD_CONFIG_DIRECTORY32.SecurityCookie, dd cookie
    at IMAGE_LOAD_CONFIG_DIRECTORY32.SEHandlerTable, dd HandlerTable
    at IMAGE_LOAD_CONFIG_DIRECTORY32.SEHandlerCount, dd HANDLERCOUNT
iend

HandlerTable:
    ...
    HANDLERCOUNT equ ($ - HandlerTable) / 4
```

  * the handler table is not protected, it's just aiming at preventing a blind exception handler change
    * `safeseh_fly.exe` sets a SEH on the fly and uses it:
```
...
    mov dword [HandlerTable], Handler - IMAGEBASE
    int3
...
```
  * `ldrsnaps` sets its `FLG_SHOW_LDR_SNAPS` GlobalFlags and checks it (also works in 64 bits with `ldrsnaps64`
```
...
    call [__imp__RtlGetNtGlobalFlags]
...
    at IMAGE_LOAD_CONFIG_DIRECTORY32.GlobalFlagsSet, dd FLG_SHOW_LDR_SNAPS
...
```

### Bound imports ###
  * are a shortcut structure to hardcode some imports values in advance, to make import values faster

> ![https://corkami.googlecode.com/svn/wiki/pics/PE_boundexports.png](https://corkami.googlecode.com/svn/wiki/pics/PE_boundexports.png)
  * all the loader does is take a filename, compare the timestamp of the file and the one included in the bound imports table, then use the VA directly as import if they match.
    * `dllbound-ld.exe` loads and execute 'dllbound.dll' via bound imports.
```
  at IMAGE_DATA_DIRECTORY_16.BoundImportsVA,   dd BoundImports - IMAGEBASE
  ...
  BoundImports:
  dd 31415925h ; timestamp of the bound DLL
  dw bounddll - BoundImports ; it's a WORD relative offset :(
  dw 0
  ...
  bounddll db 'dllbound.dll', 0 ; we have to duplicate locally this string... :(
  ...
  dll.dll_iat:
  __imp__export:
     dd 01001008h ;VA of the export of the loaded DLL
```

  * thus, replacing the RVA in the bound import table is an easy way to redirect imports.
    * `dllbound-redirld.exe` will load the wrong import of `dllbound.dll` because one RVA has been changed.
```
dll.dll_iat:
__imp__export:
   dd 01001018h ; corrupted VA of the import
```

  * under XP only, it's even possible to put a different filename and timestamp. a completely different DLL will be used no matter what the standard import table says.
    * `dllbound-redirldXP.exe` will load the wrong dll `dllbound2.dll`, as the name and timestamp have been modified.
```
BoundImports:
dd 27182818h ; timestamp of the hijacking DLL
dw bounddll - BoundImports
dw 0

;terminator
dd 0, 0

bounddll db 'dllbound2.dll', 0 ; hijacking DLL name
```

### Import table ###
  * the RVA **and** the Size required to be set on a low alignment PE to make the import table writeable, under XP.
    * `nosectionXP.exe` needs an IAT to make its imports writeable
> > ![https://corkami.googlecode.com/svn/wiki/pics/PE_nosectionXP.png](https://corkami.googlecode.com/svn/wiki/pics/PE_nosectionXP.png)

### delay imports ###
  * is a rip-off, a fake structure.
    * `delay_corrupt.exe` contains completely corrupted delay imports, which doesn't alterate the file loading and running.
  * is just a trampoline added by the compiler to load imports and DLL on request,
> > with a 'frontend' in the data directories, with a structure similar to standard imports (adress table + name table) so that external tools can still indicate that imports calls are present.
    * `delayimports.exe` has working delay imports (displayed by other tools):
> > ![https://corkami.googlecode.com/svn/wiki/pics/PE_delayimports.png](https://corkami.googlecode.com/svn/wiki/pics/PE_delayimports.png)
```
...
at IMAGE_DATA_DIRECTORY_16.DelayImportsVA, dd delay_imports - IMAGEBASE
at IMAGE_DATA_DIRECTORY_16.DelayImportsSize, dd DELAY_IMPORTS_SIZE
...
delay_imports:
istruc _IMAGE_DELAY_IMPORT_DESCRIPTOR
    at _IMAGE_DELAY_IMPORT_DESCRIPTOR.rvaDLLName,   dd msvcrt.dll
    at _IMAGE_DELAY_IMPORT_DESCRIPTOR.rvaIAT,       dd delay_iat - IMAGEBASE
    at _IMAGE_DELAY_IMPORT_DESCRIPTOR.rvaINT,       dd msvcrt_int
iend
istruc _IMAGE_DELAY_IMPORT_DESCRIPTOR
iend

delay_iat:
__imp__printf:
    dd __delay__printf
    dd 0
```
  * the information in the header is not required to make delay import works, are they are extra code added **in** the file by the compiler.
    * Erasing the data directory VA from a standard file with delay imports will not disturb its execution
    * `delaycorrupt.exe` has the same structure as `delayimports.exe`, but the descriptors are empty.
> > ![https://corkami.googlecode.com/svn/wiki/pics/PE_delaycorrupt.png](https://corkami.googlecode.com/svn/wiki/pics/PE_delaycorrupt.png)
```
...
delay_imports:
istruc _IMAGE_DELAY_IMPORT_DESCRIPTOR
iend
...
```

##### COM Runtime #####
  * not covered here
  * used in .Net files to store information of the actual .Net structures

###### reserved ######
  * used by packers and malware as markers

## section table ##
  * the section table doesn't have to be fully covered by SizeOfHeaders. (cf `truncsectbl.exe`)

##### Name #####
  * no importance whatsoever

### Misc\_VirtualSize ###
  * `bigsec` has a section with a virtual size of 0x10001000 (and executes code at the bottom of it).

> ![https://corkami.googlecode.com/svn/wiki/pics/PE_bigsec.png](https://corkami.googlecode.com/svn/wiki/pics/PE_bigsec.png)
```
...
extravirtualspace equ 010000h
    at IMAGE_OPTIONAL_HEADER32.SizeOfImage,               dd (2 + extravirtualspace) * SECTIONALIGN
...
    at IMAGE_SECTION_HEADER.VirtualSize,      dd (1 + extravirtualspace) * SECTIONALIGN
...
EntryPoint:
VirtualStart equ (extravirtualspace - 1) * SECTIONALIGN + IMAGEBASE
    mov edi, VirtualStart

    ; 68 xxxxxxxx push Msg
    mov al, 68h
        stosb
    mov eax, Msg
        stosd
...
    jmp VirtualStart
```
  * a section can have a null VirtualSize: in this case, only the SizeOfRawData is taken into consideration.
    * `nullvirt` has a section with a null VirtualSize and a non-null SizeOfRawData.

### VirtualAddress ###
  * sections have to be in increasing order, virtually.
  * A section can start before the previous one ends. Which means that offset-wise, the address is not constantly increasing.
    * `secinsec.exe` has a 1x-sized section inside the previous 3x-sized section
```
SectionHeader:
istruc IMAGE_SECTION_HEADER
    at IMAGE_SECTION_HEADER.VirtualSize,      dd 1 * SECTIONALIGN
    at IMAGE_SECTION_HEADER.VirtualAddress,   dd 1 * SECTIONALIGN
    at IMAGE_SECTION_HEADER.SizeOfRawData,    dd 3 * FILEALIGN
    at IMAGE_SECTION_HEADER.PointerToRawData, dd 1 * FILEALIGN
iend
istruc IMAGE_SECTION_HEADER
    at IMAGE_SECTION_HEADER.VirtualSize,      dd 1 * SECTIONALIGN
    at IMAGE_SECTION_HEADER.VirtualAddress,   dd 2 * SECTIONALIGN
    at IMAGE_SECTION_HEADER.SizeOfRawData,    dd 1 * FILEALIGN
    at IMAGE_SECTION_HEADER.PointerToRawData, dd 2 * FILEALIGN
iend
```
  * sections don't have to be virtually contiguous
    * `virtgap` introduces a gap of 10000000h between 2 sections.
> > ![https://corkami.googlecode.com/svn/wiki/pics/PE_virtgap.png](https://corkami.googlecode.com/svn/wiki/pics/PE_virtgap.png)

### SizeOfRawData ###
  * with standard alignments, sections can be physically empty.
  * the last section doesn't have to be physically aligned in size, cf `truncatedlast.exe`
  * if bigger than virtual size, then virtual size is taken.
    * `bigSoRD.exe` has an inflated section
> > ![https://corkami.googlecode.com/svn/wiki/pics/PE_bigsord.png](https://corkami.googlecode.com/svn/wiki/pics/PE_bigsord.png)
```
      at IMAGE_SECTION_HEADER.SizeOfRawData,    dd 1 * FILEALIGN + 0ffff0000h
```

### PointerToRawData ###
  * sections can be physically overlapping.
    * dupsec has 2 identical sections (besides the VirtualAddress)
> > ![https://corkami.googlecode.com/svn/wiki/pics/PE_dupsec.png](https://corkami.googlecode.com/svn/wiki/pics/PE_dupsec.png)
```
SectionHeader:
istruc IMAGE_SECTION_HEADER
    at IMAGE_SECTION_HEADER.VirtualSize,      dd 1 * SECTIONALIGN
    at IMAGE_SECTION_HEADER.VirtualAddress,   dd 1 * SECTIONALIGN
    at IMAGE_SECTION_HEADER.SizeOfRawData,    dd 1 * FILEALIGN
    at IMAGE_SECTION_HEADER.PointerToRawData, dd 1 * FILEALIGN
    at IMAGE_SECTION_HEADER.Characteristics,  dd IMAGE_SCN_MEM_EXECUTE | IMAGE_SCN_MEM_WRITE
iend
istruc IMAGE_SECTION_HEADER
    at IMAGE_SECTION_HEADER.VirtualSize,      dd 1 * SECTIONALIGN
    at IMAGE_SECTION_HEADER.VirtualAddress,   dd 2 * SECTIONALIGN
    at IMAGE_SECTION_HEADER.SizeOfRawData,    dd 1 * FILEALIGN
    at IMAGE_SECTION_HEADER.PointerToRawData, dd 1 * FILEALIGN
    at IMAGE_SECTION_HEADER.Characteristics,  dd IMAGE_SCN_MEM_EXECUTE | IMAGE_SCN_MEM_WRITE
iend
```
  * if a section starts at offset 0, it's invalid.
  * if a section's physical start is lower than 200h (the lower limit for standard alignment), it is rounded down to 0. Thus, it's a legitimate way to map the header.
    * `duphead.exe` maps the header in a section via rounding down it's physical start:
> > ![https://corkami.googlecode.com/svn/wiki/pics/PE_duphead.png](https://corkami.googlecode.com/svn/wiki/pics/PE_duphead.png)
```
    at IMAGE_SECTION_HEADER.PointerToRawData, dd 1ffh ; upper limit of the down-rounding trick
```
  * sections can be in wrong order physically
    * `shuffledsect.exe` has sections in a wrong physical order.
> > ![https://corkami.googlecode.com/svn/wiki/pics/PE_shuffled.png](https://corkami.googlecode.com/svn/wiki/pics/PE_shuffled.png)
```
SectionHeader:
istruc IMAGE_SECTION_HEADER
    at IMAGE_SECTION_HEADER.VirtualSize,      dd 1 * SECTIONALIGN
    at IMAGE_SECTION_HEADER.VirtualAddress,   dd 1 * SECTIONALIGN
    at IMAGE_SECTION_HEADER.SizeOfRawData,    dd FILEALIGN
    at IMAGE_SECTION_HEADER.PointerToRawData, dd 2 * FILEALIGN
...
iend
istruc IMAGE_SECTION_HEADER
    at IMAGE_SECTION_HEADER.VirtualSize,      dd 1 * SECTIONALIGN
    at IMAGE_SECTION_HEADER.VirtualAddress,   dd 2 * SECTIONALIGN
    at IMAGE_SECTION_HEADER.SizeOfRawData,    dd FILEALIGN
    at IMAGE_SECTION_HEADER.PointerToRawData, dd 1 * FILEALIGN
```
  * sections can leave a physically unused space in the PE
    * `slackspace.exe` has 2 section leaving an unused physical space in between
> > ![https://corkami.googlecode.com/svn/wiki/pics/PE_slackspace.png](https://corkami.googlecode.com/svn/wiki/pics/PE_slackspace.png)
```
...
SectionHeader:
istruc IMAGE_SECTION_HEADER
    at IMAGE_SECTION_HEADER.VirtualSize,      dd SECTIONALIGN
    at IMAGE_SECTION_HEADER.VirtualAddress,   dd SECTIONALIGN
    at IMAGE_SECTION_HEADER.SizeOfRawData,    dd FILEALIGN
    at IMAGE_SECTION_HEADER.PointerToRawData, dd FILEALIGN
...
iend
istruc IMAGE_SECTION_HEADER
    at IMAGE_SECTION_HEADER.VirtualSize,      dd SECTIONALIGN
    at IMAGE_SECTION_HEADER.VirtualAddress,   dd SECTIONALIGN * 2
    at IMAGE_SECTION_HEADER.SizeOfRawData,    dd FILEALIGN
    at IMAGE_SECTION_HEADER.PointerToRawData, dd 3 * FILEALIGN
...
```
  * the physically-last section defines the appended data. However, it's easy to 'hide' appended data by either:
    * adding a fake extra section (even physically one byte), such as in `hiddenappdata1` (creating some slackspace)
> > > ![https://corkami.googlecode.com/svn/wiki/pics/PE_hiddenappdata1.png](https://corkami.googlecode.com/svn/wiki/pics/PE_hiddenappdata1.png)
    * enlarging the physically-last section, such as in `hiddenappdata2`

##### PointerToRelocations/PointerToLinenumbers/NumberOfRelocations/NumberOfLinenumbers #####
  * no importance whatsoever

### Characteristics ###
  * to be detailed here later - check the examples.

# extra #
## 64b ##
a 64b PE (PE32+) is like a 32b PE, except that the FILE HEADER's Machine and the OPTIONAL HEADER's Magic have AMD specific values, and the Imports's INT, as well as the ImageBase, and the Stack and Heap info are QWORD (which drops a few fields from the Optional header as a consequence

![https://corkami.googlecode.com/svn/wiki/pics/PE_normal64.png](https://corkami.googlecode.com/svn/wiki/pics/PE_normal64.png)

```
...
    at IMAGE_FILE_HEADER.Machine,     dw IMAGE_FILE_MACHINE_AMD64
...
    at IMAGE_OPTIONAL_HEADER64.Magic, dw IMAGE_NT_OPTIONAL_HDR64_MAGIC
...
  .ImageBase                    resq 1
...
  .SizeOfStackReserve           resq 1
  .SizeOfStackCommit            resq 1
  .SizeOfHeapReserve            resq 1
  .SizeOfHeapCommit             resq 1
  .LoaderFlags                  resd 1
...
kernel32.dll_hintnames:
    dq hnExitProcess - IMAGEBASE
    dq 0
```

## minimal sizes ##
the minimal size for a PE is:
  * 97 bytes, under XP: `tinyXP`. In this case, the OptionalHeader is truncated.

> ![https://corkami.googlecode.com/svn/wiki/pics/PE_tinyXP.png](https://corkami.googlecode.com/svn/wiki/pics/PE_tinyXP.png)
    * the same rule applies to drivers
> > > ![https://corkami.googlecode.com/svn/wiki/pics/PE_tinydrvxp.png](https://corkami.googlecode.com/svn/wiki/pics/PE_tinydrvxp.png)
    * and DLLs: `tinydllXP`
  * 252 bytes, under Vista/Windows 7 (the same as under XP, with null padding): `tinyW7` - as the OS now enforces a minimum of physical space after the start of the OptionalHeader.
  * 268 bytes, under Vista/Windows 7 (same again, just null bytes to get the required size, but the same elements as `tinyXP`)
    * in 32b, `tinyW7_3264`
    * in 64b, `tinyW7x64`
> > > ![https://corkami.googlecode.com/svn/wiki/pics/PE_tinw7x64.png](https://corkami.googlecode.com/svn/wiki/pics/PE_tinw7x64.png)

> so, 268 bytes is the smallest size for a universal tiny PE.

> ![https://corkami.googlecode.com/svn/wiki/pics/PE_tiny.png](https://corkami.googlecode.com/svn/wiki/pics/PE_tiny.png)


---


# specific cases #

## folded header ##
  * found out by Reversing Labs as 'Dual PE Header'
  * the first checks of the PE are done on file. then the file is loaded in memory. then imports are resolved, from the image in memory.
  * by extending the header until the first SectionAlignment, it's possible to have the first section overlapping the header partially.
> thus, the actual data directories used for imports resolving will not be the contiguous ones on the disk.
> > ![https://corkami.googlecode.com/svn/wiki/pics/PE_foldedhdr.png](https://corkami.googlecode.com/svn/wiki/pics/PE_foldedhdr.png)
```c

```
...
 section progbits vstart=IMAGEBASE + SECTIONALIGN align=FILEALIGN

;---------------------------------------------- CUT and FOLD here ----------------------------------

; ok here we're at RVA 1000h (start of 1st section),
; so this data will overwrite the PE headers originally loaded from offset F80h, physically further in the file.

dd Import_Descriptor - IMAGEBASE, 0
...
times 0f80h - FILEALIGN * 2 db 0 ; to make the header overlap on address offset/rva 1000h

NT_Signature:
istruc IMAGE_NT_HEADERS
    at IMAGE_NT_HEADERS.Signature, db 'PE', 0, 0
iend
...
istruc IMAGE_DATA_DIRECTORY_16
dd 88660001h,010009988h
;---------------------------------------------- CUT and FOLD here ----------------------------------
; we cut the header here, and we're right at offset 1000h
;---------------------------------------------- CUT and FOLD here ----------------------------------

dd 86600010h,001000998h
dd 66000100h,000100099h
dd 6000100Fh,0F0010009h
...
```
```

## PE + PDF + ZIP ##

> ![https://corkami.googlecode.com/svn/wiki/pics/PE_pdf_zip.png](https://corkami.googlecode.com/svn/wiki/pics/PE_pdf_zip.png)

  * PDF in/and ZIP:
    * PDF documents don't need to have their signature starting at offset 0
    * in a ZIP archive with no compression, the file is stored in its original form

So it's possible to make a ZIP containing a PDF that also works as the PDF itself.

  * ZIP archives also don't need to start at offset 0
    * so a PE can contain such a ZIP, and work as both a PE and a ZIP
  * moving the 'PE' headers far enough via increasing `e_lfanew` enables to store any size of file in the PE

so it's possible to make a PE containing a ZIP containing a uncompressed PDF, that works as both a document (PDF), an archive (ZIP), and an executable (PE), like `pdf_zip_pe.exe`.

File formats should have enforce their signature at offset 0.

### file walkthrough ###
  1. start of the file (as a PE):
```
00000000:  4D 5A 00 00 00 00 00 00 00 00 00 00 00 00 00 00  MZ..............
```
  1. start of the ZIP archive:
```
00000030:        50 4B 03 04 0A 00 00 00 00 00 74 01 00 00    PK???.....t?..\
```
  1. e\_lfanew pointing to the PE header (PE) (overlapping ZIP's lastmod)
```
00000030:                                      74 01 00 00              t?..
```
  1. PDF signature (PDF):
```
00000050:                                            25 50                %P
00000060:  44 46 2D 31.2E 0A 31 20.30 20 6F 62.6A 3C 3C 2F  DF-1.?1 0 obj<</
```
  1. end of central directory (ZIP):
```
00000150:                                            50 4B                PK
00000160:  05 06 00 00.00 00 01 00.01 00 3C 00.00 00 F0 00  ??....?.?.<...=.
```
  1. PE header (PE):
```
00000170:              50 45 00 00.4C 01 00 00.00 00 00 00      PE  L?......
```

## quine ##
  * a quine is a file that prints its own source.
  * by putting the PE header further in the file and only using printable characters in between,
> `quine.exe` has its own source directly accessible via the _type_ command.
> after the source, an EOF character is inserted.
> > ![https://corkami.googlecode.com/svn/wiki/pics/PE_quine.png](https://corkami.googlecode.com/svn/wiki/pics/PE_quine.png)
```
db 'MZ'
align 3bh, db 0dh
dd nt_header - IMAGEBASE
    db 0dh

incbin 'quine.asm'
db 1ah
```
  * when executed, quine will open a command window to type itself.
```
op db "open", 0
fn db "cmd", 0
param db "/K type quine.exe", 0
...
EntryPoint:
    push 1
    push 0
    push param
    push fn
    push op
    push 0
    call [ShellExecuteA]
```

## tls\_aoiOSDET ##
  * the address pointed by TLS.AddressOfIndex is cleared on loading.
  * if IMAGE\_IMPORT\_DESCRIPTOR.Name is null, this import descriptor is considered a terminator (imports are no further parsed).

  * under XP, it's cleared after imports are parsed, thus the imports are completely loaded.
  * under W7, it's cleared before, so the descriptor is considered as a terminator, so imports are no further parsed.

so, depending on the OS that is running, `tls_aoiOSDET` behaves differently by checking its own imports
```
...
mov eax, [__imp__MessageBoxA]
cmp eax, hnMessageBoxA - IMAGEBASE
jz W7

...
;user32.dll_DESCRIPTOR:
    dd user32.dll_hintnames - IMAGEBASE
    dd 0, 0
AddressOfIndex:
    dd user32.dll - IMAGEBASE
    dd user32.dll_iat - IMAGEBASE
...
```

![https://corkami.googlecode.com/svn/wiki/pics/PE_tlsaoidet_w7.png](https://corkami.googlecode.com/svn/wiki/pics/PE_tlsaoidet_w7.png) ![https://corkami.googlecode.com/svn/wiki/pics/PE_tlsaoidet_xp.png](https://corkami.googlecode.com/svn/wiki/pics/PE_tlsaoidet_xp.png)

### manyimportsW7 ###
  * this W7-only binary use the TLS AoI trick to clean its imports. On disk, the import table is full of bogus descriptors, which will be ignored on loading
```
Image_Tls_Directory32:
...
AddressOfIndex        dd zero_here_plz
...
zero_here_plz:
%rep 40000h
dd fake_imports - IMAGEBASE + i * 4
...
```

![https://corkami.googlecode.com/svn/wiki/pics/PE_manyimportsW7.png](https://corkami.googlecode.com/svn/wiki/pics/PE_manyimportsW7.png)

### no0code ###
  * by using a similar trick as in quine to make a printable DOS header, and relocating the NT HEADERS far enough so that e\_lfanew contains no null char, no0code contains no null byte before the code start.
```
db 'MZ'
align 3bh, db 90h
dd nt_header - IMAGEBASE
    db 90h
EntryPoint:
bits 32
...
times 01010000h db ' '
...
nt_header:
istruc IMAGE_NT_HEADERS
    at IMAGE_NT_HEADERS.Signature, db 'PE',0,0
```
  * however, the NT HEADERS magic contains necessarily 2 null bytes, thus it's impossible to have a working PE with no null byte at all.

## data PEs ##
some specific cases require PE files with less elements than otherwise mentioned

### Data Files ###
loading a file via LoadLibraryEx with LOAD\_LIBRARY\_AS\_DATAFILE needs a PE file with only a very few defined elements: not even the Subsystem or the Machine needs to be defined for such a ''library''.
  * `d_tiny.dll` is a 61 bytes PE with only 3 defined elements
> > ![https://corkami.googlecode.com/svn/wiki/pics/PE_tiny_dpe.png](https://corkami.googlecode.com/svn/wiki/pics/PE_tiny_dpe.png)

yet even if nothing is defined, its code can still be ran. This allows us to make a non-null PE with code.
  * `d_nonnull.dll` is a data PE with executed code and no null character
> > ![https://corkami.googlecode.com/svn/wiki/pics/PE_nonnull_dpe.png](https://corkami.googlecode.com/svn/wiki/pics/PE_nonnull_dpe.png)

### resources ###
A standard use for code-less PEs is to store resources. In this case, more fields are required (Machine, SizeOfOptionalHeader, SizeOfHeaders), but most fields can contain bogus values.
  * `d_resource.dll` is a PE with a lot of corrupted fields yet correct and usable resources
> > ![https://corkami.googlecode.com/svn/wiki/pics/PE_resourcedPE.png](https://corkami.googlecode.com/svn/wiki/pics/PE_resourcedPE.png)


---

# conclusion #
  * a PE executing code can have either no sections, no entrypoint, no data directories - but not at least one of the 3 is required - which breaks the typical model.
  * many fields are not relevant, and can have completely bogus values (even important-looking ones such as BaseOfCode, SizeOfCode...)
  * many fields are taken into account only once the PE is mapped in memory, which requires a different way of thinking (allocated areas, mapped sections...)
    * most fields are not checked for boundaries, even important ones such as EntryPoint, TLS Callbacks, imports, exports...
  * several fields enable loader-based decryption (relocations, exports)
  * TLS is arguably the most f`*`cked up part of the PE format.


---

# acknowledgements #
  * Peter Ferrie

  * Bernhard Treutwein
  * Costin Ionescu
  * Ivanlef0u
  * Kris Kaspersky
  * Moritz Kroll
  * Walied Assar

# extra resources #
&lt;wiki:gadget url="https://corkami.googlecode.com/svn/wiki/gadgets/imgur\_com101.xml" width=600 height=550 border=0/&gt;

&lt;wiki:gadget url="https://corkami.googlecode.com/svn/wiki/gadgets/imgur\_pe101.xml" width=600 height=550 border=0/&gt;

&lt;wiki:gadget url="https://corkami.googlecode.com/svn/wiki/gadgets/imgur\_pe102.xml" width=600 height=550 border=0/&gt;

[Binary Art - byte-ing the PE that fails you](hashdays2012.md), presented at Hashdays, Luzern, on the 3rd November 2012
&lt;wiki:gadget url="https://corkami.googlecode.com/svn/wiki/gadgets/hashdays2012\_slideshare.xml" width=595 height=497 border=0/&gt;

# external resources #
  * [Undocumented PE/COFF](http://www.reversinglabs.com/advisory/pecoff.php) _Reversing Labs_
  * [Microsoft's Rich Signature (undocumented)](http://ntcore.com/files/richsign.htm) _Daniel Pistelli_
  * [Maximum possible code execution in the PE header](http://pferrie.host22.com/misc/pehdr.htm) _Peter Ferrie_
  * [Virtual Code](http://spth.virii.lu/v3/vessel/display/articles/roy%20g%20biv/vcode2.txt) _Roy G Biv_

[<< index](http://code.google.com/p/corkami/) [Android/Java/x86/... opcodes tables](http://opcodes.corkami.com) [PDF tricks](http://pdf.corkami.com) [Portable Executable](http://pe.corkami.com) [x86 oddities](http://x86.corkami.com)