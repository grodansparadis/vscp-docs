# Class=1060 (0x424) Measurement float

    CLASS2.MEASUREMENT_FLOAT

##  Description 

This is a mirror of [CLASS1_MEASUREMENT](http://www.vscp.org/docs/vscpspec/doku.php?id=class1.measurement) where the data is in string form. The zero terminated string has the following form “sensor-index, value-unit, zone, subzone, value” where the value is a double precision floating point value and the senor-index and value-unit is the same as for [CLASS1_MEASUREMENT](http://www.vscp.org/docs/vscpspec/doku.php?id=class1.measurement) but where sensor index's can be in the range 0-255 and value-unit in the range 0-255.

 | Byte | Description                                                    | 
 | ---- | -----------                                                    | 
 | 0    | Index for sensor, 0-255.                                       | 
 | 1    | Zone, 0-255.                                                   | 
 | 2    | Sub zone, 0-255.                                               | 
 | 3    | Unit from measurements, 0-255.                                 | 
 | 4-11 | 64-bit double precision floating point value stored MSB first. | 

\\ 
----
{{  ::copyright.png?400  |}}

`<HTML>``<p style="color:red;text-align:center;">``<a href="http://www.paradiseofthefrog.com">`Paradise of the Frog AB`</a>``</p>``</HTML>`

