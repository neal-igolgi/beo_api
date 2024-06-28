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

body: 

    None

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

examples:

    { "status"  : { "state": "stopped" } }

    { "status" : 
        {
            "state" : "running",
            "Config": "/var/www/configs/t2_b25_c01_secv8hd_LVM.cfg",
            "_audio_only": 0,
            "_preview_image_id": 1719584959,
            "APP STATE": 1,
            "INPUT SIGNAL": "1",
            "INPUT": {
                "": "",
                "INPUT PRIMARY": "eth1 (239.243.202.78:12000)",
                "INPUT DECODER": "MPEG2:Software",
                "INPUT VIDEO": "MPEG2: 1920 x 1080 @ 29.97 fps : AVG : 14.61 mbps",
                "INPUT VIDEO PID": " 1011 (0x  3f3)",
                "INPUT PMT PID": " 1010 (0x  3f2)",
                "INPUT AUDIO 1": "AC3 - PID:  1012 (0x  3f4) @ 288.721 kbps, 48000 Hz",
                "SLATE": "ENABLED",
                "SLATE STATE": "INACTIVE, NORMAL PASSTHRU",
                "SLATE V INSERT": "0",
                "SLATE A INSERT": "0,0,0,0"
            },
            "OUTPUT": {
                "": "",
                "OUTPUT MODE": "MULTIRATE TS",
                "OUTPUT PRIMARY": "eth1 (233.2.25.1:9001)",
                "STREAM 1": "H.264:  512 x  384 @ 1000 kbps Q: 8  30.00 fps AUDIO: 96 kbps (HIGH)",
                "_output_url_1_1": "udp://@233.2.25.1:9001",
                "STREAM 2": "H.264:  960 x  540 @ 2000 kbps Q: 8  30.00 fps AUDIO: 96 kbps (HIGH)",
                "_output_url_1_2": "udp://@233.2.25.1:9002",
                "STREAM 3": "H.264: 1280 x  720 @ 4500 kbps Q: 8  30.00 fps AUDIO: 96 kbps (HIGH)",
                "_output_url_1_3": "udp://@233.2.25.1:9003",
                "STREAM 4": "H.264: 1280 x  720 @ 7000 kbps Q: 12  60.00 fps AUDIO: 96 kbps (HIGH)",
                "_output_url_1_4": "udp://@233.2.25.1:9004",
                "STREAM 5:1": "AUDIO ONLY: AAC, channels: 2, bitrate 96 kbps"
            },
            "EXTRA": {
                "": "",
                "ORIGIN PUBLISH": {
                    "": "",
                    "SERVER": "http://10.193.118.254:8089",
                    "MEDIA NAME": "secv8hd",
                    "PUBLISHING POINT": "233.2.25.1",
                    "STATUS": "ERROR COMMUNICATING WITH CIM"
                }
            },
            "STATUS": {
                "": "",
                "FRAMES PROCESSED": "40928",
                "UP TIME": "22m 47s since last re-sync",
                "RESYNC COUNT": "2",
                "CLOCK DIFF": "122402 (SOURCE VS NTP)",
                "PROC": "88260"
            }
        }
    }
    
This is the ch_status of a non running channel:

    {
        "Config": "/usr/bin/mpeg2ts.cfg",
        "_audio_only": 0,
        "_preview_image_id": 0,
        "APP STATE": 0,
        "INPUT SIGNAL": "0",
        "INPUT": {
            "": "",
            "INPUT PRIMARY": "default (0.0.0.0:2500)",
            "INPUT DECODER": "MPEG2:Software",
            "INPUT VIDEO": "MPEG2: 1920 x 1080 @ 29.97 fps : AVG : 0.00 mbps",
            "INPUT VIDEO PID": "    0 (0x    0)",
            "INPUT PMT PID": "    0 (0x    0)",
            "INPUT AUDIO": "NOT PRESENT",
            "SLATE": "DISABLED"
        },
        "OUTPUT": {
            "": "",
            "OUTPUT PRIMARY": "default (127.0.0.1:2900) @ 4.00 mbps",
            "OUTPUT PRIMARY": " (:0)",
            "OUTPUT VIDEO": "H.264: 0 x 0 @ 0.00 fps : AVG: CALCULATING mbps",
            "OUTPUT VIDEO PID": "    0 (0x    0)",
            "OUTPUT PMT PID": "    0 (0x    0)"
        },
        "EXTRA": {
            "": "",
            "ORIGIN PUBLISH": {
                "": "",
                "SERVER": "",
                "MEDIA NAME": "",
                "PUBLISHING POINT": "",
                "STATUS": "INACTIVE"
            }
        },
        "STATUS": {
            "": "",
            "FRAMES PROCESSED": "0",
            "RESYNC COUNT": "0",
            "LEVELS": "DI:-1 VPQ:-1 H264:-1 AMUX:-1 VMUX:-1 SCTE35:-1 CC:-1 UDP:-1",
            "PROC": "88262",
            "QUALITY": "5"
        }
    }


-----------------------------------------------------------------------
## Get Config
    
### Description

This is the command to get a config for a channel

### HTTP Request

    GET /v1/beo/channel/<chid>/config
    GET /v1/beo/channel/<chid>/config/<name>"

If the name is not specified and the channel is running, then you get the running config.  
If the name is not specified and the channel is not running, the default config is returned. 
The default config is '`config_<chid>`'.  
If the name is specified, then you get the config for that name.

body: 

    None

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

examples:

    { "error"  : "'name' does not exit", }

    { "error"  : "'name' is corrupted", }

    
-----------------------------------------------------------------------
## Create/Modify Config
    
### Description

This is the command to create a config for a channel

### HTTP Request

    PUT /v1/beo/channel/<chid>/config
    PUT /v1/beo/channel/<chid>/config/<name>

    PUT /v1/beo/channel/<chid>/config?action=reset

If no 'name' is specified, then the defaul name is where the config
object is stored. The default name is '`config_<chid>`'.

If no name is specified and the 'action=reset' is specified, then
the server will reset the channels default config back to factory value.

The reset action can only be applie to the default config.

body:

    {
        "config" : { reference "config" object }
    }


### response body

    { "status" : "ok", }
 
or:

    { "error"  : "some error string", }

examples:

    { "error"  : "no space on device", }

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

examples:

    { "error"  : "'name' does not exit", }

    
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

