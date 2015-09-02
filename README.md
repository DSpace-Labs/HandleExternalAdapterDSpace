# Handle External Adapter for DSpace

Handle External Adapter for DSpace (HEAD, aka Handle for DSpace) is a gradle application that builds a plugin for the handle.net standalone/stock software, and allows you to connect your DSpace instance to the external/standalone handle software, and the only connection between handle and DSpace is by handle querying DSpace over HTTP, such as http://dspace.example.com/handleresolver/resolve/123456789/12

  - configure 'dspace.handle.endpoint1' in src/main/resources/handle-dspace-plugin.cfg
  - gradle awesomeFunJar
  - java -jar build/libs/HandleExternalAdapterDSpace-standalone.jar
  - Configure your handle server

