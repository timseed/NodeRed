# NodeRed Recipies 

I want to explore some data processing options instead of my current QT/C++ processing.


## Python Module

We need to add the Python module. This then allows us to process using Python.

Should you have a Python Virtual env (and **you should**) then this needs to be activated in the shell, before you run NodeRed.

i.e.

```bash
#Activate Python Virtual Env
source ~/.peham/bin/activate 
#Now run NodeRed ... it will use the Python Venv we just activated
NodeRed
```

## Rest Service 

To try and improve one of the flows... Instead of calling a Python routine which had to load some data and process it ... **Every time** it was called (Think of NodeRed as a Seperate Thread per data call). I decided to use a REST-API mechanism - so a little Flask... and we are sorted. 

This is what the Startup process looks like [./start.sh](./start.sh)

```bash
#!/bin/bash
# source ~/pe39/bin/activate
source ~/.peham/bin/activate
#
# Start Flask Service
# It is in a detached Process
nohup python  ~/Dev/Python/Ham/ham/dxcc/rest/dxcc_rest.py&
echo "Flask running on 5000"
export PYTHON=$(which python)
echo "Using Python at $PYTHON"
node-red
```

### NodeRed using Two REST Calls 

I want to call my REST API two times, and then add the data creating a *richer* payload.

 
 ```JS
 [
    {
        "id": "fd96fa3f.a14c18",
        "type": "tab",
        "label": "Flow 2",
        "disabled": false,
        "info": ""
    },
    {
        "id": "96754e4a.789e9",
        "type": "http request",
        "z": "fd96fa3f.a14c18",
        "name": "Call Python/Flask Service",
        "method": "GET",
        "ret": "obj",
        "paytoqs": false,
        "url": "",
        "tls": "",
        "proxy": "",
        "authType": "",
        "x": 630,
        "y": 320,
        "wires": [
            [
                "d91896ed.49cd2"
            ]
        ]
    },
    {
        "id": "78c6bb75.5e5dc4",
        "type": "function",
        "z": "fd96fa3f.a14c18",
        "name": "REST DX Call",
        "func": "msg.payload=\"http://localhost:5000/call/\"+msg.payload.Dx\n\nreturn msg;",
        "outputs": 1,
        "noerr": 0,
        "x": 220,
        "y": 320,
        "wires": [
            [
                "fc3ecd7c.f13ee"
            ]
        ]
    },
    {
        "id": "fc3ecd7c.f13ee",
        "type": "change",
        "z": "fd96fa3f.a14c18",
        "name": "",
        "rules": [
            {
                "t": "set",
                "p": "url",
                "pt": "msg",
                "to": "payload",
                "tot": "msg"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 430,
        "y": 320,
        "wires": [
            [
                "96754e4a.789e9"
            ]
        ]
    },
    {
        "id": "1428ff0d.b181c9",
        "type": "function",
        "z": "fd96fa3f.a14c18",
        "name": "REST Spotter Call",
        "func": "msg.payload=\"http://localhost:5000/call/\"+msg.payload.Spotter\nreturn msg;",
        "outputs": 1,
        "noerr": 0,
        "x": 230,
        "y": 380,
        "wires": [
            [
                "453e68ab.cb27d8"
            ]
        ]
    },
    {
        "id": "453e68ab.cb27d8",
        "type": "change",
        "z": "fd96fa3f.a14c18",
        "name": "",
        "rules": [
            {
                "t": "set",
                "p": "url",
                "pt": "msg",
                "to": "payload",
                "tot": "msg"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 430,
        "y": 380,
        "wires": [
            [
                "aba5d900.e106d"
            ]
        ]
    },
    {
        "id": "aba5d900.e106d",
        "type": "http request",
        "z": "fd96fa3f.a14c18",
        "name": "Call Python/Flask Service",
        "method": "GET",
        "ret": "obj",
        "paytoqs": false,
        "url": "",
        "tls": "",
        "proxy": "",
        "authType": "",
        "x": 630,
        "y": 380,
        "wires": [
            [
                "496e2aa3.a680b4"
            ]
        ]
    },
    {
        "id": "60ae2cf0.cd7674",
        "type": "inject",
        "z": "fd96fa3f.a14c18",
        "name": "",
        "repeat": "",
        "crontab": "",
        "once": false,
        "onceDelay": 0.1,
        "topic": "Dx",
        "payload": "M0FGC",
        "payloadType": "str",
        "x": 100,
        "y": 200,
        "wires": [
            [
                "6e61c83c.a770a"
            ]
        ]
    },
    {
        "id": "6e61c83c.a770a",
        "type": "join",
        "z": "fd96fa3f.a14c18",
        "name": "",
        "mode": "custom",
        "build": "object",
        "property": "payload",
        "propertyType": "msg",
        "key": "topic",
        "joiner": "\\n",
        "joinerType": "str",
        "accumulate": false,
        "timeout": "",
        "count": "2",
        "reduceRight": false,
        "reduceExp": "",
        "reduceInit": "",
        "reduceInitType": "",
        "reduceFixup": "",
        "x": 280,
        "y": 260,
        "wires": [
            [
                "78c6bb75.5e5dc4",
                "1428ff0d.b181c9"
            ]
        ]
    },
    {
        "id": "94535293.638e6",
        "type": "inject",
        "z": "fd96fa3f.a14c18",
        "name": "",
        "repeat": "",
        "crontab": "",
        "once": false,
        "onceDelay": 0.1,
        "topic": "Spotter",
        "payload": "DV3A",
        "payloadType": "str",
        "x": 90,
        "y": 260,
        "wires": [
            [
                "6e61c83c.a770a"
            ]
        ]
    },
    {
        "id": "cd73e102.86c568",
        "type": "join",
        "z": "fd96fa3f.a14c18",
        "name": "",
        "mode": "custom",
        "build": "object",
        "property": "_msgid",
        "propertyType": "msg",
        "key": "topic",
        "joiner": "\\n",
        "joinerType": "str",
        "accumulate": false,
        "timeout": "",
        "count": "2",
        "reduceRight": false,
        "reduceExp": "",
        "reduceInit": "",
        "reduceInitType": "",
        "reduceFixup": "",
        "x": 1120,
        "y": 340,
        "wires": [
            [
                "4b945922.a88fc8"
            ]
        ]
    },
    {
        "id": "d91896ed.49cd2",
        "type": "python-function",
        "z": "fd96fa3f.a14c18",
        "name": "Set DX",
        "func": "from pprint import pprint\nurl_data={}\npprint(msg['payload'])\nurl_data=msg['payload']\nmsg['dx']=url_data\nmsg['payload']={}\nreturn msg",
        "outputs": 1,
        "x": 870,
        "y": 320,
        "wires": [
            [
                "cd73e102.86c568"
            ]
        ]
    },
    {
        "id": "496e2aa3.a680b4",
        "type": "python-function",
        "z": "fd96fa3f.a14c18",
        "name": "Set Spotter",
        "func": "from pprint import pprint\nurl_data={}\npprint(msg['payload'])\nurl_data=msg['payload']\nmsg['spotter']=url_data\nmsg['payload']={}\nmsg['complete']=True\nreturn msg",
        "outputs": 1,
        "x": 890,
        "y": 380,
        "wires": [
            [
                "cd73e102.86c568"
            ]
        ]
    },
    {
        "id": "4b945922.a88fc8",
        "type": "debug",
        "z": "fd96fa3f.a14c18",
        "name": "",
        "active": true,
        "tosidebar": true,
        "console": false,
        "tostatus": false,
        "complete": "true",
        "targetType": "full",
        "x": 1100,
        "y": 500,
        "wires": []
    }
]
```
which looks like this 


