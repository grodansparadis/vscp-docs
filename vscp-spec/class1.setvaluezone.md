# Class=85 (0x55) Set value with zone

    CLASS1.SETVALUEZONE

### Description

This class mirrors the standard measurement events is [CLASS1.MEASUREMENT=10](http://www.vscp.org/docs/vscpspec/doku.php?id=class1.measurement) but also have zone information and is intended for setting a value instead of providing a measurement.

 | Byte | Description                                                        | 
 | ---- | -----------                                                        | 
 | 0    | Index for sensor.                                                  | 
 | 1    | Zone for which event applies to (0-255). 255 is all zones.         | 
 | 2    | Sub-zone for which event applies to (0-255). 255 is all sub-zones. | 
 | 3    | Data coding.                                                       | 
 | 4-7  | Data with format defined by byte 0.                                | 


\\ 
----
{{  ::copyright.png?400  |}}

`<HTML>``<p style="color:red;text-align:center;">``<a href="http://www.paradiseofthefrog.com">`Paradise of the Frog AB`</a>``</p>``</HTML>`
