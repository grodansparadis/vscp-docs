# VSCP Level II Specifics

Also check VSCP over TCP/IP which have more Level II specifics for the lower levels.

Level II nodes are intended for media with higher bandwidth then Level I nodes and no nickname procedure is therefore implemented on Level II. As result of this the full GUID is sent in each packet. 

## Level II events

The header for a level II event is defined as

`<code="c">`
// This structure is for VSCP Level II typedef struct _vscpMsg 
{ 
    _u16 crc;	     // CRC checksum 
    _u8 *pdata;      // Pointer to data. Max 487 (512- 25) bytes 
	
    // The following two are for daemon internal use 
    _u32 obid;       // Used by driver for channel info etc. 
    _u32 timestamp;  // Relative time stamp for package in microseconds 
     
    // CRC should be calculated from 
    // here to end + datablock 
    _u8 head;        // bit 765 prioriy, 
                     // Priority 0-7 where 0 is highest. 
                     // bit 4 = hardcoded, true for a hardcoded device. 
                     // bit 3 = don't calculate CRC, 
                     // false for CRC usage. 
    _u16 vscp_class; // VSCP class 
    _u16 vscp_type;  // VSCP type 
    _u8 GUID[ 16 ];  // Node address MSB -> LSB _u16 sizeData; 
                     // Number of valid data bytes 
} vscpMsg;
`</code>`

The biggest differences is that the GUID is sent in each event and that both class and type has a 16-bit length.

The CRC is calculated using the CCITT polynomial

The max size for a Level II event is 512 bytes and as the header takes up 25 bytes the payload or data-part can take up max 487 bytes. This can seem a bit strange looking at the above structure but in a datagram or another package not all of the above members are present. The format in a stream is 

## VSCP LEVEL II UDP datagram offsets

`<code="c">`
#define VSCP_UDP_POS_HEAD    0 

#define VSCP_UDP_POS_CLASS   1 
#define VSCP_UDP_POS_TYPE    3 

#define VSCP_UDP_POS_GUID    5 
#define VSCP_UDP_POS_SIZE    21 

#define VSCP_UDP_POS_DATA    23 
#define VSCP_UDP_POS_CRC     len - 2

`</code>`

As is noted the obid and time-stamp is not present in the actual stream and is only inserted by the driver interface software.

Level I events can travel in the Level II world. This is because all Level I events are repleted in Class=1024. As nicknames are not available on Level II they are replaced in the events by the full GUID. This is an important possibility in larger installations.

A event from a node on a Level I that is transferred out on a Level II can have the nodes own GUID set or the GUID of the interface between the Level I and the Level II segment. Which method to choose is up to the implementer as long as the GUID's are unique.

For interfaces the machine MAC address, if available, is a good base for a GUID which easily can map to real physical nodes that are handled by the interface. By using this method each MAC address gives 65536 unique GUID's.

Other methods to generate GUID's s also available form more information see Appendix A. 

## XML Representation

VSCP level II events can be presented as XML data. Format is

`<code=xml>`
`<?xml version = "1.0" encoding = "UTF-8" ?>` 
`<!-- Version 0.0.1 2007-09-27 -->` 
`<event>` 
    `<head>`flags for event`</head>`
    `<class>`Eventclass is numerical form or CLASSx:numerical form`</class>`
    `<type>`Event type in numerical form.`</type>`
    `<!-- GUID of sending node -->`
    `<guid>`ff:ee:dd:cc:bb:aa:99:88:77:66:55:44:33:22:11:00`</guid>` 
    `<timestamp>`YYYY-MM-DD HH:MM:SS|millieconds since epoch`</timestamp>` 
    `<data>`
    Comma separated list with event data. 
    Hex values (preceded with '0x')and decimal values allowed.
    `</data>`
    `<!-- Optional for use with measurements -->`
    `<unit>`Code for unit data is presented in`</unit>`
    `<sensorindex>`Index for sensor`</sensorindex>`
    `<coding>`How value is codes`</coding>`
    `<value>`Measurement value`</value>` 
`</event>`
`</code>`

## JSON Representation

VSCP level II events can be presented as JSON data. Format is

`<code=javascript>`
{ 
    “measurement”: {
         “head”:flags,
         “class”: vscp class,
         “type”: vscp type,
         "guid": "ff:ee:dd:cc:bb:aa:99:88:77:66:55:44:33:22:11:00",
         "timestamp": "YYYY-MM-DD HH:MM:SS|millieconds since epoch",
         “unit”: 0,        /* optional for measurements */
         “sensorindex”: 0  /* optional for measurements */ 
         “coding”: 0       /* optional for measurements */
         “value”: 0,       /* optional for measurements */
    }
}
`</code>`

## Globally Unique Identifiers

To classify as a node in a VSCP net all nodes must be uniquely identified by a globally unique 16-byte (yes that is 16-byte (128 bits) not 16-bit) identifier. This number uniquely identifies all devices around the world and can be used as a means to obtain device descriptions as well as drivers for a specific platform and for a specific device.

The manufacturer of the device can also use the number as a serial number to track manufactured devices. In many other environments and protocols there is a high cost in getting a globally unique number for your equipment. This is not the case with VSCP. If you own an Ethernet card you also have what is needed to create your own GUID's.

The GUID address is not normally used during communication with a node. Instead an 8-bit address is used. This gives a low protocol overhead. A segment can have a maximum of 127 nodes even if the address gives the possibility for 256 nodes. The 8-bit address is received from a master node called the segment controller. The short address is also called the nodes nickname-ID or nickname address.

