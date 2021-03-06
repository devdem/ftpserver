osgi-ftpserver
==============

Version 1.0.6 of [Apache FTP Server](http://mina.apache.org/ftpserver-project/) presents a few challenges when you need to customise its behvaiour in an OSGi environment. This build adjusts the packaging to address a number of these limitations:-

It isn't possible to inject implementations of the ftplet API interfaces
------------------------------------------------------------------------
This is covered by the (unreleased) JIRA [FTPSERVER-425](https://issues.apache.org/jira/browse/FTPSERVER-425) which addresses the OSGi private packaging of a number of interfaces, in particular its private copy of the interfaces in the `org.apache.ftpserver.ftplet`
package. Since the private copy of these interfaces is always used by the FTP server in an OSGi environment, they are not assignable from classes implementing the versions in the `ftplet-api` bundle.

Implementation packages are not exported
----------------------------------------
This *shouldn't* be a problem, but for a number of customisation scenarios it currently is. 

 * If you want to implement a custom `Command` for example, two of its `execute` method parameters are instances of classes in `org.apache.ftpserver.impl` which is not exposed by default.
 
 * If you want to handle different types of `AuthorizationRequest` in a custom `User` implementation you can't access properties of specific authorisations (such as `WriteRequest` as their implementations in `org.apache.ftpserver.usermanager.impl` are not exposed.
 
 * If you want to customise or otherwise wrap standard commands, such as wrapping a custom read permission handler around the RETR class, you can't easily delegate to the default commands as their package `org.apache.ftpserver.command.impl` is not exposed. 

MINA 2.0.9 broken OSGi imports
------------------------------
Between Apache MINA 2.0.8 and 2.0.9 explicit OSGi imports have been set in the bundle builder in pom.xml. Unfortunately these incompletely replace those that had been generated by default, and remove such required imports as javax.net.ssl. Deploying to an OSGi container that has MINA 2.0.9 provisioned prevents the FTP server from starting. 


This fork creates a 1.0.6-osgi build version which addresses the above issues. Whilst the `impl.*` dependencies are better addressed by refactoring, here we're just ensuring the required dependencies are exported and the imported version of MINA is restricted to no higher than 2.0.8.


 