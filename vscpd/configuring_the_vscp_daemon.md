# Configuring the VSCP Daemon

## Location of the configuration file

The daemon needs a configuration file called **vscpd.conf**. It will not start without this file nor if the file contains invalid information.

On **Windows machines** the configuration file should be in the folder

    \Documents and Settings\All users\Application Data\vscp

on older windows versions. And in 

    \ProgramData\vscp

on newer windows versions. Normally the installation program will create a default file.

On **Unix machines** it will be located in 
    /etc/vscp
    
The location that is searched for the configuration file can be changed with switches when you start the daemon. see the section about [startup switches](http://www.vscp.org/docs/vscpd/doku.php?id=vscp_daemon_startup_switches) for each platform.

The configuration file have a path to a sql database file that store configuration setting among other things. Many configuration items are possible to set in both the xml file and in sql file. If a setting is present in both the setting in the xml file will be used. 

The reading of configuration files is as follows (since version 1.12.24)

 1.  The general section of the XML configuration is read.
 2.  The database configuration is read.
 3.  The full XML configuration file is read.

Values present in both the database configuration and in the XML configuration will us end up with the settings from the XML file.

##  Configuration

The configuration file is a standard XML file that contains information that tells the VSCP daemon what to do and how it should be done. The information in it is divided into sections and this documentation and each section and it's content is described below.

You can view a sample configuration file [here](Sample vscpd configuration file).

## The general section

In the general section you find settings that are common to all components of the VSCP daemon software. 

### security

`<code=xml>`
    <security admin="username" 
      password="450ADCE88F2FDBB20F3318B65E53CA4A;06D3311CC2195E80BE4F8EB12931BFEB5C630F6B154B2D644ABE29CEBDBFB545" 
      allowfrom="list of remotes" 
      vscptoken="Carpe diem quam minimum credula postero"
      vscpkey="A4A86F7D7E119BA3F0CD06881E371B989B33B6D606A863B633EF529D64544F8E"
      digest=""
    />
`</code>`

The admin user and other security settings can only be defined in the configuration file. It is __VERY important__ that this file only can be read by users that are allowed to do so.

#####  admin

Name of admin user. Default is "admin". __Use something else.__ __PLEASE!__

##### password

Password for admin user. Default is "secret". __Use something else.__ __PLEASE!__  The password consist of a 16-byte salt and a 32 byte has on the form salt;hash. pbkdf2 is used for password handling with SHA256,

Use the **vscpmkpasswd** tool supplied with VSCP & Friends to generate the password (called mkpasswd in earlier versions).

Before version 1.12.26 the password stored here is formed by taking an MD5 over *username:authdomain:password*.

##### allowfrom

A comma separated list (IPv4 and IPv6) of remote machines from which the admin user can log in. Wild-cards can be use. "192.168.1.*" means all machines on the 192.168.1.0 subnet. "*" means all remote machines and is the default.  

##### vscptoken

This is used as a secret token (**key**) in traffic between server and client. Use any string you like and keep it secret. It should never be sent unencrypted over the wire.

##### vscpkey

This is 256-bit, 32-byte, hexadecimal string that is used a the key for encryption/decryption. It can also be set as a command line switch for the VSCP server. Default is 

    A4A86F7D7E119BA3F0CD06881E371B989B33B6D606A863B633EF529D64544F8E
    
or 

`<code="c">`  
uint8_t key[] = { 0xA4,0xA8,0x6F,0x7D,0x7E,0x11,0x9B,0xA3,
                   0xF0,0xCD,0x06,0x88,0x1E,0x37,0x1B,0x98,
                   0x9B,0x33,0xB6,0xD6,0x06,0xA8,0x63,0xB6,
                   0x33,0xEF,0x52,0x9D,0x64,0x54,0x4F,0x8E };
`</code>`

Read the [security section](security_general) for more information about how the different values is used.




### loglevel

** Can only be set in XML configuration file **

`<code=xml>`
    `<loglevel>`n`</loglevel>`
`</code>`

Set loglevel n is in range 0-2 where 0 is no logging and the rest of the levels are different levels of logging which will log everything with a higher log value. 

 | Numeric level | textual level | Description                                                                | 
 | ------------- | ------------- | -----------                                                                | 
 | 0             | none          | No logging will be performed. This is not recommended.                     | 
 | 1             | normal        | Normal logging information will be printed. **This is the default level.** | 
 | 2             | debug         | Maximum amount of logging information will be printed.                     | 

**Before Version 1.12.5 log levels where 0-5.**


----

### runasuser

** Can only be set in XML configuration file **

`<code=xml>`
    `<runasuser>`n`</runasuser>`
`</code>`

__Only on Unix/Linux__ user to run the VSCP daemon as.

----

### guid

`<code=xml>`
`<guid>`
    FF:FF:FF:FF:FF:FF:FF:FF:FF:FF:FF:F5:00:00:00:00
`</guid>`
`</code>`

Set the server GUID for the daemon. This GUID is the base for all interface GUID's in the system. Interface GUID's is formed like xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:aa:aa:bb:bb where xx is guid set here, aa is the interface id and bb is nickname id's for devices on that interface. 

If not set here (or all nills) a GUID will be formed from the (first) MAC address of the machine the daemon runs on (on Linux needs to be run as root to get this) or if this fails the local IP address is used to form the GUID. The GUID string should be in MSB -> LSB order and have the four lsb set to zero.

Clients will be identified with a GUID that consist of this GUID plus an on the system unique number in the aa aa bytes. Devices will use this GUID as the source for events if no GUID is set for the driver or if it is set to all zeros.

**Example**
   FF:EE:DD:CC:BB:AA:99:88:77:66:55:44:00:00:00:00

----

### Servername

If set this real text name will be used as an identifier for the server along with the GUID. The default name will be something like

    VSCP Daemon @ FF:FF:FF:FF:FF:FF:FF:FE:00:26:55:CA:1F:DA:00:00

----

### logsyslog

**Can only be set in XML configuration file**

`<code=xml>`
`<logsyslog enable="true" />`
`</code>`

Set to true to (also) log to syslog.

----

### logdatabase

`<code=xml>`
`<logdatabase path="/srv/vscp/vscp_log.sqlite3" />`
`</code>`

Had attribute *enable* before version 1.12.18.0.

The VSCP daemon can log to plain files (see below) or to a database. Enabling *logdatabase* will send all logging data to a database instead of writing it to separate text files. Logging to a database is the default.

The file used here is a file that will be updated frequently so if you are on a system with a flash disk it may be a good choice to point this file to a non flash disk such as a ram disk or similar. 

The log types in the database is like the following

 | Type                    | Identifier in db | 
 | ----                    | ---------------- | 
 | DAEMON_LOGTYPE_GENERAL  | 0                | 
 | DAEMON_LOGTYPE_SECURITY | 1                | 
 | DAEMON_LOGTYPE_ACCESS   | 2                | 
 | DAEMON_LOGTYPE_DM       | 3                | 

and the database is defined by

`<code=sql>`
CREATE TABLE `log` (
	`idx_log`	INTEGER NOT NULL PRIMARY KEY AUTOINCREMENT UNIQUE,
	`type`    INTEGER,
	`date`	  TEXT,
	`level`	  INTEGER,
	`message` TEXT
)
`</code>`

This setting was added in version 1.12.6


### generallogfile

Format from version 1.12.6
Removed in version 1.12.18.0

`<code=xml>`
`<generallogfile enable="true" path="/srv/vscp/logs/vscp_log_security" />`
`</code>`

previous format (still works but is deprecated)

`<code=xml>`
`<generallogfile enable="true">`
    c:/tmp/vscp_log_general.txt
`</generallogfile>`
`</code>`

The generallogfile gets all general logging information if database logging is disabled.   

#####  enable

Enable or disable writing to the file. If database logging is enable this functionality has no meaning.

#####  path

Path to the log file. If it is available when the system starts new log information will be appended to this file. Default path on Unix is

    /var/log/vscp/vscp_log_general

and on windows

    /Programdata/vscp/vscp/logs/vscp_log_general.txt

----

### securitylogfile

Format from version 1.12.6
Removed in version 1.12.18.0

`<code=xml>`
`<securitylogfile enable="true" path="/srv/vscp/logs/vscp_log_security" />`
`</code>`

previous format (still works but is deprecated)

`<code=xml>`
`<securitylogfile enable="true">`
    /srv/vscp/logs/vscp_log_security
`</securitylogfile>`
`</code>`

The security logfile get all security information in the system. Valid and invalid logins etc if database logging is disabled.

##### enable

Enable or disable writing to the file.If database logging is enable this functionality has no meaning.

##### path

Path to the log file. If it is available when the system starts new log information will be appended to this file. Default path on Unix is

    /var/log/vscp/vscp_log_security

and on windows

    /Programdata/vscp/vscp/logs/vscp_log_security.txt

----

### accesslogfile

Format from version 1.12.6
Removed in version 1.12.18.0

`<code=xml>`
`<accesslogfile enable="true" path="/srv/vscp/logs/vscp_log_access" />`
`</code>`

previous format (still works but is deprecated)

`<code=xml>`
`<accesslogfile enable="true">`
   /srv/vscp/logs/vscp_log_access
`</accesslogfile>`
`</code>`

The access logfile get all access information from web page, rest and websocket traffic if database logging is disabled.  

##### enable

Enable or disable writing to the file.If database logging is enable this functionality has no meaning.

#####  path

Path to the log file. If it is available when the system starts new log information will be appended to this file. Default path on Unix is

    /var/log/vscp/vscp_log_access

and on windows

    /Programdata/vscp/vscp/logs/vscp_log_access.txt
    
----



##  tcp/ip client interface

`<code=xml>`
`<tcpif interface=”ip4-address:port” />`
`</code>`

Set the port and interface(s) the TCP/IP client should be listening on.

**Examples**
`<code=xml>`
`<tcpip interface="9598"/>`
`</code>`

###   enable

*deprecated in 1.12.6 now always enabled* \\  
Enable/disable the VSCP daemon TCP/IP interface. Default is enabled. 

###   interface

Set one or more addresses separated with space the interface to bind to on a machine with multiple interfaces. If no address (just a port) is given the TCP/IP interface will be available on all interfaces (this is the default). If an address is given the daemon listens only on that interface or if several is listen on all listed interfaces . Default is “9598” meaning the TCP/IP client will be available on all interfaces on the machine. For SSL, server certificate must be specified as
\\ 
   ssl://[ip:]port:server_cert.pem
\\ 
here server_cert.pem is the full path including the filename to the cert. To enable client certificate authentication (two-way SSL), a CA certificate should be specified: 
\\ 
   ssl://[ip:]port:server_cert.pem:ca_cert.pem 
\\ 
where both server_cert.pem and ca_cert.pem are full paths including filename to the certificates. Server certificate must be in pem format. Pem file should contain both certificate and the private key concatenated together.

----

## Multicast announce interface

Valid from version 1.12.26 

The multicast announce interface is used to announce a VSCP system about indirectly discovered nodes and for Level II nodes a way to make there existence known. The VSCP assigned multicast group **224.0.23.158** is always used.

`<code=xml>`
<multicast-announce enable="true"
        interface="udp://9598"
        ttl = "1"
/>
`</code>`

### enable

Set to true ("true") to enable interface. Default is enabled.

### interface

Set the interface and the port used for the announcements. Normally this should be

    udp://9598

which is the default.  This setting say that announcements should be done on all interfaces of the machine using the VSCP standard port 9598. Set a specific port as 

    udp://192.168.1.233:9598
 
if you only want announcements on a specific port.
    

###  ttl

Time to live for multicast datagrams.

The following table show the possible settings.

 | TTL | Scope                                                                            | 
 | --- | -----                                                                            | 
 | 0   | Are restricted to the same host. Won't be output by any interface.               | 
 | 1   | Are restricted to the same subnet. Won't be forwarded by a router. (**default**) | 
 | 32  | Are restricted to the same site, organization or department.                     | 
 | 64  | Are restricted to the same region.                                               | 
 | 128 | Are restricted to the same continent.                                            | 
 | 255 | Are unrestricted in scope (global).                                              | 
##   Multicast channel interface

Valid from version **1.12.26**

One can set up one or more multicast channel to share events among a group of Level II devices, Normally the VSCP assigned multicast group is used for this and where different groups use different ports.

`<code=xml>`
`<multicast enable="true">`
        <channel enable="true"
                    public="192.168.1.9"
                    port="44444"
                    group="224.0.23.158"
                    ttl="1"
                    guid="00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:00"
                    txfilter=""
                    txmask=""
                    rxfilter=""
                    rxmask=""
                    encryption=""
                    bSendAck="false"
        />
    `</multicast>`
`</code>`

`<multicast>`

###   enable

Enable/disable VSCP server multicast interface. Can be "true" for enabled or "false" for disabled. Default is "false"

\\ 

`<channel>`

###   enable

Enable/disable VSCP server multicast channel. Can be "true" for enabled or "false" for disabled. Default is "false".

###   public

This must be a static public ip-address available on one of the interfaces of your machine. Can not be set to a local ip-address like 127.0.0.1 or be empty. Setting this to a non static, non public value will definitely crash the VSCP server.

###  port

Port to use for this multicast group. Defaults to 44444.

###  group

This is the multicast group to join. Defaults to the VSCP assigned multicast group 224.0.23.158.

###  ttl

Time to live for multicast datagrams.

The following table show the possible settings.

 | TTL | Scope                                                                            | 
 | --- | -----                                                                            | 
 | 0   | Are restricted to the same host. Won't be output by any interface.               | 
 | 1   | Are restricted to the same subnet. Won't be forwarded by a router. (**default**) | 
 | 32  | Are restricted to the same site, organization or department.                     | 
 | 64  | Are restricted to the same region.                                               | 
 | 128 | Are restricted to the same continent.                                            | 
 | 255 | Are unrestricted in scope (global).                                              | 

### guid

The GUID for the interface. Will be set by the system by default or if all nills "00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:00".

###  txfilter

Transmission filter. Outgoing events can be filtered with the txfilter/txmask pair. The filter should be given on the form

    priority,class,type,GUID
    
Default is to transmit all events.  

### txmask

Transmission filter. Outgoing events can be filtered with the txfilter/txmask pair. The mask should be given on the form

    priority,class,type,GUID
    
Default is to transmit all events.

### rxfilter

Receive filter. Incoming events can be filtered with the rxfilter/rxmask pair. The filter should be given on the form

    priority,class,type,GUID
    
Default is to receive all events.

### rxmask

Receive mask. Incoming events can be filtered with the rxfilter/rxmask pair. The filter should be given on the form

    priority,class,type,GUID
    
Default is to receive all events.

### encryption

Multicast frames can be received/sent in four different formats. Either as they are (encryption="none"), or encoded with AES128 (encryption="aes128"), encoded with AES192 (encryption="aes192") or encoded with AES256 (encryption="aes256"). When frames are encoded a 16-byte iv is appended to each frame. The key (16-byte,24-byte or 32-byte) used to decrypt the frames should be the same as on the VSCP Server has set in it's security setting. Default is

    vscpkey="A4A86F7D7E119BA3F0CD06881E371B989B33B6D606A863B633EF529D64544F8E"
    
or

`<code="c">`
    uint8_t key[] = { 0xA4,0xA8,0x6F,0x7D,0x7E,0x11,0x9B,0xA3,
                    0xF0,0xCD,0x06,0x88,0x1E,0x37,0x1B,0x98,
                    0x9B,0x33,0xB6,0xD6,0x06,0xA8,0x63,0xB6,
                    0x33,0xEF,0x52,0x9D,0x64,0x54,0x4F,0x8E };
`</code>`
   

### bSendAck

Set to "true" to send an ACK package when a multicast datagram is received. 

 | Package      | Description                           | 
 | -------      | -----------                           | 
 | ACK package  | CLASS1.ERROR, Type = 0 (0x00) Success | 
 | NACK package | CLASS1.ERROR, Type = 1 (0x01) Error   | 

bAllowUnsecure

With this setting one can tell if frames that are not encrypted should be received. If set to “true” they will. Setting it to false is more secure as only machines/users knowing the secret key can send multicast datagrams to the machine. Default is true.


----

##   UDP interface

Valid from version **1.12.26**

`<code=xml>`
<udp enable="true"
            interface="udp://44444"
            user=""
            password=""
            bAllowUnsecure="true"
            bSendAck="true"
            filter=""
            mask=""
            guid="00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:00" >

        `<!-- Nodes that will receive VSCP Server events -->`
        <rxnode enable="false"
            interface="udp://127.0.0.1:9999"
            filter=""
            mask=""
            encryption=""
            bSetBroadcast="false"
        />
    `</udp>`
`</code>`

**udp**

###   enable

Enable ("true")/disable ("false") daemon UDP interface. Default is disabled. 

###  interface

Set the interface where incoming UDO frames are received. Default is

    "udp://127.0.0.1:9999" 

will listen on all interfaces put a static public address should be used for it to be useful for real work.

###  user

The user the incoming interface should act as. Default is none (""). Setting a user will use that users privileges when it comes to what events can be received and from whom.

### password

The password for the user the incoming interface should act as. Default is none.

### bAllowUnsecure

With this setting one can tell if frames that are not encrypted should be received. If set to "true" they will. Setting it to false is more secure as only machines/users knowing the secret key can send UDP datagrams to the machine. Default is true.

### filter

Receive filter. Incoming events can be filtered with the filter/mask pair. The filter should be given on the form

    priority,class,type,GUID
    
Default is to receive all events.

### mask

Receive mask. Incoming events can be filtered with the filter/mask pair. The filter should be given on the form

    priority,class,type,GUID
    
Default is to receive all events.

### guid

The GUID for the interface. Will be set by the system by default or if all nills "00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:00".


**rxnode**

Here one can set up any number of nodes that will get outgoing UDP datagrams from the VSCP server sent to them.

### interface

This is the interface the VSCP server will send on. Should consist of a remote host ip-address and a port.

### filter

Transmission filter. Outgoing events can be filtered with the filter/mask pair. The filter should be given on the form

    priority,class,type,GUID
    
Default is to transmit all events.

### mask

Transmission mask. Outgoing events can be filtered with the filter/mask pair. The filter should be given on the form

    priority,class,type,GUID
    
Default is to transmit all events.

### encryption

UDP datagrams can be sent in four different formats. Either as they are (encryption="none"), or encoded with AES128 (encryption="aes128"), encoded with AES192 (encryption="aes192") or encoded with AES256 (encryption="aes256"). When frames are encoded a 16-byte iv is appended to each frame. The key (16-byte,24-byte or 32-byte) used to decrypt the frames should be the same as on the VSCP Server has set in it's security setting. Default is

    vscpkey="A4A86F7D7E119BA3F0CD06881E371B989B33B6D606A863B633EF529D64544F8E"
    
or

`<code="c">`
    uint8_t key[] = { 0xA4,0xA8,0x6F,0x7D,0x7E,0x11,0x9B,0xA3,
                    0xF0,0xCD,0x06,0x88,0x1E,0x37,0x1B,0x98,
                    0x9B,0x33,0xB6,0xD6,0x06,0xA8,0x63,0xB6,
                    0x33,0xEF,0x52,0x9D,0x64,0x54,0x4F,0x8E };
`</code>`

### bSetBroadcast

Set to true ("true") to set broadcast flag (MG_F_ENABLE_BROADCAST) on the socket. 

Currently not used.

----

##   vscp

`<code=xml>`
`<vscp enable=”true|false” />`
`</code>`

*deprecated in 1.12.6 now always enabled*  

Enable/disable daemon internal VSCP functionality. If disabled the server will no longer react on events like the High end server probe etc. Default is  enabled. 

----

##   dm

`<code=xml>`
<dm enable=”true|false” 
          path[xml]=”path-to-dm-xml” 
          pathdb="path-to-dm-database"
          loglevel="debug"        
`</code>`

###   enable

*deprecated in 1.12.6 now always enabled*  

Enable/disable the internal decision matrix of the daemon. Default is enabled. 

###   path/pathxml

Path to the decision matrix XML file. The standard place for the decision matrix is 

    /srv/vscp/dm.xml 

on a Unix machine and

    /ProgramData/vscp/dm.xml

on a Windows machine.

The older form *path* is deprecated but will be left in the system and will be read on system startup. 


You also can control the log file for action handling in the decision matrix here. 

###   pathdb

This is the path to the DM database file. This path can point to a read only or a read/write location depending on if one want to be able to edit the DM in the web interface or not. 

The standard place for the decision matrix is 

    /srv/vscp/vscp_dm.sqlite3 

on a Unix machine and

    /ProgramData/vscp/vscp_dm.sqlite3 

on a Windows machine.

###   loglevel

If set to "debug" and if main server debug level is set to debug extra DM information (a lot) will be logged. Useful when checking DM problems. 

----

##   variables

`<code=xml>`
<variables enable=”true|false” 
    path[xml]=”/srv/vscp/variables.xml”
    pathdb=”/srv/vscp/vscp_variable.sqlite3”
/>
`</code>`

###   enable

*deprecated in 1.12.6 now always enabled*  

Enable/disable the internal variable handling of the daemon. Default is enabled. 

###   path[xml]

The **pathxml** or the older form **path** points to the standard place for the persistent variable file and is  

    /srv/vscp/variables.xml 

on a Unix machine and

    /ProgramData/vscp/variables.xml

on a Windows machine.  On versions prior to 1.12.6 this file should be placed on a location that was writeable but later versions is always only read. It is actually deprecated but still works and will do so for a foreseeable future.

### pathdb

Available from version 1.12.6. This is the path to the database file for variables. It should be located on a read/write location. Defaults are

    /srv/vscp/vscp_variable.sqlite3 

on a Unix machine and

    /ProgramData/vscp_variable.sqlite3

on a Windows machine.

### autosave

*deprecated in version 1.12.6* \\  

The **autosave** value defaults to 5 minutes and tell how many minutes should elapse between automatic saves of the variables. Set to zero to disable. This is deprecated from version 1.12.6

----



### clientbuffersize

`<code=xml>`
`<clientbuffersize>`1024`</clientbuffersize>`
`</code>`

This is the default buffer size (number of events) for all clients in the system. Everything from a driver to a TCP/IP user is regarded as a client. Default is 1024. 

----

##   webserver

`<code=xml>`
<webserver enable="true" 
              disableauthentication="false"
              webrootpath="/srv/vscp/www"
              port="8080"
              path_cert=""
              authdomain="mydomain.com"
              cgi_interpreter=""
              cgi_pattern="**.cgi$|**.pl$|**.php|**.py"
              enable_directory_listing="true"
              hilde_file_patterns="secret.txt|even_more_secret.txt"
              index_files="index.html,index.htm,index.cgi,index.shtml,index.php"
              extra_mime_types=".cpp=plain/text,.java=plain/text"
              url_rewrites=""
              ssi_pattern=""
              run_as_user=""
              per_directory_auth_file=""
              global_auth_file=""
              ip_acl=""
              dav_document_root=""
`</webserver>`              
`</code>`




###   enable

Enable or disable the web server functionality.

###   disableauthentication

If set to true web authentication will not be performed and any user from any host can connect to any page.

###   webrootpath

Path to webserver root filesystem. The daemon should have read/write permissions to this area. Webserver and websocket interface look for content here. Defaults to 

    /programdata/vscp/www” 

on windows and to

    /srv/vscp/www” 

on Unix/Linux.

###   port

Port is the port the webserver should listen to. It can be given just as a port (default "8080") and will then listen on that port on all interfaces or it can be given as "ip-addr:port" e.h "127.0.0.1:8080" and will then only use that interface. To enter an ssl port put an "s" after the port. You can also redirect a port like "80r,443s" where access on port 80 is redirected to 443.

###   pathcert

Path to SSL certificate file. The file must be in PEM format, and it must have both private key and certificate, see for example ssl_cert.pem. If this option is set, then the webserver serves SSL connections on the port set up to listen on. Default: not set.

**Example cert**

	
	   -----BEGIN RSA PRIVATE KEY-----
	   MIIEogIBAAKCAQEAwONaLOP7EdegqjRuQKSDXzvHmFMZfBufjhELhNjo5KsL4ieH
	   hYN0Zii2yTb63jGxKY6gH1R/r9dL8kXaJmcZrfSa3AgywnteJWg=
	   -----END RSA PRIVATE KEY-----
	   -----BEGIN CERTIFICATE-----
	   MIIDBjCCAe4CCQCX05m0b053QzANBgkqhkiG9w0BAQQFADBFMQswCQYDVQQGEwJB
	   SEGI4JSxV56lYg==
	  -----END CERTIFICATE-----


###   authdomain

The domain the (web)server belong to. Defaults to 

    “mydomain.com” 

and is used for authentications in the system. 

*In the past the autdomain was used in the algorithm to calculate the password hash but this is no longer true as it has been replaced by the vscptoken. See the `<security>` tab.*

###   cgi_interpreter

Path to an executable to be used use as an interpreter for all CGI scripts regardless script extension. Default: not set, the webserver looks at shebang line "#!".

For example, if both PHP and perl CGIs are used, then #!/path/to/php-cgi.exe and #!/path/to/perl.exe must be first lines of the respective CGI scripts. Note that paths should be either full file paths, or file paths relative to the directory where mongoose executable is located.

If all CGIs use the same interpreter, for example they are all PHP, then cgi_interpreter option can be set to the path to php-cgi.exe executable and shebang line in the CGI scripts can be omitted. Note: PHP scripts must use php-cgi.exe, not php.exe.

###   cgi_pattern

All files that match cgi_pattern are treated as CGI files. Default pattern allows CGI files to be anywhere. To restrict CGIs to a certain directory, use /path/to/cgi-bin/`<nowiki>``</nowiki>`.cgi as a pattern. Note that full file path is matched against the pattern, not the URI.

When the webserver start a CGI program, it creates new environment for it (in contrast, usually child program inherits the environment from parent). Several environment variables however are inherited from the webservers environment, they are: PATH, TMP, TEMP, TMPDIR, PERLLIB, MONGOOSE_CGI. 

On UNIX it is also LD_LIBRARY_PATH. On Windows it is also COMSPEC, SYSTEMROOT, SystemDrive, ProgramFiles, ProgramFiles(x86), CommonProgramFiles(x86).

    Default: **.cgi$|**.pl$|**.php$

###   enable_directory_listing

Enable directory listing, either true or false. Default: true.

**hide_file_patterns** A pattern for the files to hide. Files that match the pattern will not show up in directory listing and return 404 Not Found if requested. Pattern must be for a file name only, not including directory name, e.g. secret.txt|even_more_secret.txt. Default: not set.

###   index_files

Comma-separated list of files to be treated as directory index files. Default: index.html,index.htm,index.cgi,index.shtml,index.php

###   extra_mime_types

Extra mime types to recognize, in form extension1=type1,extension2=type2,.... Extension must include dot. Example:  .cpp=plain/text,.java=plain/text. Default: not set.

###   url_rewrites

Comma-separated list of URL rewrites in the form of uri_pattern=file_or_directory_path. When the webserver receives the request, it constructs the file name to show by combining document_root and the URI. However, if the rewrite option is used and uri_pattern matches the requested URI, then document_root is ignored. Instead, file_or_directory_path is used, which should be a full path name or a path relative to the web server's current working directory. Note that uri_pattern, as all webserver patterns, is a prefix pattern. If uri_pattern is a number, then it is treated as HTTP error code, and file_or_directory_path should be an URI to redirect to. The webserver will issue 302 temporary redirect to the specified URI with following parameters: ?code=HTTP_ERROR_CODE&orig_uri=ORIGINAL_URI&query_string=QUERY_STRING.

If uri_pattern starts with @ symbol, then the webserver compares it with the HOST header of the request. If they are equal, the webservr sets document root to file_or_directory_path, implementing virtual hosts support.

**Examples:**

    # Redirect all accesses to `.doc` files to a special script

* *.doc$=/path/to/cgi-bin/handle_doc.cgi

    # Implement user home directories support
    /~joe/=/home/joe/,/~bill=/home/bill/

    # Redirect 404 errors to a specific error page
    404=/cgi-bin/error.cgi

    # Virtual hosts example: serve foo.com domain from different directory
    @foo.com=/var/www/foo.com

### per_directory_auth_file

Leave as NULL to disable authentication. To enable directory protection with authentication, set this to ".htpasswd". Then, creating ".htpasswd" file in any directory automatically protects it with digest authentication. Use `mongoose` web server binary, or `htdigest` Apache utility to create/manipulate passwords file. Make sure `auth_domain` is set to a valid domain name.

### global_auth_file

Leave as NULL to disable authentication. Normally, only selected directories in the document root are protected. If absolutely every access to the web server needs to be authenticated, regardless of the URI, set this option to the path to the passwords file. Format of that file is the same as ".htpasswd" file. Make sure that file is located outside document root to prevent people fetching it.

### ssi_pattern

[SSI](https///sv.wikipedia.org/wiki/Server_Side_Includes) files pattern. If not set, "**.shtml$|**.shtm$" is used.

### ip_acl

IP ACL. By default, empty or nondefined, meaning all IPs are allowed to connect. 

### dav_document_root

[DAV](http://www.webdav.org/) document root. If NULL or undefined, DAV requests are going to fail.


##   websockets 

`<code=xml>`
`<websockets enable="true" auth="true" />`
`</code>`

Set **enable** to true to enable the interface and false to disable. The **auth** attribute tells if the authentication system should be active or not on the websocket interface. If set to false the websocket system is open to anyone and this setting __should never__ be true on a production system if you don't know what you are doing. 

:!: Both **enable** and **auth** is deprecated from version 12.29.1.13

----



 



## Remote user settings

Example

`<code=xml>`
`<remoteuser>` 
        `<user>`
            `<name>`admin`</name>`
            `<password>`d50c3180375c27927c22e42a379c3f67`</password>`
            `<privilege>`admin`</privilege>`
            `<allowfrom>`127.0.0.1,192.168.1.`</allowfrom>`
        `</user>`	
        `<user>`
            `<name>`user`</name>`
            `<password>`ee11cbb19052e40b07aac0ca060c23ee`</password>`
            `<privilege>`admin`</privilege>`
            `<allowfrom>`127.0.0.1,192.168.1.`</allowfrom>`
            `<allowevent>`10:6,10:7`</allowevent>`
        `</user>`	
    `</remoteuser>`
`</code>`

In the `<remoteuser» tab are settings that affect which users that can access the daemon through the TCP/IP interface, web interface, rest-interface and through the websocket inteface. Each user is defined between a <user>`…`</user>` pair. 

##   user

`<code=xml>`
`<name>`user`</name>`  
`</code>`

Username for this user.

###  password

`<code=xml>`
`<password>`password-hash`</password>`  
`</code>`

The password hash for the user. This hash should be the md5 over "username:vscptoken:password". The **mkpasswd**  utility that is distributed with the VSCP & Friends package can be user to generate the password. There are also plenty of services on the net that can be used. One example is [here](http://www.md5online.org/md5-encrypt.html).  

The vscptoken is set in the `<securitytab>` and it should never be sent on it's own over the wire.

###  privilege

`<code=xml>`
`<privilege>`level`</privilege>`
`</code>`

Privilege is the security level for this user. Users can have privileges from 0-15 that allow them to do command that are equal to or less then the set privilege level. Enter a privilege level as 0-15 here or in symbolic form as "//admin//" (==15) or "//user//" (==4) or "//driver//" (==6). Default is 4. 

###  allowfrom

`<code=xml>`
`<allowfrom>`comma-seperated-list`</allowfrom>`
`</code>`

This is a comma separated list with host addresses this user is allowed to log in to the server from. Wild cards can be used to indicate “all”. This mean that

    "*.*.*.*"

all users can log in from all remote machines. While

    "194.*.*.*"

means that he/she can log on from all remote machines with a IP addresses that starts with 194 and son on. Default is all so if you want to allow everyone to access your server you can skip this tag or leave it blank. 

###  allowevent

`<code=xml>`
`<allowevent>`comma-seperated-list`</allowevent>`
`</code>`

This is a comma separated list of events this user is allowed to send. The form is

    class:type,class:type

wild-cards (*)can be used for both. This means that 20:*,30:* means that the user is allowed to send all events in class=20 and class=30. Default allows user to send all events. "*.*" means the user is allowed to send anything. And "20:1,20:2,20:10" that the user is allowed to send event with Class=20 and Type=1 and Class=20 and Type=2 and Class=20 and Type=10

### mask

`<code=xml>`
`<mask priority="xxx" class="xxx" type="xxx" GUID="xxx" />`
`</code>`

Set the mask for incoming events to a client. The mask has a bit set for a binary digit that is of interest. Default is all is null == disabled. It is important that filter and mask is both set if they are used.

### filter

`<code=xml>`
`<filter priority="xxx" class="xxx" type="xxx" GUID="xxx" />`
`</code>`

This is the event filter. Set the filter for incoming events to a client. The form is If a mask bit is set to one for a position then the filter bit must be equal to the bit of the incoming event for the client to get it. It is important that filter and mask is both set if they are used.

##  Automation

**Example**

`<code=xml>`
`<automation enable="true|false>`
    `<zone>`1`</zone>`
    `<sub-zone>`2`</sub-zone>`
    `<longitude>`61.7441833`</longitude>`
    `<latitude>`15.1604167`</latitude>`
    `<sunrise enable="true" />`
    `<sunrise-twilight enable="true" />`
    `<sunset enable="true" />`
    `<sunset-twilight enable="true" />`
    `<segmentcontrol-event enable="true" interval="60" />`
    `<heartbeat-event enable="true" interval="60" />`
    `<capability-event enable="true" interval="60" />`
`</automation>`
`</code>`

In the `<automation>` tab settings that affect the internal VSCP functionality of the server is located. 

### enable

Enable or disable automation interface

### zone

This is the zone for the server 0-254. 

### sub-zone

This is the sub-zone for the server 0-254. 

### longitude

Longitude for place where daemon is located in decimal form. 

### latitude

Latitude for place where daemon is located in decimal form. 

### sunrise

Enable/disable the CLASS1.INFORMATION, Type=44 (Sunrise) to be sent. Longitude, latitude and timezone must be set for this to work correctly.

`<code=xml>`
`<sunrise enable="true|false" />`
`</code>`

### sunrise-twilight

Enable/disable the CLASS1.INFORMATION, Type=52 (Civil sunrise twilight time) to be sent. Longitude, latitude and timezone must be set for this to work correctly.

`<code=xml>`
`<sunrise-twilight enable="true|false" />`
`</code>`

### sunset

Enable/disable the CLASS1.INFORMATION, Type=45 (Sunset) to be sent. Longitude, latitude and timezone must be set for this to work correctly.

`<code=xml>`
`<sunset enable="true|false" />`
`</code>`

###  sunset-twilight

Enable/disable the CLASS1.INFORMATION, Type=53 (Civil sunset twilight time) to be sent. Longitude, latitude and timezone must be set for this to work correctly.

`<code=xml>`
`<sunset-twilight enable="true|false" />`
`</code>`

### segmentcontrol-event

Enable/disable the periodic CLASS1.PROTOCOL, Type=1 (Segment Status Heartbeat) event to be sent from the daemon. The interval between events is set in seconds (default=60). The event will be sent on all interfaces if enabled.

`<code=xml>`
`<segmentcontrol-event enable="true|false" interval="seconds" />`
`</code>`

### heartbeat-event

Enable/disable the CLASS1.INFORMATION, Type=9 (Node Heartbeat) to be sent. The interval between events is set in seconds  (default=60).

`<code=xml>`
`<heartbeat-event enable="true|false" interval="seconds" />`
`</code>`

### capabilities-event

Enable/disable the CLASS2.PROTOCOL, Type=20 (High end server capabilities) to be sent. The interval between events is set in seconds  (default=60).

`<code=xml>`
`<capabilities-event enable="true|false" interval="seconds" />`
`</code>`

----
##  Level I Drivers

Enable/disable daemon Level I driver interface. If disabled no Level I drivers will be loaded. Default is enabled. This is the driver type that in the past used to be called CANAL (CAN Abstraction Layer) drivers.



### level1driver

`<code=xml>`
`<level1driver enable=”true|false” />`
`</code>`

The content consist of one or several driver entries each representing a level 1 driver that should be used. 

##### enable

Set to true to enable level I driver functionality , set to false to disable. This is convenient if one wants to disable all level I drivers without removing them from the configuration file.

#### driver

`<code=xml>`
`<driver enable="false">`
`</code>`

A level I driver entry.

##### enable

The driver should be loaded if set to true. If set to false the driver will not be loaded.

##### name

`<code=xml>`
`<name>`driver-name`</name>`
`</code>`

A name given by the user for the driver. This is the name by which the driver is identified by the system.

##### config

`<code=xml>`
`<config>`config1;config2`</config>`
`</code>`

The semicolon separated configuration string for the driver. The meaning of this string is driver specific.

##### flags

`<code=xml>`
`<flags>`0`</flags>`
`</code>`

A 32 bit driver specific number that have bits set that have special meaning for a driver. See the documentation for a specific driver for an explanation of the meaning of each flag bit for that driver.

##### path

`<code=xml>`
`<path>`path-to-driver`</path>`
`</code>`

Path to where the driver is located. A dll file on Windows or a .so dl file on Linux etc.

##### guid

`<code=xml>`
`<guid>`guid-for-driver`</guid>`
`</code>`

The GUID that the driver will use for it's interface. This means that this is the GUID that will be the source of Level I events but only if the GUID is not set to all zeros in which case the generated interface GUID from the general section will be used as the base for the GUID. 

Note that the GUID set here will have the two least significant bytes replaced by the node id so when set here they should be set to zero.

##### Translation

`<translation>` is a list of semicolon separated fields with translations that should be carried out by the system on event passing through driver.

 | Mnemonic                            | Description                                                                                                          | 
 | --------                            | -----------                                                                                                          | 
 | **OUT_MEASUREMENT_L1_TO_L2_FLOAT**  | On events from the driver (OUT) translate Level I measurement events to Level II measurement, floating point events. | 
 | **OUT_MEASUREMENT_L1_TO_L2_STRING** | On events from the driver (O - output) translate Level I measurement events to Level II measurement, string events.  | 
 | **OUT_ALL_TO_L1_OVER_L2**           | All outgoing events will be translated to class Level I events over Level II that is have 512 added to it's class.   | 

##### Known nodes

`<known-nodes>` is used for the symbolic node reporting and access system. It is entirely optional. GUID entered should be the real GUID for the node, not the interface GUID. Names can be any length but will be truncated to 64-bytes max when reported. This tag must always be the last tag within a driver tag.

** Example **

`<code=xml>`
`<leve1driver enable="true" >` 

        `<!-- The level I logger driver  -->`
        `<driver enable="false">`
            `<name>`logger`</name>`
            `<config>`/tmp/canallog.txt`</config>`
            `<path>`/ust/local/lib/canallogger.so`</path>`
            `<flags>`1`</flags>`
            `<guid>`00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:00`</guid>`
            `<translate>`OM1M2F`</translate>`
        `</driver>`

        `<!-- The can232 driver -->`
        `<driver enable="false" >`
            `<name>`can232`</name>`
            `<config>`/dev/ttyS0;19200;0;0;125`</config>`
            `<path>`/usr/local/lib/can232drv.so`</path>`
            `<guid>`00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:00`</guid>`
            `<flags>`0`</flags>`
        `</driver>`

        `<!-- The xap driver is documented  -->`
        `<driver enable="false" >`
            `<name>`xap`</name>`
            `<config>`9598;3639`</config>`
            `<path>`/usr/local/lib/xapdrv.so`</path>`
            `<flags>`0`</flags>`
            `<guid>`00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:00`</guid>`
            `<known-nodes>`
                `<node guid="00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:01" name="Known node1" />`
                `<node guid="00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:02" name="Known node2" />`
                `<node guid="00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:03" name="Known node3" />`
                `<node guid="00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:04" name="Known node4" />`
            `</known-nodes>`
        `</driver>`

    `</leve1driver>`
`</code>`

----

## Level II drivers

Level II drivers can handle the full VSCP abstraction and don't have the limitation of the Level I drivers. 



###  level2driver 

`<code=xml>`
`<leve2driver enable=”true|false” />`
`</code>`

Enable/disable daemon Level II driver interface. If disabled no Level II drivers will be loaded. Default is enabled. 

#### driver

`<code=xml>`
`<driver enable="false">`
`</code>`
The content consist of one or several driver entries each representing a driver that should be used. 

##### enable

The driver should be loaded if set to true. If false the driver will not be loaded.

##### name

`<code=xml>`
`<name>`driver-name`</name>`
`</code>`

A name given by the user for the driver.

##### config

`<code=xml>`
`<config>`config1;config2`</config>`
`</code>`

The semi colon separated configuration string for the driver. The meaning of this string is driver specific.

#####  guid

`<code=xml>`
`<guid>`guid-for-driver`</guid>`
`</code>`

The GUID that the driver will use for it's interface.

##### translation

See explanation for Level I drivers above.

##### known-nodes

See explanation for Level I drivers above.

**Example**

`<code=xml>`
`<level2driver enable="true|false">` 	

    `<driver enable="true|false" >`
        `<name>`test`</name>` 				
        `<path>`path_to_driver`</path>`
        `<config>`param2;param2;param3;...`</config>` 		
        `<guid>`00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:00`</guid>`
        `<known-nodes>`
            `<node guid="00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:01" name="Known node1" />`
            `<node guid="00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:02" name="Known node2" />`
            `<node guid="00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:03" name="Known node3" />`
            `<node guid="00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:04" name="Known node4" />`
        `</known-nodes>`
    `</driver>` 	

`</level2driver>`
`</code>`

----

## tables

`<code=xml>`
`<tables>`
    `<table ....... />`
    `<table ....... />`
    `<table ....... />`
    ...    
`</tables>`
`</code>`

Tables makes it possible to define tables that collect data (measurements). Tables can be of three types. **Type="dynamic"** which is a standard table that grows over time and **type="static"** which is a static table that have a fixed size and **type="max"** which is a table that grows up to a specified size and then is cleared and start to fill the table again.

You can access the written table data through the websocket, TCP/IP or the REST interface.

#### table

`<code=xml>`
<table enable="true|false"
    name="testtable1"
    bmemory="false|true"
    type="dynamic|static|max"
    size="0"
    
    owner="admin"
    permission="0x777"
    
    vscp-class="10"
    vscp-type="6"
    vscp-sensor-index="0"
    vscp-unit="1"
    vscp-zone="0"
    vscp-subzone="0"

    xname="x-label name"
    yname="y-label name"
    title="Title for diagram"
    note="Note for diagram"

    sql-create="SQL expression to create table"
    sql-insert="SQL expression to insert value into table"
    sql-delete="SQL expression to delete table"

    description="Description of database"
/>
`</code>`

This is the definition of a table. It defines the information for the table but does not enable any writing of data to the table. This must be done with a VSCP decision matrix entry and is [described here](http://www.vscp.org/docs/vscpd/doku.php?id=vscp_daemon_decision_matrix#write_table).

##### enable

If set to true (enable="true") the table will be read in. If set to false (enable="false" the table will not be read in.

##### name

This is the name of the table. It must be unique in the system and this is also the name the database file will get.

##### bmemory

If bMemory is set to true (bMemory="true") the database will be created in memory. This s a very fast database but the data will be lost when the VSCP daemon is restarted. Care must also be taken on how much you let this database grow in size and therefore it may we wise to make this database static. 

If set to "false" (bMemory="false") the database will be created on disk. This means it will be possible to work with by other tools as well and that it is persistent over time.

##### type

This is the type for the table. Two types are available. The first is **dynamic** (type="dynamic") which is a table that grows over time with not limits. The second type is **static** (type="static") which is a table with a fixed size. A fixed size table is perfect if you want to collect data for the last 24 hours, the last week or similar while the normal table is more like a traditional database.

##### size

Size only have meaning for a table with type="static" and then set the max size for the table. Every time data is added to the table the size will be checked and the oldest records will be removed until the size is equal to the value set here.

##### owner

Is the user that owns this table.

##### permission

Is the rights for users to read/write records in this table.

##### vscp-class

This is the VSCP class data is collected from.

##### vscp-type

This is the type of the VSCP class data is collected from.

##### vscp-sensor-index

This is the sensor index for the sensor data is collected from. Default to zero. Can be max 7 for Level I events and have a value of max 255 for Level II events.

##### vscp-unit

This is the unit data is collected for. Defaults to the default unit zero. Not used if the event dos not have a zone specified. Can be max 3 for Level I events and have a value of max 255 for Level II events.

##### vscp-zone

This is the zone the sensor data is collected from should be part of. Defaults to 255 (all zones). Not used if the event dos not have a zone specified.

##### vscp-subzone

This is the subzone the sensor data is collected from should be part of. Defaults to 255 (all subzones).

##### xname

This is the x label name for a UI diagram or the column name for a UI table. Used for click and play diagrams and tables.

##### yname

This is the Y label name for a UI diagram or the column name for a UI table. Used for click and play diagrams and tables.

##### title

This is the title for a UI diagram or a UI table. Used for click and play diagrams and tables.

##### note

This is a note for a UI diagram or a UI table. Used for click and play diagrams and tables.

##### sql-create

This is the SQL expression used to create the database for the table if it does not exist. For a in memory table this means every time the VSCP daemon is started. If this attribute is left blank it will be set to the following SQL expression

`<code="sql">`
CREATE TABLE 'vscptable' ( `idx` INTEGER NOT NULL PRIMARY KEY UNIQUE, `datetime` TEXT, `value` REAL DEFAULT 0 );
`</code>`

But you can use your own SQL expression to create tables with other fields. The following restrictions is the only ones


*  One table must be named **vscptable** but you are free to create any number of tables with other names in the database.

*  The vscptable must have the field **datetime** defined which is of type **TEXT** and the field **value** defined which is of type **REAL**.

Before the expression is executed by the SLQ engine [VSCP decision matrix escapes](http://www.vscp.org/docs/vscpd/doku.php?id=vscp_daemon_decision_matrix#variable_substitution_for_parameters_escapes) are replaced with real values.

##### sql-insert

This is the SQL expression used to insert data into the table. If this attribute is left blank it will be set to the following SQL expression

`<code="sql">`
INSERT INTO 'vscptable' (datetime,value) VALUES ('%%s','%%f');
`</code>`

You can set your own SQL expression. But **datetime** must be followed by a %%s somewhere and in the same way the **value** need to be followed by %%f somewhere. The order does not matter. %%s will be replaced by %s (string insert) by the [VSCP decision matrix escapes](http://www.vscp.org/docs/vscpd/doku.php?id=vscp_daemon_decision_matrix#variable_substitution_for_parameters_escapes) handling and %%f will be replaced by %f (floating point value insert) by the [VSCP decision matrix escapes](http://www.vscp.org/docs/vscpd/doku.php?id=vscp_daemon_decision_matrix#variable_substitution_for_parameters_escapes) handling.

Before the expression is executed by the SQL engine [VSCP decision matrix escapes](http://www.vscp.org/docs/vscpd/doku.php?id=vscp_daemon_decision_matrix#variable_substitution_for_parameters_escapes) are replaced with real values. So many other values and content of variables can be inserted in the table when a new datapoint is inserted.

##### sql-delete

To be defined

##### description

This is a description of the table.

##  Example 1 

`<code="xml">`
<table enable="true" 
    bmemory="false"
    type="static"
    name="test1"
    owner="admin"
    permission="0x777"
    labelx="Time"
    labely="Temperature in degrees Celsius"
    title="Temperature outside"
    note="Diagram showing temperature outside at s:t Claus house at the North pole fetched from a Kelvin NTC10K module."
    size="0"
    sqlcreate="CREATE TABLE 'vscptable' ( `idx` INTEGER NOT NULL PRIMARY KEY UNIQUE, `datetime` TEXT, `value` REAL DEFAULT 0, `event` TEXT, 'timestamp  INTEGER DEFAULT 0' );"
    sqlinsert="INSERT INTO 'vscptable' (datetime,value,event,timestamp) VALUES ('%%s','%%f','%event','%event.timestamp');"
    sqldelete="DELETE FROM 'vscptable' WHERE idx IN (SELECT idx FROM temp ORDER BY idx ASC LIMIT 1);"    
    vscpclass="10"
    vscptype="6"
    vscpsensorindex="0"
    vscpunit="1"
    vscpzone="255"
    vscpsubzone="255"
    description="Temperature data collections from the North pole s:t Clause house, Kelvin NTC10K, Sensor 3"
/>
`</code>`

----
\\ 
----
{{  ::copyright.png?600  |}}

`<HTML>``<p style="color:red;text-align:center;">``<a href="http://www.grodansparadis.com">`Grodans Paradis AB`</a>``</p>``</HTML>`