Besides the GUID it is recommended that all nodes should have a node description string in the firmware that points to a URL that can give full information about the node and its family of devices. As well as providing information about the node, this address can point at drivers for various operating systems or segment controller environments. Reserved GUID's

Some GUID's are reserved and unavailable for assignment. Appendix A list these and also assigned IDs.

The VSCP team controls the rest of the addresses and will allocate addresses to individuals or companies by them sending a request to [guid@vscp.org](guid@vscp.org). You can request a series of 32-bits making it possible for you to manufacture 4294967295 nodes. If you need more (!!!) you can ask for another series. There is no cost for reserving a series. Appendix A in this document contains a list of assigned addresses which will also be available at [http://www.vscp.org](http://www.vscp.org)

## Predefined VSCP GUID's

It is possible to create your own GUID without requesting a series and still get a valid VSCP GUID.

### Assigned Global Unique IDs

** FF:FF:FF:FF:FF:FF:FF:FF:YY:YY:YY:YY:YY:YY:YY:YY **
Dallas Semiconductor GUID's. This is the 1-wire/iButton 64-bit ID. The device code is in the MSB byte and CRC in the LSB byte. 

** FF:FF:FF:FF:FF:FF:FF:FE:YY:YY:YY:YY:YY:YY:XX:XX **
Ethernet Device GUID's. The holder of the address can freely use the two least significant bytes of the GUID. MAC address in MSB - LSB order. Also called MAC-48 or EUI-48 by IEEE 

** FF:FF:FF:FF:FF:FF:FF:FD:YY:YY:YY:YY:XX:XX:XX:XX **
Internet version 4 GUID's. This is a 32-bit ID so the holder of the address can freely use the four least significant bytes of the GUID. IP V4 address in MSB - LSB order.

** FF:FF:FF:FF:FF:FF:FF:FC:XX:XX:XX:XX:XX:XX:XX:XX **
Private. Use for in-house local use. The GUID should never appear outside your local segments. 

** FF:FF:FF:FF:FF:FF:FF:FB:YY:YY:YY:XX:XX:XX:XX:XX ** 
ISO ID. This is a three byte ID so the holder of the ISO ID can freely use the five least significant bytes of the GUID. 

** FF:FF:FF:FF:FF:FF:FF:FA:YY:YY:YY:YY:XX:XX:XX:XX ** 
CiA (CAN in Automation) vendor ID. This is a 32-bit ID so the holder of the vendor ID can freely use the four least significant bytes of the GUID.

** FF:FF:FF:FF:FF:FF:FF:F9:YY:YY:YY:XX:XX:XX:XX:XX ** 
ZigBee 802.15.4 OID. This is a 24-bit ID so the holder of the OID can freely use the five least significant bytes of the GUID. 

** FF:FF:FF:FF:FF:FF:FF:F8:YY:YY:YY:YY:YY:YY:XX:XX ** 
Bluetooth MAC. This is a 48-bit ID so the holder of the OID can freely use the two least significant bytes of the GUID. 

** FF:FF:FF:FF:FF:FF:FF:F7:YY:YY:YY:YY:YY:YY:YY:YY ** 
IEEE EUI-64. This is a 64-bit ID. The upper three bytes are purchased from IEEE by the company that releases the product. The lower five bytes are assigned by the device and must be unique.

** FF:FF:FF:FF:FF:FF:FF:F6:00:YY:YY:YY:YY:YY:YY:YY ** 
Reserved for RAMTRON MRAM (and compatible), seven byte IDs.

** FF:FF:FF:FF:FF:FF:FF:F6:01:YY:YY:YY:YY:YY:YY:YY - FF:FF:FF:FF:FF:FF:FF:F6:FF:YY:YY:YY:YY:YY:YY:YY	**
Reserved other future seven bit ID (memory devices) that may have ID, such as PRAM etc.

** FF:FF:FF:FF:FF:FF:FF:F5:00:00:00:00:XX:XX:xXX:XX - FF:FF:FF:FF:FF:FF:FF:FF:FF:FF:FF:FF:XX:XX:XX:XX ** 	
Reserved

**00:00:00:00:00:00:00:00:00:00:00:00:xx:xx:xx:xx** 
Lab usage. You can use this range for your own development or for in-house local use. The GUID should never appear outside your local segments. 

** FE:YY:YY:YY:YY:YY:YY:YY:YY:YY:YY:YY:YY:YY:YY:YY**	
Reserved for a generated 128 bit GUID where the most significant byte is replaced by FE Only use for Level II and on internal net. [http://hegel.ittc.ku.edu/topics/internet/internet-drafts/draft-l/draft-leach-uuids-guids-01.txt](http://hegel.ittc.ku.edu/topics/internet/internet-drafts/draft-l/draft-leach-uuids-guids-01.txt)

## Shorthand GUID's

Note that there is a convenient shorthand notation :: used for IPV6 that can be used as a place holder for zeros. For example

    ::1 

really means 

    00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:01

and 

    FF:21::22:32 

is the same as

    FF:21:00:00:00:00:00:00:00:00:00:00:00:00:22:32

we use *: in the same way for FFs so that

*:1 really means 

    FF:FF:FF:FF:FF:FF:FF:FF:FF:FF:FF:FF:FF:FF:FF:01

These short cut notations makes it much easier to write GUID's. 

\\ 
----
{{  ::copyright.png?400  |}}

`<HTML>``<p style="color:red;text-align:center;">``<a href="http://www.paradiseofthefrog.com">`Paradise of the Frog AB`</a>``</p>``</HTML>`
