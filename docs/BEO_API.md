# BEO API

These are all channel based comands for transcoder:

 - start channel
 - stop channel
 - status
 - get config
 - create config
 - modify config
 - delete config
 - list config names
 - slate start 
 - slate stop 
 - slate select 
 - slate upload    TBD
 - slate delete    TBD
 - slate preview   TBD

These are all channel based comands for splitter:

 - start 
 - stop 
 - create config
 - modify config
 - status


PUT is idempotent
POST is not idempotent

-----------------------------------------------------------------------
## Start Channel
    
### Description

This is the command to start a channel in the ADI.
If the chid is not specified, then it defaults to zero.
This rule is applied to all endpoints.

### HTTP Request

    PUT /v1/beo/channel/<chid>

body:

    {
        "command": "start";
        "config" : { reference 'config' object }
    }

in this case the config is applied to the default config name 'config_`<chid>`'
and that config name is used if the channel is accidentally restarted or crashes
or the unit is power cycled

or:

    {
        "command": "start";
        "config_name": "abchd";
    }

in this case the config named "abchd" is loaded for the channel, that config
is used if the channel is accidentally restart, crashes, or the unit is power
cycled.

or:

    {
        "command": "start";
        "config_name": "abchd";
        "config" : { reference 'config' object }
    }

in this case the config object is saved in the file name "abchd", then the 
channel is started with that config. The config "abchd" is used if the channel
is accidentally restarted, crashes, or the unit is power cycled.

### Response Body


    { "status" : "ok", }

or:

    { "error"  : "some error string", }



-----------------------------------------------------------------------
## Stop Channel
    
### Description

This is the command to start a channel in the ADI.

### HTTP Request

    PUT /v1/beo/channel/<chid>

body:

    {
        "command": "stop";
    }


### response body

    { "status" : "ok", }

or:

    { "error"  : "some error string", }

-----------------------------------------------------------------------
## Status
    
### Description

This is the command to get the runtime status of a channel

### HTTP Request

    GET /v1/beo/channel/<chid>

### response body

    { 
        "status" : { contents of the ch<n>_status.json }
    }


or:

    { "error"  : "some error string", }


or:

    { 
        "status" : { contents of the ch<n>_status.json }
        "error"  : "some error string";
    }

The error field is optional and is present if there is a run time error.


-----------------------------------------------------------------------
## Get Config
    
### Description

This is the command to get a config for a channel

### HTTP Request

    GET /v1/beo/channel/<chid>/config
    GET /v1/beo/channel/<chid>/config?name="abcdhd"

If the name is not specified, the default config is returned. 
The default config is '`config_<chid>`'.

body: None

### response body

    { 
        "status" : "ok"; 
        "config" : { reference "config" object }
    }

or:

    { 
        "status" : "ok"; 
        "config_name": "abchd";
        "config" : { reference "config" object }
    }

or:

    { "error"  : "some error string", }

-----------------------------------------------------------------------
## Create/Modify Config
    
### Description

This is the command to create a config for a channel

### HTTP Request

    PUT /v1/beo/channel/<chid>/config/<name>

body:

    {
        "config" : { reference "config" object }
    }


### response body

    { "status" : "ok", }
 
or:

    { "error"  : "some error string", }

-----------------------------------------------------------------------
## Delete Config
    
### Description

This is the command to delete a config for a channel

### HTTP Request

    DELETE /v1/beo/channel/<chid>/config/<name>

body:

    None

### response body

    { "status" : "ok", }

or:

    { "error"  : "some error string"; }

-----------------------------------------------------------------------
## List Config Names
    
### Description

This is the command to get a list of configs for a channel

### HTTP Request

    GET /v1/beo/channel/<chid>?list=config_names

body:

    {
      [ "abchd1",
        "abchd2",
        "cnnhd",
        "fnchd",
        "espnhd" ]
    }

### response body

    { "status" : "ok", }

or:

    { "error"  : "some error string", }

-----------------------------------------------------------------------
## slate start
    
### Description

This is the command to start the slate on a channel

### HTTP Request

    PUT /v1/beo/channel/<chid>

    body:
    {
        "command" : "start_slate";
    }

### response body

    { "status" : "ok", }
    or 
    { "error"  : "some error string", }

-----------------------------------------------------------------------
## slate stop
    
### Description

This is the command to start the slate on a channel

### HTTP Request

    PUT /v1/beo/channel/<chid>

    body:
    {
        "command" : "stop_slate";
    }

### response body

    { "status" : "ok", }
    or 
    { "error"  : "some error string", }


-----------------------------------------------------------------------
## slate select
    
### Description

This is the command to select a new slate image file. If the channel is
running, then the transcoder will pick up the new file while running.
If the channel is not running, then the config file is updated.

### HTTP Request

    PUT /v1/beo/channel/<chid>

    body: 
    {
        "command" : "select_slate";
        "config_name": "abchd";
        "slate_config"              : { reference "slate_config" object };
    }

### response body

    { "status" : "ok", }
    or 
    { "error"  : "some error string", }


-----------------------------------------------------------------------
## slate upload
    
### Description

This is the command to upload a slate image file

### HTTP Request

    PUT /v1/beo/slate/

    body: TBD
    body: 
    {
        "command": "upload";
        "slate_config"              : { reference "slate_config" object };
    }

### response body

    { "status" : "ok", }
    or 
    { "error"  : "some error string", }

-----------------------------------------------------------------------
## slate delete
    
### Description

This is the command to delete a slate image file
QUESTION: what happens if you delete a slate image that is in use? 
ANSWER: you should not use it any more

### HTTP Request

    DELETE /v1/beo/slate

    body: 
    {
        "filename"              : { "filename for slate image" };
    }

### response body

    { "status" : "ok", }
    or 
    { "error"  : "some error string", }






-----------------------------------------------------------------------
## Json Objects:

[Json Schema](./schemas/schema.json)

# Implementation

    We would map commands with channels in them to ports on the docker images

    PUT (addr:80) /v1/beo/channel/1?cmd=start   ->   PUT (addr:8001) /v1/beo/channel?cmd=start  
    PUT (addr:80) /v1/beo/channel/2?cmd=start   ->   PUT (addr:8002) /v1/beo/channel?cmd=start  
    PUT (addr:80) /v1/beo/channel/2?cmd=stop    ->   PUT (addr:8002) /v1/beo/channel?cmd=stop  

    In the docker invocation, you can map 8001 to port 80 on the docker image for channel 1.
    In the docker invocation, you can map 8002 to port 80 on the docker image for channel 2.

