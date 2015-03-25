[link](http://crackmes.de/users/lilcw/just_a_simple_xor_encryption/)
# introduction #
this crackme just requires a correct password...

at first I found it a bit uninteresting but hoped that the author would notmake something just impossible.

While having fun with it, I tried it to avoid any bruteforcing...

hoping that the logic would be enough.


the binary is in clean code, nothing obfuscated.

I like the use of CALL to push strings adress :)
# start #
it turns out that the serial is just used directly to xor a code buffer.
```
0040127A   .  68 2C104000   PUSH    crackme.0040102C
0040127F   .  64:67:A1 0000 MOV     EAX, DWORD PTR FS:[0]
00401284   .  50            PUSH    EAX
00401285   .  64:67:8926 00>MOV     DWORD PTR FS:[0], ESP
0040128B   .  B9 4F000000   MOV     ECX, 4F
00401290   .  BE DB124000   MOV     ESI, crackme.004012DB
00401295   .  56            PUSH    ESI
00401296   .  5F            POP     EDI                              ;  kernel32.7C816FD7
00401297   .  32E4          XOR     AH, AH
00401299   >  80FC 00       CMP     AH, 0
0040129C   .  75 08         JNZ     SHORT crackme.004012A6
0040129E   .  8B1D 05204000 MOV     EBX, DWORD PTR [402005]
004012A4   .  8A23          MOV     AH, BYTE PTR [EBX]
004012A6   >  AC            LODS    BYTE PTR [ESI]
004012A7   .  32C4          XOR     AL, AH
004012A9   .  AA            STOS    BYTE PTR ES:[EDI]
004012AA   .  43            INC     EBX
004012AB   .  8A23          MOV     AH, BYTE PTR [EBX]
004012AD   .^ E2 EA         LOOPD   SHORT crackme.00401299
```
the same thing in pseudocode:
```
h = 0
l = start
    if h == 0
        b = name
        h = *b
    *l++ ^= h
    h = *++b
```

if the decrypted code fails when running, it will trigger an exception that shows that the serial is wrong.

Bad guy exception:
```
0040102C   .  6A 10         PUSH    10
0040102E   .  E8 0A000000   CALL    crackme.0040103D                 ;  PUSH ASCII "w000000ps"
00401033   .  77 30 30 30 3>ASCII   "w000000ps",0
0040103D   >  E8 21000000   CALL    crackme.00401063                 ;  PUSH ASCII "Something fucked up baaaaaaaaaad"
00401042   .  53 6F 6D 65 7>ASCII   "Something fucked"
00401052   .  20 75 70 20 6>ASCII   " up baaaaaaaaaad"
00401062   .  00            ASCII   0
00401063   >  6A 00         PUSH    0                                ; |hOwner = NULL
00401065   .  E8 30030000   CALL    <JMP.&USER32.MessageBoxA>        ; \MessageBoxA
0040106A   .  6A 00         PUSH    0                                ; /ExitCode = 0
0040106C   .  E8 C3020000   CALL    <JMP.&KERNEL32.ExitProcess>      ; \ExitProcess
00401071      00            DB      00
00401072      00            DB      00
00401073      00            DB      00
00401074      00            DB      00
```
there is no other test on the serial.

the code is blindly decrypted, and executed.

execution will determine the rest.

here is the encrypted code buffer
```
.004012DB:  04 7F 8A 62-64 79 77 1E-5C 5C 55 1E-55 42 5B 5E   èbdyw \\U UB[^
.004012EB:  00 47 5E 6F-8A 47 64 79-77 1D 04 09-45 1B 04 15   G^oèGdyw   E  
.004012FB:  02 0D 10 1C-0B 16 15 00-16 1D 57 00-1F 56 45 15            W  VE
.0040130B:  0A 1C 0C 1C-51 03 1D 30-17 30 03 16-03 36 05 18      Q  0 0   6 
.0040131B:  65 1C 65 9A-1C 6E 30 77-04 6F 8A 65-64 79 77     e eÜ n0w oèedyw
```

the exception code just displays a message box and exits.

it doesn't act like a common EH.

we can only assume that the "bad guy" exception is similar to the "good guy" exception.

