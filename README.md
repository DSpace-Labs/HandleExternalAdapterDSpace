# Handle External Adapter for DSpace

A plugin for the Handle Server to externally resolve handles to one or more DSpace instances, using just HTTP. (no need to open ports 2641 and 8000 on every DSpace application server). This is a gradle application, it produces a jar file that you copy into the handle server lib directory.
Gradle is a build automation tool (like maven). The Gradle Wrapper ./gradlew or gradle.bat (for windows) makes this project runnable without requiring you to have installed prerequisites (gradle wrapper will install gradle for you).

All DSpaces since DSpace 4 include a small web service for handle resolution:

List Handle Prefix, the handle prefix for this instance: https://trydspace.longsight.com/handleresolver/listprefixes
["123456789"]

Resolve a handle. Given a handle prefix/suffix, it will return the DSpace url for that collection or item.
https://trydspace.longsight.com/handleresolver/resolve/123456789/67
["https://trydspace.longsight.com/handle/123456789/67"]

Since this handle adapter supports multiple external DSpaces, incoming resolution requests to your handle server will send handle lookups to the instance that has that prefix.


## Configure Plugin / Build a Jar

git clone https://github.com/DSpace-Labs/HandleExternalAdapterDSpace.git
cd HandleExternalAdapterDSpace

Edit src/main/resources/handle-dspace-plugin.cfg to have the handle resolver endpoint for your DSpace.
dspace.handle.endpoint1 = https://trydspace.longsight.com/handleresolver
dspace.handle.endpoint2 = http://dc.statelibrary.sc.gov/handleresolver
...

To build the fat jar that contains the plugin, and all missing runtime dependencies.
Linux:
./gradlew fatjar

Windows:
gradle.bat fatjar

That produces build/libs/HandleExternalAdapterDSpace-all.jar

## Configure Handle Server
The assumption is that you have a stock handle server from http://www.handle.net/download.html, follow the INSTALL.txt.
Handle will be installed at: /hs/hsj-7.3.1
Configuration will go in: /hs/svr_1


Copy the dspace handle fat jar plugin to handle's /lib/ directory.
cp build/libs/HandleExternalAdapterDSpace-all.jar /hs/hsj-7.3.1/lib/

Edit /hs/srv_1/config.dct to include the following lines in the "server_config" clause:
"storage_type" = "CUSTOM"
"storage_class" = "org.dspace.handle.MultiRemoteDSpaceRepositoryHandlePlugin"

Copy /hs/hsj-7.3.1/bin/hdl-server to /hs/srv_1/start-hdl-server
Edit /hs/srv_1/start-hdl-server

Around line 20, replace:
# Get the full name of the directory where the Handle system is installed
HDLHOME=`dirname "$PRG"`
HDLHOME="${HDLHOME}/../"

With:
# Get the full name of the directory where the Handle system is installed
HDLHOME="/hs/hsj-7.3.1/"

Stop the handle server, if its already running. Then, start the handle server:
/hs/srv_1/start-hdl-server

Check the handle logs for more information:
/hs/svr_1/logs/error.log-201509



Side Note #1: Gradle can produce a skinny-jar of this plugin with ./gradlew assemble. It gets generated to: build/libs/HandleExternalAdapterDSpace.jar, you will need to add log4j.jar and commons-lang.jar for this to work with handle. Don't use both the skinny-jar and the fat-jar. If in doubt, just use the fat jar method.
Side Note #2: You can test the handle resolver with ./gradlew run


## Other Notes

Other guidance on how to configure handle daemon.
https://wiki.duraspace.org/display/~pbecker/MultiRemoteDSpaceRepositoryHandlePlugin

