# Introduction #

compiled by OpenWatcom (ymmv)

the original call is: `myfunc(0,1,2,3,4)`
  * standard order is first argument is pushed last,
  * standard stack adjusting is 'callee cleanup' - after returning, the stack should be without its calling arguments
=> the stack looks vertically like the call order.
## sdtcall (stack only) ##
```
push    4
push    3
push    2
push    1
push    0
call    myfunc
xor     eax,eax
retn    10
```

## fastcall (ecx, edx) ##
this is actually **Microsoft**'s fastcall
```
push    4
push    3
push    2
mov     edx,1
xor     ecx,ecx
call    myfunc
xor     eax,eax
retn    10
```

## cdecl & syscall (caller cleanup) ##
```
push    4
push    3
push    2
push    1
push    0
call    myfunc
add     esp,014
xor     eax,eax
retn    10
```

## pascal (reverse order, ebx saved, even if ebx is unused...) ##
```
push    ebx
push    0
push    1
push    2
push    3
push    4
call    myfunc
xor     eax,eax
pop     ebx
retn    10
```

## fortran/watcall (eax, edx, ebx, ecx, then stack - ebx is saved) ##
Apparently it's not so clear what the fortran calling convention is, and this one is even different from raymond's post's [The \_\_fortran calling convention isn't the calling convention used by FORTRAN](http://blogs.msdn.com/b/oldnewthing/archive/2010/12/22/10108152.aspx)
```
push    ebx
push    4
mov     ecx,3
mov     ebx,2
mov     edx,1
xor     eax,eax
call    myfunc
xor     eax,eax
pop     ebx
retn    10
```