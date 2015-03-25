# [Dirty J.O.E](http://rejava.sourceforge.net/) #
## Allatori obfuscator ##

  1. contains a `<single_letter>.class` in root
    * with a `<single_letter> method`
```
bipush              85
swap
dup
arraylength
iconst_1
isub
dup_x2
istore_3
astore_1
istore_2
iflt                pos.00000053
aload_1
iload_3
aload_0
iload_3
invokevirtual       char java.lang.String.charAt(int)
iload_2
ixor
i2c
swap
dup_x1
iload_2
ixor
i2c
bipush              63
```
  1. `/com/.../something.class` contains calls like:
```
ldc                 "G}q}e4p`i`q;"
invokestatic        java.lang.String m.c(java.lang.String)
```
  1. so use DirtyJOE's allatori.py with keys `[85,63]` in this example.
```
import pyjoe

def dj_decryptUTF8(inBuf):
	key = [85, 63]
	return pyjoe.dj_decryptUTF8_Allatori(inBuf, key)
```


## Zelix KlassMaster ##
  1. identifiable as `ZKM...` in the constant pool
  1. in method `<clinit>`:
```
tableswitch         l: 0, h: 3, def: pos.000000A0, pos.(0000008C, 00000091, 00000096, 0000009B)
bipush              111
goto                pos.000000A2
bipush              110
goto                pos.000000A2
bipush              97
goto                pos.000000A2
bipush              83
goto                pos.000000A2
bipush              71
ixor
```
  1. in DirtyJOE:
```
def dj_decryptUTF8(inBuf):
	key = [111,110, 97, 83, 71]
	return pyjoe.dj_decryptUTF8_ZKM(inBuf, key)
```

# [reJava](http://rejava.sourceforge.net/) #
  * [screen tutorial](http://rejava.sourceforge.net/hello.html)