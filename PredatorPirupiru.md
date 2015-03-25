[link](http://crackmes.de/users/predator/pirupiru/)
# unpack #
Ok, this file has a lot of appended data...
it's fishy, I remove the appended data, run it, it doesn't work.

Looking around in the file, I see RC4 strings in the file... trying to find the key... setting some breakpoints... no success...

then I see that my process is terminated in OllyDbg yet the crackme is running.

I'm suspecting the case of a binder:

the file
  1. creates a suspended process of itself (CreateProcessA)
  1. unpacks the original file (likely the appended data compressed with RC4) in memory (WriteProcessMemory)
  1. then resume the suspended thread (ResumeThread) and close itself.

the best shortcut is to set a breakpoint on WriteProcessMemory.
the buffer to write is in [esp+c].
Dump the memory area with OllyDbg (Memory Map submenu), cut it with your favorite Hex editor, and you get the original file.
Don't forget to kill the suspended thread with your favorite process tool.

Unpacking done.
# patch #
now, the unpacked EXE. it contains the nag screens text in clear form.
It looks like this string is the one to get :
```
402188: C7459C541D4000 mov         d,[ebp][-064],000401D54 ;'Good Work'
```

the start of the procedure containing that line is in
```
402030: 55             push        ebp
```
if you set a bp on it, it triggers when you click Verify.

if you trace a bit, you see this call is the one that triggers the nag.
```
402144: FF97B0020000   call        d,[edi][0000002B0]
```

a while before, it does a StrLen then compare the results with 3
```
4020AD: FF1508104000   call        __vbaLenBstr
4020B3: 33D2           xor         edx,edx
4020B5: 83F803         cmp         eax,3
```

and... the string it uses for comparison is the Windows Title, "Pirupiru CrackMe" (offset 40125E), which length is 10

Just patch that length check to 0x10 (4020b7), run, click the button, finished.

Thanks to Predator for the fun.

Ange