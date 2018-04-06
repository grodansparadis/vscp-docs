# Class=1026 (0x402) Level II Information =======

    CLASS2.INFORMATION
##  Description 

Level II Information events. 

# Type=0 (0x0000) Undefined.

General event. 

# Type=1 (0x0001) Token Activity

This event is used for cards, RFID's, iButtons and other identification devices. The event is generated when the token device is attached/detached to/from the system. 

 | Byte | Description                | 
 | ---- | -----------                | 
 | 0    | Event code.                | 
 | 1    | Zone.                      | 
 | 2    | Sub-zone.                  | 
 | 3    | Token device type code     | 
 | 4-7  | Reserved.                  | 
 | 8..  | Unique ID of token-device. | 

##### Event codes

 | Code  | Description           | 
 | ----  | -----------           | 
 | 0     | Touched and released. | 
 | 1     | Touched.              | 
 | 2     | Released.             | 
 | 3-254 | Reserved.             | 
 | 255   | Error.                | 

##### Token device type 

 | Device type | Description                                                                                                                                                          | 
 | ----------- | -----------                                                                                                                                                          | 
 | 0           | Unknown Token.                                                                                                                                                       | 
 | 1           | iButton 64-bit token.                                                                                                                                                | 
 | 2           | RFID Token.                                                                                                                                                          | 
 | 3           | Philips mifare® RFID Token.                                                                                                                                         | 
 | 4-8         | Reserved.                                                                                                                                                            | 
 | 9           | ID/Credit card.                                                                                                                                                      | 
 | 10-15       | Reserved.                                                                                                                                                            | 
 | 16          | Biometri device 256-bits.                                                                                                                                            | 
 | 17          | Biometri device. 64-bits.                                                                                                                                            | 
 | 18          | Bluetooth device. 48-bits                                                                                                                                            | 
 | 19          | GSM IMEI code (International Mobile Equipment Identity) AA-BBBBBB-CCCCCC-D packed in 64 bits. [http://en.wikipedia.org/wiki/IMEI](http://en.wikipedia.org/wiki/IMEI) | 
 | 20          | GSM IMSI code (International Mobile Subscriber Identity) packed in 64 bits. [http://en.wikipedia.org/wiki/IMSI](http://en.wikipedia.org/wiki/IMSI)                   | 
 | 21-255      | Reserved.                                                                                                                                                            | 

# Type=2 (0x0002) Level II Node Heartbeat

A level II node should send this event at least once a minute to indicate that it is live and well. The implementation **is mandatory**. Recommended interval is 30-60 seconds but in a node that need to sleep longer to save resources a longer interval can be used.

 | Byte | Description                 | 
 | ---- | -----------                 | 
 | 0-63 | Real text name of the node. | 

This event is sent by all level nodes even if they are just a client which can connect to a server but not accept connections itself. 

# Type=3 (0x0003) Level II Proxy Node Heartbeat

This event, which it is advised that all nodes should implement, can be sent by a server that have lower end nodes connected to it at different interfaces. This can be level II or level I nodes. 

 | Byte    | Description                                                             | 
 | ----    | -----------                                                             | 
 | 0-15    | Real GUID for node (not interface GUID)                                 | 
 | 16-31   | Reserved                                                                | 
 | 32-47   | GUID for interface on server the node is connected to.                  | 
 | 48      | Interface Level (0=Level I, 1=Level II)                                 | 
 | 49-63   | Reserved                                                                | 
 | 64-127  | Real text name of node (if any). Set to all zero if not available.      | 
 | 128-191 | Real text name of interface (if any). Set to all zero if not available. | 

If the real GUID for a node is not known (for example discovery is in progress or have failed) bytes 0-15 should be set to zero. This is true for a node that has been configured as a known node but where the system has not yet fetched information from the node.

# Type=4 (0x0004) Level II Multicast channel announce

This event is used by multicast nodes to announce which port(=channel) they communicate on. This may be something they want or not want to share with the rest of the world for security reasons. Therefore this is an optional event. 

If several channels is used several events is sent.

Typically this event is sent on the multicast announce channel

 | Byte | Description    | 
 | ---- | -----------    | 
 | 0    | MSB of channel | 
 | 1    | LSB of channel | 
 | 2-5  | Reserved       | 

\\ 
----
{{  ::copyright.png?400  |}}

`<HTML>``<p style="color:red;text-align:center;">``<a href="http://www.paradiseofthefrog.com">`Paradise of the Frog AB`</a>``</p>``</HTML>`

