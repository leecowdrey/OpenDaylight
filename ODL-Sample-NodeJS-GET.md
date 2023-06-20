# Sample simple NodeJS HTTP GET Example

```
const http = require("http");

const options = {
    "method": "GET",
    "hostname": "127.0.0.1",
    "port": 8181,
    "path": "/rests/data/network-topology:network-topology/topology=topology-netconf/node=ericsson_trafficnode_13322/yang-ext:mount/core-model-1-4:control-construct?content=nonconfig&fields=logical-termination-point(uuid;layer-protocol(local-id;layer-protocol-name))",
    "headers": {
        "cache-control": "no-cache",
"Accept": "application/json",
"User-Agent": "lumina-test/0.1",
"Authorization": "Basic bW9uaXRvcjpNb0dlbjAx"
    }
}

const req = http.request(options, function(res) {

    var chunks = [];

    console.log(options.method+" "+options.hostname+":"+options.port+options.path);

    res.on("data", function (chunk) {
        chunks.push(chunk);
    });

    res.on("end", function() {
        var body = Buffer.concat(chunks);
        console.log(body.toString());
    });

});

req.end()
```

```bash
lumina@lsc12:~$ nodejs rest.js
GET 127.0.0.1:8181/rests/data/network-topology:network-topology/topology=topology-netconf?content=nonconfig&fields=node(node-id;netconf-node-topology:connection-status)
```
```json
{"network-topology:topology":[{"node":[{"node-id":"danos1909","netconf-node-topology:connection-status":"connected"}]}]}
```