so we can build a skeleton of the correctly decrypted code:
```
6A ??
E8 ?? 00 00 00
[0-9a-zA-Z ]* 00
E8 ?? 00 00 00
[0-9a-zA-Z ]* 00
6A 00
E8 ?? 00 00 00 => 40139A (USER32.MessageBoxA)
6A 00
E8 ?? 00 00 00 => 401334 (KERNEL32.ExitProcess)
00000000
```
the decrypted code is near the imports so the calls are just one non-zero byte.

we assume that the entered serial is limited to pure alphanums chars.

in the decrypted buffer, we see that dyw (8A62647977) repeats itself 3 times.

the xor algo is using the serial as a circular buffer.

the difference between 3 repeated patterns is always 18 (0x12),

so we assume that's the length of the serial
```
04 7F8A62647977 1E5C5C551E55425B5E0047
5E 6F8A47647977 1D0409451B0415020D101C
0B 161500161D57 001F5645150A1C0C1C5103
1D 301730031603 360518651C659A1C6E3077
04 6F8A65647977
   ^^^^^^^^^^^^
     pattern
```

so let's represent the decrypted buffer in pieces of 18.
```
    è b d y w   \ \ U   U B [ ^   G  ^ o è G d y w       E                            W     V E           Q      0   0       6     e   e Ü   n 0 w    o è e d y w
047F8A626479771E5C5C551E55425B5E0047 5E6F8A476479771D0409451B0415020D101C 0B161500161D57001F5645150A1C0C1C5103 1D301730031603360518651C659A1C6E3077 046F8A65647977
```

and the code we expect. split in 3 because we don't know the length of strings.
```
6A??E8??000000
                                      00E8??000000                         
                                                          006A00E8??0000006A00E8??00000000000000
 n ? b ? d y w 
size : 0x48
```
so the known bytes give us immediately n?b?dyw to get a correct push call.

we don't know the style of the message box so the push is unknown.
so

I'll fill the 012345678901234567 string with the letters i know.

let's try with n1b3dyw78901234567...

of course because of the patterns it generates 3 calls (we assume we need 4)
because the first two are close, we assume they are the string ones.

and the last one is near the end of the buffer so we can only assume it's the exitprocess one.

to have a call to exitprocess we need a call to +A
```
$-5      >  E8 56000000     CALL    crackme.00401380
$ ==>    >  3C 3C           CMP     AL, 3C
$+2      >  3C 3C           CMP     AL, 3C
$+4      >- FF25 9C304000   JMP     DWORD PTR [<&KERNEL32.GlobalAllo>; kernel32.GlobalAlloc
$+A      >- FF25 A0304000   JMP     DWORD PTR [<&KERNEL32.ExitProces>; kernel32.ExitProcess
```
we want this call to match the exit process, the right caracter for that is o. => n1bodyw78901234567

and before all calls, we have a 00 character in our badguy exception. (end of strings, and 0 argument for messagebox &exitprocess)

so we need our pre-call character to decrypt as 00 in the last call, so so o => nobodyw78901234567

it also generates a push 0x10 like in the badguy start, and we have the 2 calls linked as well as the correct exitprocess,
and 00 before each call, so my assumptions were correct.

hopefully Lilcw was nice (or well his challenge would be just impossible and not interesting...

well now we have nobodyw... nobody will ? want ? won't ? would...

with nobodywill01234567 the buffer suddenly becomes very clear... can't be bad news.
```
004012DD  E8 0D 00 00 00 77 30 30 65 2F 67 71 6F 6B 36 70  è....w00e/gqok6p
004012ED  30 00 E8 28 00 00 00 74 68 65 75 2A 36 26 36 38  0.è(...theu*6&68
004012FD  26 2B 65 79 77 6F 72 64 20 69 73 3A 75 24 38 2F  &+eyword is:u$8/
0040130D  38 29 67 34 73 5F 75 5F 67 6F 74 5F 69 74 55 2D  8)g4s_u_got_itU-
0040131D  57 A9 28 5B 06 40 6A 00 E8 0A 00 00 00           W©([@j.è....
```
same w000000ps as the badguy exception ? pass/keyword ? u\_got\_it ?

when trying to get the w0000ps, the following password is needed.

nobodywille.%1kn0w

it's not exact but close then

ever know ? even known ? something else ?

nobodywilleverkn0w just works....

the magic keyword is: congrats\_u\_got\_it

Thanks for the entertaining challenge.

Ange 10/2007