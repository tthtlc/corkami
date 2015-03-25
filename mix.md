_[CorkaMIX](http://code.google.com/p/corkami/downloads/detail?name=CorkaMIX.zip)_,
_[CorkaMInuX](http://code.google.com/p/corkami/downloads/detail?name=CorkaMInuX.zip)_ and
_[CorkaM-OsX](http://code.google.com/p/corkami/downloads/detail?name=CorkaM-OsX.zip)_ are respectively valid _Windows_, _Linux_ and _OS X_ binaries, and also a working PDF document, Jar (Zip + Class + manifest), and HTML + JavaScript files.

![https://corkami.googlecode.com/svn/wiki/pics/PE_corkamix.png](https://corkami.googlecode.com/svn/wiki/pics/PE_corkamix.png)
![https://corkami.googlecode.com/svn/wiki/pics/ELF_corkaminux.png](https://corkami.googlecode.com/svn/wiki/pics/ELF_corkaminux.png)
![https://corkami.googlecode.com/svn/wiki/pics/MACHO_corkamosx.png](https://corkami.googlecode.com/svn/wiki/pics/MACHO_corkamosx.png)

# About #
They serve no purpose, except proving that file formats not starting at offset 0 are a bad idea.

Many files (known as _polyglot_) already combines various languages in one file, however it's most of the time at source level, not at binary level.

If you're worried about malware, just remember that none of these files show anything new, and doesn't provide a methodology or a tool, as it's made entirely by hand, from scratch. Besides, any file with similar characteristics would be highly suspicious.

So, the technique to combine these formats is not new, not trivial to reproduce, and likely useless for malicious purposes.

# Technical details #

## Compiling ##
all 100% written by hand in x86 assembly with YASM, including Zip, Class and binary structures. the PDF is also hand-written.

to compile, just run: `yasm -o CorkaMInuX elf.asm` / `yasm -o corkamix.exe corkamix.asm` / `yasm -o corkam-osx mosx.asm`

Note that _yasm_ is fully _nasm_-compatible.

## Formats ##

### binary ###
The PE/ELF/Mach-O file formats all hopefully **HAVE** to start at offset 0, which determines the file's start.

  * the PE itself is sectionless, with a collapsed imports structure, breaking all tools at the time of release (check my [PE page](http://pe.corkami.com) for more information)

  * both ELF and Mach-O are non-standard, also breaking a few tools ;)

### PDF ###
  * CorkaMiX 's PDF is Adobe-compatible only: it has an incomplete signature, no xref, etc...

  * CorkaMInuX's: %PDF signature is **NOT** required for alternate (_Linux_', potentially _MuPDF_-based) readers - but a CR character is required before the first object. it also miss Tf parameters, and other things... its PDF is **NOT** _Adobe reader_-compatible.

  * OS X PDF tools typically have very strict requirements, so no signature trick, etc...

check my [PDF page](http://code.google.com/p/corkami/wiki/PDFTricks?wl=en#readers_compatibility) for more information.

### Java ###
  * As Java doesn't check the validity of the ZIP (JAR)'s CRCs, they have been cleared on purpose (this makes the ZIP pseudo-invalid).

  * the Java Class is written by hand

### misc ###
the x86 code contains a few 'undocumented' opcodes - check my [x86 page](http://x86.corkami.com) for more information.

PDF, HTML formats have to be renamed with correct extensions.

More formats could be added inside the ZIP, but this offers no technical challenge.

No widespread image format is allowed to start beyond offset 0 (EMF, GIF, JPG, PNG, TIF, TGA, PCX, BMP...) so none of them can be included directly as-is in the binary (ie, not in the HTML or the ZIP).

### CorkaMiX specific ###
For extra fun, the various parts of the file have been shuffled around: for example, the PDF starts in the PE header, and finishes in the constant pool of the CLASS, inside the ZIP (without compression).

CorkaMiX is also a valid python script:
if the file is a valid ZIP with no appended data, running it with python will handle it as an egg, therefore it will look for a main.py inside the zip instead of just executing the file as a PY.
Therefore, appending a single byte will make it work as a valid PY.
However, this will prevent Java to handle the file as a JAR correctly.

## Follow-up ##

I presented on the topic of binary formats:
&lt;wiki:gadget url="https://corkami.googlecode.com/svn/wiki/gadgets/44con2013\_slideshare.xml" width=595 height=497 border=0/&gt;

I brought the concept further with my [inception slides](https://corkami.googlecode.com/files/44CON2013-Messing%20with%20binary%20formats.zip), where the PDF slides file is also the PDF viewer that I used to show them - ie, the file opens itself, and the presentation is also the demo. I also bundled in the file a Zip of the other PoCs, an HTML page, and a bogus PDF to be displayed only under Chrome:

![https://pbs.twimg.com/media/BUg9neOCIAAAF0l.png](https://pbs.twimg.com/media/BUg9neOCIAAAF0l.png)

# Acknowledgments #
  * [Nicolas Seriot](http://seriot.ch/hello_macho.php)

  * [Brian Raiter](http://www.muppetlabs.com/~breadbox/software/tiny/teensy.html)
  * [Michal Zalewski](http://lcamtuf.coredump.cx/squirrel/)
  * Sergey Bratus
  * revenge
  * Osxreverser