![./img/two_rest.png](./img/two_rest.png)

And the data object looks like this ... with 2 data portions (DX and Spotter) now added.

```text
Spotter : msg : Object
object
_msgid: object
payload: object
topic: "Spotter"
url: "http://localhost:5000/call/DV3A"
statusCode: 200
headers: object
responseUrl: "http://localhost:5000/call/DV3A/"
redirectList: array[1]
retry: 0
dx: object
spotter: object
```


## Make a Common Data Object 

I have two input strings. Which slightly differ.... using Python create a dictionary which is the same for both. 

So going forward we do not need to worry about the data definition.

![./img/handle_data.png](./img/handle_data.png)


```javascript
[
    {
        "id": "3607f9949782d139",
        "type": "tab",
        "label": "Flow 1",
        "disabled": false,
        "info": "",
        "env": []
    },
    {
        "id": "0c8e0506c786faf4",
        "type": "inject",
        "z": "3607f9949782d139",
        "name": "ClusterSpot",
        "props": [
            {
                "p": "payload"
            },
            {
                "p": "topic",
                "vt": "str"
            }
        ],
        "repeat": "",
        "crontab": "",
        "once": false,
        "onceDelay": 0.1,
        "topic": "",
        "payload": "DX de TF3Y-#:    14005.0  VU2TS        CW  20dB Q:6 Z:14,15           0010Z",
        "payloadType": "str",
        "x": 150,
        "y": 160,
        "wires": [
            [
                "6941474bf300d039"
            ]
        ]
    },
    {
        "id": "06f2f8026366b303",
        "type": "debug",
        "z": "3607f9949782d139",
        "name": "debug 1",
        "active": true,
        "tosidebar": true,
        "console": false,
        "tostatus": false,
        "complete": "true",
        "targetType": "full",
        "statusVal": "",
        "statusType": "auto",
        "x": 720,
        "y": 200,
        "wires": []
    },
    {
        "id": "a9db2b6fd19fdbd1",
        "type": "inject",
        "z": "3607f9949782d139",
        "name": "Manual Spot",
        "props": [
            {
                "p": "payload"
            },
            {
                "p": "topic",
                "vt": "str"
            }
        ],
        "repeat": "",
        "crontab": "",
        "once": false,
        "onceDelay": 0.1,
        "topic": "",
        "payload": "DX de WU6X:      14051.5  AB9CA        POTA in SD                     0011Z",
        "payloadType": "str",
        "x": 150,
        "y": 260,
        "wires": [
            [
                "6941474bf300d039"
            ]
        ]
    },
    {
        "id": "6941474bf300d039",
        "type": "python-function",
        "z": "3607f9949782d139",
        "name": "Basic Parse",
        "func": "#DX de TF3Y-#:    14005.0  VU2TS        CW  20dB Q:6 Z:14,15           0010Z\nfrom pprint import pprint\nincoming_data={}\ndata=msg['payload']\n\n\nincoming_data={}\n\nif (data.startswith(\"DX de\") and data.find(\"Z:\")!=-1):\n    print(\"My Cluster Aggregated\")\n    parts=data.split('#')\n    spotter = parts[0].split()[2]\n    spotter=spotter.replace('-','').replace(':','')\n    parts=parts[1].split()\n    pprint(spotter)\n    pprint(parts)\n    freq=parts[1]\n    dx=parts[2]\n    mode=parts[3]\n    print(f\"Spotter {spotter} Dx {dx} Freq {freq} mode {mode}\")\n    incoming_data['source']=data\n    incoming_data['spotter']=spotter\n    incoming_data['dx']=dx\n    incoming_data['mode']=mode\n    incoming_data['freq']=freq\n    msg['incoming_data']=incoming_data\n    pprint(incoming_data)\nelif (data.startswith(\"DX de\") and data.find(\"Z:\")==-1):\n    print(\"Non Agg\")\n    parts=data.split()\n    #spotter = parts[0].split()[2]\n    #spotter=spotter.replace('-','').replace(':','')\n    spotter=parts[2].replace('-','').replace(':','')\n    dx=parts[4]\n    freq=parts[3]\n    mode=\"\"\n    incoming_data['source']=data\n    incoming_data['spotter']=spotter\n    incoming_data['dx']=dx\n    incoming_data['mode']=mode\n    incoming_data['freq']=freq\n    pprint(incoming_data)\n    msg['incoming_data']=incoming_data\n\nreturn msg",
        "outputs": 1,
        "x": 390,
        "y": 200,
        "wires": [
            [
                "06f2f8026366b303"
            ]
        ]
    }
]
```



 