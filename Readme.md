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





 