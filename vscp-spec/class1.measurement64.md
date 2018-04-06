# Class=60 (0x3C) Double precision floating point measurement

    CLASS1.MEASUREMENT64

### Description

Floating point double precision measurements. This class mirrors the standard measurement events is [CLASS1.MEASUREMENT=10](http://www.vscp.org/docs/vscpspec/doku.php?id=class1.measurement). The measurement unit is always the standard unit. 

The value is a "double"	- IEEE-754, 64 Bits, double precision. 

    s eeeeeeeeeee mmmmmmmmmmmmmmmmmmmmmmmmmmmmmmmmmmmmmmmmmmmmmmmmmmm
    s = sign bit ( 1-bit )
    e = exponent ( 11-bits )
    m = mantissa ( 52-bits )	

 | Byte | Description                     | 
 | ---- | -----------                     | 
 | 0    | s e10 e9 e8 e7 e6 e5 e4         | 
 | 1    | e3 e2 e1 e0 m51 m50 m49 m48     | 
 | 2    | m47 m46 m45 m44 m43 m42 m41 m40 | 
 | 3    | m39 m38 m37 m36 m35 m34 m33 m32 | 
 | 4    | m31 m30 m29 m28 m27 m26 m25 m24 | 
 | 5    | m23 m22 m21 m20 m19 m18 m17 m16 | 
 | 6    | m15 m14 m13 m12 m11 m10 m9 m8   | 
 | 7    | m7 m6 m5 m4 m3 m2 m1 m0         | 



\\ 
----
{{  ::copyright.png?400  |}}

`<HTML>``<p style="color:red;text-align:center;">``<a href="http://www.paradiseofthefrog.com">`Paradise of the Frog AB`</a>``</p>``</HTML>`
