# Introduction to the VSCP Daemon Software

There are so many capabilities of the VSCP daemon so even how many words we ever write we will only just touch on it's capabilities, and even if we managed to describe all features they will still be extended by you and others with drivers and other things to give the software new amazing capabilities. It's a moving target.

Yes and it's **open source**. **Free to download** and modify. And yes it is also **free to use**. And yes if you want a **commercial license** you can get one by contacting [sales@grodansparadis.com](mailto/sales@grodansparadis.com). 

###  Why buy a commercial license

*  You don't have to worry about legal issues related to GPL licensed software

*  Modifications to the code do not have to be released under the open source version and can be integrated in a closed source project

*  Source codes, copyright notices and licenses for Grodans Paradis AB / Paradise of the Frog do not have to be distributed with your product

*  You receive priority support in case of bugs or feature requests

*  You receive a heads-up in case of upcoming Security Advisories

*  Integration support is available if you need help integrating VSCP & Friends in your product

The VSCP daemon is like a HUB. It can connect to other VSCP daemons forming larger systems. It can connect to your devices. They may be VSCP enabled devices or not. The driver system of the daemon served as a hardware abstraction layer for anything that is out there. Two sorts of drivers are available. **Level I** and **Level II** with slightly different capabilities. It is easy to construct new drivers and there are plenty of sample code on how to do it available.

The VSCP daemon handles **variables**. Variables is just persistent typed objects that can be reached and/or read/updated from remote interfaces or when things happen in the system. A typical variable can be the state of a physical thing out in the world. The "thing" updates it's status from time to time and then also updates the variable. A variable may also be the energy consumption over a mount or a year. Variables can be viewed as a sort of database but with discrete values.

The daemon exports a **tcp/ip interface** that other services and humans can connect to and and perform tasks on a VSCP daemon system from a remote location. The interface is protected with user/password credentials and a system that only allows host you specify to connect. On top of this SSL can also be enabled for the interface giving it **state of the art security**.

The VSCP daemon have a full **web server** that can server users with local web pages as any other web seserver but also have a built in configuration interface to remotely manage the VSCP daemon. Also this server have the same security as the tcp/ip interface. It is even possible to extend it with cgi scripts.

A **REST interface** is available on the daemon that can be used by clients to log and read information and to control and be controlled. SSL, username/password and limited remote host capabilities are enabled also here.

The **websocket interface** can be used to build beautiful dynamic web pages for presenting and interacting with devices. Suddenly this becomes a very simple task. Same security level is available here as for tcp/ip/web and rest interfaces.

A local **scheduler** makes it possible to construct build advanced control systems. It is combined with a **decision matrix** which can be programmed to perform things when other things happen. Typical samples are 

 * Send an event when something happen
 * execute an external program when something happens.
 * Log data to a table that later can be displayed as a dynamic diagram or a table on a web page.
 * Handle variables.
 * deliver/collect data to/from a remote web site.

And there is more as we said above...






\\ 
----
{{  ::copyright.png?600  |}}

`<HTML>``<p style="color:red;text-align:center;">``<a href="http://www.grodansparadis.com">`Grodans Paradis AB`</a>``</p>``</HTML>`
