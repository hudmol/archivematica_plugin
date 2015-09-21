Integration endpoints for Archivematica.

## Installation

    cd /path/to/your/archivesspace/plugins
    git clone https://github.com/hudmol/archivematica_plugin.git

Then edit the ArchivesSpace `config/config.rb` and add the plugin to the list:

    AppConfig[:plugins] = ['local', 'other_plugins', 'archivematica_plugin']


## Using it

Once installed, the ArchivesSpace backend will respond to some
additional queries:

### Find Digital Object Components by component_id

     > GET /repositories/2/find_by_id/digital_object_components?component_id[]=123 HTTP/1.1
     > User-Agent: curl/7.38.0
     > Host: localhost:8089
     > Accept: */*
     > X-ArchivesSpace-Session: [token]
     >
     < HTTP/1.1 200 OK
     < Content-Type: application/json
     < Cache-Control: private, must-revalidate, max-age=0
     < X-Content-Type-Options: nosniff
     < Content-Length: 86

     {
         "digital_object_components": [
             {
                 "ref": "/repositories/2/digital_object_components/1"
             }
         ]
     }


### Find Archival Objects by ref_id

     > GET /repositories/2/find_by_id/archival_objects?ref_id[]=987 HTTP/1.1
     > User-Agent: curl/7.38.0
     > Host: localhost:8089
     > Accept: */*
     > X-ArchivesSpace-Session: [token]
     >
     < HTTP/1.1 200 OK
     < Content-Type: application/json
     < Cache-Control: private, must-revalidate, max-age=0
     < X-Content-Type-Options: nosniff
     < Content-Length: 72

     {
         "archival_objects": [
             {
                 "ref": "/repositories/2/archival_objects/25324"
             }
         ]
     }

### Find Archival Objects by component_id

This uses the same endpoint with a different parameter name:

     > GET /repositories/2/find_by_id/archival_objects?component_id[]=987 HTTP/1.1
     ...

If you specify both `ref_id[]` and `component_id[]` parameters, you
get back records matching either identifier (a logical OR).

### Finding and getting records in a single request

If you pass an extra parameter like:

     resolve[]=archival_objects&resolve[]=digital_object_components

Then ArchivesSpace will return the full JSON object representing each
record found.  The response structure is the same, but you'll get an
extra '_resolved' key in each object that contains the record itself.
For example:

     {
         "archival_objects": [
             {
                 "ref": "/repositories/2/archival_objects/25324",
                 "_resolved": {
                     "title": "Some ArchivalObject",
                      "resource" {
                          "ref": "/repositories/2/resources/555"
                      },

                      ...
                 }
             }
         ]
     }
