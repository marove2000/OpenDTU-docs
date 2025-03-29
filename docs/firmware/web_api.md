---
hide:
  - toc
---

# Web API

!!!danger "Important Note"
    The Web API is mainly generated to fetch and post information between the WebUI and the ESP. You use it at your own  risk. It may change in future and break compatibility!

<swagger-ui src="../assets/swagger/main.yaml"/>

## Examples of Use

### Important notes

- IP addresses and serial numbers in these examples are anonymized. Adjust to your own needs.
- The output from curl is without a linefeed at the end, so please be careful when copying the output - do not accidentally add the shell prompt directly after it.
- When POSTing config data to OpenDTU, always send all settings back, even if only one setting was changed. Sending single settings is not supported and you will receive a response `{"type":"warning","message":"Values are missing!"}`
- When POSTing, always put single quotes around the data part. Do not confuse the single quote `'` with the backtick `` ` ``. You have been warned.
- Some API calls have a single URL for GET and POST - e.g. `/api/ntp/config`
- Other API calls use e.g. `/api/limit/status` to GET data and a different URL `/api/limit/config` to POST data.
- If you want to investigate the web api communication, a good tool is [Postman](https://www.postman.com/)
- Settings API require username and password provided with Basic Authentication credentials
- If you disable the readonly access to the web API, every endpoint requires authentication

### Get information

You can "talk" to the OpenDTU with a command line tool like `curl`. The output is in plain JSON, without carriage return/linefeed and is therefore not very human readable.

#### Get common live data

```bash
$ curl http://192.168.10.10/api/livedata/status
{"inverters":[{"serial":"1161716xxxxx","name":"Meine Solaranlage","order":0,"data_age":30636,"poll_enabled":true,"reachable":false,"producing":false,"limit_relative":100,"limit_absolute":1500},{"serial":"1141716xxxxx","name":"Inverter 2","order":0,"data_age":108349,"poll_enabled":false,"reachable":false,"producing":false,"limit_relative":0,"limit_absolute":-1},{"serial":"1141716xxxxx","name":"Inverter 3","order":0,"data_age":108349,"poll_enabled":false,"reachable":false,"producing":false,"limit_relative":0,"limit_absolute":-1}],"total":{"Power":{"v":0,"u":"W","d":1},"YieldDay":{"v":0,"u":"Wh","d":0},"YieldTotal":{"v":2515.605957,"u":"kWh","d":3}},"hints":{"time_sync":false,"radio_problem":false,"default_password":false}}
```

To enhance readability (and filter information) use the JSON command line processor `jq`.

```bash
$ curl --no-progress-meter http://192.168.10.10/api/livedata/status | jq
{
  "inverters": [
    {
      "serial": "1161716xxxxx",
      "name": "Meine Solaranlage",
      "order": 0,
      "data_age": 30687,
      "poll_enabled": true,
      "reachable": false,
      "producing": false,
      "limit_relative": 100,
      "limit_absolute": 1500
    },
    {
      "serial": "1141716xxxxx",
      "name": "Inverter 2",
      "order": 0,
      "data_age": 108400,
      "poll_enabled": false,
      "reachable": false,
      "producing": false,
      "limit_relative": 0,
      "limit_absolute": -1
    },
    {
      "serial": "1141716xxxxx",
      "name": "Inverter 3",
      "order": 0,
      "data_age": 108400,
      "poll_enabled": false,
      "reachable": false,
      "producing": false,
      "limit_relative": 0,
      "limit_absolute": -1
    }
  ],
  "total": {
    "Power": {
      "v": 0,
      "u": "W",
      "d": 1
    },
    "YieldDay": {
      "v": 0,
      "u": "Wh",
      "d": 0
    },
    "YieldTotal": {
      "v": 2515.605957,
      "u": "kWh",
      "d": 3
    }
  },
  "hints": {
    "time_sync": false,
    "radio_problem": false,
    "default_password": false
  }
}
```

#### Get inverter detail livedata

```bash
$ curl http://192.168.10.10/api/livedata/status?inv=1161716xxxxx
{"inverters":[{"serial":"1161716xxxxx","name":"Meine Solaranlage","order":0,"data_age":30824,"poll_enabled":true,"reachable":false,"producing":false,"limit_relative":100,"limit_absolute":1500,"AC":{"0":{"Power":{"v":0,"u":"W","d":1},"Voltage":{"v":0,"u":"V","d":1},"Current":{"v":0,"u":"A","d":2},"Frequency":{"v":0,"u":"Hz","d":2},"PowerFactor":{"v":0,"u":"","d":3},"ReactivePower":{"v":0,"u":"var","d":1}}},"DC":{"0":{"name":{"u":""},"Power":{"v":0,"u":"W","d":1},"Voltage":{"v":0,"u":"V","d":1},"Current":{"v":0,"u":"A","d":2},"YieldDay":{"v":0,"u":"Wh","d":0},"YieldTotal":{"v":834.2609863,"u":"kWh","d":3},"Irradiation":{"v":0,"u":"%","d":3,"max":385}},"1":{"name":{"u":""},"Power":{"v":0,"u":"W","d":1},"Voltage":{"v":0,"u":"V","d":1},"Current":{"v":0,"u":"A","d":2},"YieldDay":{"v":0,"u":"Wh","d":0},"YieldTotal":{"v":832.7069702,"u":"kWh","d":3},"Irradiation":{"v":0,"u":"%","d":3,"max":385}},"2":{"name":{"u":""},"Power":{"v":0,"u":"W","d":1},"Voltage":{"v":0,"u":"V","d":1},"Current":{"v":0,"u":"A","d":2},"YieldDay":{"v":0,"u":"Wh","d":0},"YieldTotal":{"v":843.7299805,"u":"kWh","d":3},"Irradiation":{"v":0,"u":"%","d":3,"max":385}},"3":{"name":{"u":""},"Power":{"v":0,"u":"W","d":1},"Voltage":{"v":0,"u":"V","d":1},"Current":{"v":0,"u":"A","d":2},"YieldDay":{"v":0,"u":"Wh","d":0},"YieldTotal":{"v":4.907999992,"u":"kWh","d":3}}},"INV":{"0":{"Power DC":{"v":0,"u":"W","d":1},"YieldDay":{"v":0,"u":"Wh","d":0},"YieldTotal":{"v":2515.605957,"u":"kWh","d":3},"Temperature":{"v":0,"u":"°C","d":1},"Efficiency":{"v":0,"u":"%","d":3}}},"events":2}],"total":{"Power":{"v":0,"u":"W","d":1},"YieldDay":{"v":0,"u":"Wh","d":0},"YieldTotal":{"v":2515.605957,"u":"kWh","d":3}},"hints":{"time_sync":false,"radio_problem":false,"default_password":false}}
```

The eventlog can be fetched with the inverter serial number as parameter:

```bash
$ curl --no-progress-meter http://192.168.10.10/api/eventlog/status?inv=11418186xxxx | jq
{
  "11418186xxxx": {
    "count": 4,
    "events": [
      {
        "message_id": 1,
        "message": "Inverter start",
        "start_time": 28028,
        "end_time": 28028
      },
      {
        "message_id": 209,
        "message": "PV-1: No input",
        "start_time": 28036,
        "end_time": 0
      },
      {
        "message_id": 2,
        "message": "DTU command failed",
        "start_time": 28092,
        "end_time": 28092
      },
      {
        "message_id": 207,
        "message": "MPPT-A: Input undervoltage",
        "start_time": 28336,
        "end_time": 0
      }
    ]
  }
}
```

#### combine curl and jq

`jq` can filter specific fields from json output.

For example, filter out the current total power:

```bash
$ curl --no-progress-meter http://192.168.10.10/api/livedata/status | jq '.total | .Power.v'
140.7999878
```

#### Get information where login is required

When config data is requested, username and password have to be provided to `curl`
Username is always `admin`, the default password is `openDTU42`. The password is used for both the admin login and the Admin-mode Access Point.

```bash
$ curl --u admin:openDTU42 http://192.168.10.10/api/ntp/config
{"ntp_server":"pool.ntp.org","ntp_timezone":"CET-1CEST,M3.5.0,M10.5.0/3","ntp_timezone_descr":"Europe/Berlin"}
```

### Post information

With HTTP POST commands information can be written to the OpenDTU.

The Web API is designed to allow the web frontend in the web browser to communicate with the OpenDTU software running on the ESP32. It is not designed to be intuitive or user-friendly, so please follow the instructions here.

#### Example 1: change ntp settings

If you want to configure the ntp server setting, first fetch the information from the web API:

```bash
$ curl -u "admin:password" http://192.168.10.10/api/ntp/config
{"ntp_server":"pool.ntp.org","ntp_timezone":"CET-1CEST,M3.5.0,M10.5.0/3","ntp_timezone_descr":"Europe/Berlin"}
```

Then, second step, send your new settings. Use the text output from curl in the first step, add `data=` and enclose the whole data with single quotes.

```bash
$ curl -u "admin:password" http://192.168.10.10/api/ntp/config -d 'data={"ntp_server":"my.own.ntp.server.home","ntp_timezone":"CET-1CEST,M3.5.0,M10.5.0/3","ntp_timezone_descr":"Europe/Berlin"}'
{"type":"success","message":"Settings saved!"}
```

You will receive a json formatted response.

#### Example 2: change power limit

In the second example, I want to change the non persistent power limit of an inverter. Again, first fetch current data:

```bash
$ curl http://192.168.10.10/api/limit/status
{"11418186xxxx":{"limit_relative":100,"max_power":600,"limit_set_status":"Ok"},"11418180xxxx":{"limit_relative":100,"max_power":800,"limit_set_status":"Ok"}}
```

I see data from two configured inverters.

Now I set the relative power limit of inverter with serialnumber `11418180xxxx` to 50%.

```bash
$ curl -u "admin:password" http://192.168.10.10/api/limit/config -d 'data={"serial":"11418180xxxx", "limit_type":1, "limit_value":50}'
{"type":"success","message":"Settings saved!"}
```

Then I read again the limit status. In the first answer the status is `pending`, some seconds later it changed to `OK`.

```bash
$ curl http://192.168.10.10/api/limit/status
{"11418186xxxx":{"limit_relative":100,"max_power":600,"limit_set_status":"Ok"},"11418180xxxx":{"limit_relative":100,"max_power":800,"limit_set_status":"Pending"}}

...

$ curl http://192.168.10.10/api/limit/status
{"11418186xxxx":{"limit_relative":100,"max_power":600,"limit_set_status":"Ok"},"11418180xxxx":{"limit_relative":50,"max_power":800,"limit_set_status":"Ok"}}
```
