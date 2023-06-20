# OpenDaylight Sample Java11 HTTP Chunked GET Example

```java
package com.luminanetworks.java11;

import java.io.*;
import java.util.*;
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpHeaders;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;
import java.time.Duration;

public class Rest {

    private static final HttpClient httpClient = HttpClient.newBuilder()
        .version(HttpClient.Version.HTTP_1_1)
        .connectTimeout(Duration.ofSeconds(10))
        .build();

    public static void main(String[] args) throws IOException, InterruptedException {

        HttpRequest request = HttpRequest.newBuilder()
            .GET()
            .uri(URI.create("http://127.0.0.1:8181/rests/data/network-topology:network-topology/topology=topology-netconf/node=ericsson_trafficnode_13322/yang-ext:mount/core-model-1-4:control-construct?content=nonconfig&fields=logical-termination-point(uuid;layer-protocol(local-id;layer-protocol-name))"))
            .setHeader("User-Agent", "lumina-test/0.1")
            .setHeader("cache-control", "no-cache")
            .setHeader("Authorization", "Basic bW9uaXRvcjpNb0dlbjAx")
            .setHeader("Accept", "application/json")
            .timeout(Duration.ofMinutes(5))
            .build();
        System.out.println("Request: HTTP/1.1 GET /rests/data/network-topology:network-topology/topology=topology-netconf/node=ericsson_trafficnode_13322/yang-ext:mount/core-model-1-4:control-construct?content=nonconfig&fields=logical-termination-point(uuid;layer-protocol(local-id;layer-protocol-name))");
        HttpResponse < InputStream > response = httpClient.send(request, HttpResponse.BodyHandlers.ofInputStream());

        // print response headers
        HttpHeaders headers = response.headers();
        headers.map().forEach((k, v) - > System.out.println("Response Headers: " + k + ":" + v));

        // print status code
        System.out.println("Response HTTP Status Code: " + response.statusCode());

        // print response body
        InputStream in = response.body();
        BufferedReader br = new BufferedReader(new InputStreamReader( in ));
        StringBuilder sb = new StringBuilder();
        int c;
        try {
            while ((c = br.read()) != -1) {
                sb.append((char) c);
            }
        } catch (IOException e) {
            //System.out.println(e.toString());
        } finally {
            System.out.println();
            System.out.println(sb.toString());
            br.close(); in .close();
        }
    }
}
```

```bash
lmsdn@lmsdnc:~$ java Rest.java
Request: HTTP/1.1 GET /rests/data/network-topology:network-topology/topology=topology-netconf/node=ericsson_trafficnode_13322/yang-ext:mount/core-model-1-4:control-construct?content=nonconfig&fields=logical-termination-point(uuid;layer-protocol(local-id;layer-protocol-name))
Response Headers: date:[Wed, 29 Jul 2020 08:44:58 GMT]
Response Headers: expires:[Thu, 01 Jan 1970 00:00:00 GMT]
Response Headers: server:[Jetty(9.4.18.v20190429)]
Response Headers: set-cookie:[JSESSIONID=node056vbcj588iles18xpee9uxph189.node0;Path=/, rememberMe=deleteMe; Path=/; Max-Age=0; Expires=Tue, 28-Jul-2020 08:44:58 GMT]
Response Headers: transfer-encoding:[chunked]
Response HTTP Status Code: 200
```
```json

{"core-model-1-4:control-construct":{"logical-termination-point":[{"uuid":"RF-2146697601","layer-protocol":[{"local-id":"2146697601","layer-protocol-name":"air-interface-2-0:LAYER_PROTOCOL_NAME_TYPE_AIR_LAYER"}]},{"uuid":"ETH-2134638850","layer-protocol":[{"local-id":"2134638850","layer-protocol-name":"ethernet-container-2-0:LAYER_PROTOCOL_NAME_TYPE_ETHERNET_CONTAINER_LAYER"}]},{"uuid":"ETY-2134638850","layer-protocol":[{"local-id":"2134638850","layer-protocol-name":"wire-interface-2-0:LAYER_PROTOCOL_NAME_TYPE_WIRE_LAYER"}]},{"uuid":"ETH-2134638978","layer-protocol":[{"local-id":"2134638978","layer-protocol-name":"ethernet-container-2-0:LAYER_PROTOCOL_NAME_TYPE_ETHERNET_CONTAINER_LAYER"}]},{"uuid":"ETY-2134638978","layer-protocol":[{"local-id":"2134638978","layer-protocol-name":"wire-interface-2-0:LAYER_PROTOCOL_NAME_TYPE_WIRE_LAYER"}]},{"uuid":"MAC-2134638596","layer-protocol":[{"local-id":"2134638596","layer-protocol-name":"mac-interface-1-0:LAYER_PROTOCOL_NAME_TYPE_MAC_LAYER"}]},{"uuid":"MAC-2134638594","layer-protocol":[{"local-id":"2134638594","layer-protocol-name":"mac-interface-1-0:LAYER_PROTOCOL_NAME_TYPE_MAC_LAYER"}]},{"uuid":"MAC-2134638595","layer-protocol":[{"local-id":"2134638595","layer-protocol-name":"mac-interface-1-0:LAYER_PROTOCOL_NAME_TYPE_MAC_LAYER"}]},{"uuid":"HMS-2146697473","layer-protocol":[{"local-id":"2146697473","layer-protocol-name":"hybrid-mw-structure-2-0:LAYER_PROTOCOL_NAME_TYPE_HYBRID_MW_STRUCTURE_LAYER"}]},{"uuid":"ETY-2147434624","layer-protocol":[{"local-id":"2147434624","layer-protocol-name":"wire-interface-2-0:LAYER_PROTOCOL_NAME_TYPE_WIRE_LAYER"}]},{"uuid":"PES-2134638595","layer-protocol":[{"local-id":"2134638595","layer-protocol-name":"pure-ethernet-structure-2-0:LAYER_PROTOCOL_NAME_TYPE_PURE_ETHERNET_STRUCTURE_LAYER"}]},{"uuid":"PES-2134638596","layer-protocol":[{"local-id":"2134638596","layer-protocol-name":"pure-ethernet-structure-2-0:LAYER_PROTOCOL_NAME_TYPE_PURE_ETHERNET_STRUCTURE_LAYER"}]},{"uuid":"PES-2134638594","layer-protocol":[{"local-id":"2134638594","layer-protocol-name":"pure-ethernet-structure-2-0:LAYER_PROTOCOL_NAME_TYPE_PURE_ETHERNET_STRUCTURE_LAYER"}]},{"uuid":"PES-2134638850","layer-protocol":[{"local-id":"2134638850","layer-protocol-name":"pure-ethernet-structure-2-0:LAYER_PROTOCOL_NAME_TYPE_PURE_ETHERNET_STRUCTURE_LAYER"}]},{"uuid":"PES-2134638978","layer-protocol":[{"local-id":"2134638978","layer-protocol-name":"pure-ethernet-structure-2-0:LAYER_PROTOCOL_NAME_TYPE_PURE_ETHERNET_STRUCTURE_LAYER"}]},{"uuid":"ETY-2134638723","layer-protocol":[{"local-id":"2134638723","layer-protocol-name":"wire-interface-2-0:LAYER_PROTOCOL_NAME_TYPE_WIRE_LAYER"}]},{"uuid":"ETH-2134638723","layer-protocol":[{"local-id":"2134638723","layer-protocol-name":"ethernet-container-2-0:LAYER_PROTOCOL_NAME_TYPE_ETHERNET_CONTAINER_LAYER"}]},{"uuid":"HMS-2146697601","layer-protocol":[{"local-id":"2146697601","layer-protocol-name":"hybrid-mw-structure-2-0:LAYER_PROTOCOL_NAME_TYPE_HYBRID_MW_STRUCTURE_LAYER"}]},{"uuid":"MAC-2147434624","layer-protocol":[{"local-id":"2147434624","layer-protocol-name":"mac-interface-1-0:LAYER_PROTOCOL_NAME_TYPE_MAC_LAYER"}]},{"uuid":"RF-2146697473","layer-protocol":[{"local-id":"2146697473","layer-protocol-name":"air-interface-2-0:LAYER_PROTOCOL_NAME_TYPE_AIR_LAYER"}]},{"uuid":"MAC-2134647012","layer-protocol":[{"local-id":"2134647012","layer-protocol-name":"mac-interface-1-0:LAYER_PROTOCOL_NAME_TYPE_MAC_LAYER"}]},{"uuid":"ETY-2134638594","layer-protocol":[{"local-id":"2134638594","layer-protocol-name":"wire-interface-2-0:LAYER_PROTOCOL_NAME_TYPE_WIRE_LAYER"}]},{"uuid":"ETH-2134638596","layer-protocol":[{"local-id":"2134638596","layer-protocol-name":"ethernet-container-2-0:LAYER_PROTOCOL_NAME_TYPE_ETHERNET_CONTAINER_LAYER"}]},{"uuid":"ETY-2134638596","layer-protocol":[{"local-id":"2134638596","layer-protocol-name":"wire-interface-2-0:LAYER_PROTOCOL_NAME_TYPE_WIRE_LAYER"}]},{"uuid":"ETY-2134638595","layer-protocol":[{"local-id":"2134638595","layer-protocol-name":"wire-interface-2-0:LAYER_PROTOCOL_NAME_TYPE_WIRE_LAYER"}]},{"uuid":"ETH-2134638595","layer-protocol":[{"local-id":"2134638595","layer-protocol-name":"ethernet-container-2-0:LAYER_PROTOCOL_NAME_TYPE_ETHERNET_CONTAINER_LAYER"}]},{"uuid":"PES-2134638723","layer-protocol":[{"local-id":"2134638723","layer-protocol-name":"pure-ethernet-structure-2-0:LAYER_PROTOCOL_NAME_TYPE_PURE_ETHERNET_STRUCTURE_LAYER"}]},{"uuid":"PES-2147434624","layer-protocol":[{"local-id":"2147434624","layer-protocol-name":"pure-ethernet-structure-2-0:LAYER_PROTOCOL_NAME_TYPE_PURE_ETHERNET_STRUCTURE_LAYER"}]},{"uuid":"ETH-2134638594","layer-protocol":[{"local-id":"2134638594","layer-protocol-name":"ethernet-container-2-0:LAYER_PROTOCOL_NAME_TYPE_ETHERNET_CONTAINER_LAYER"}]},{"uuid":"TDM-2147352844","layer-protocol":[{"local-id":"2147352844","layer-protocol-name":"tdm-container-2-0:LAYER_PROTOCOL_NAME_TYPE_TDM_CONTAINER_LAYER"}]},{"uuid":"TDM-2147352843","layer-protocol":[{"local-id":"2147352843","layer-protocol-name":"tdm-container-2-0:LAYER_PROTOCOL_NAME_TYPE_TDM_CONTAINER_LAYER"}]},{"uuid":"TDM-2147352842","layer-protocol":[{"local-id":"2147352842","layer-protocol-name":"tdm-container-2-0:LAYER_PROTOCOL_NAME_TYPE_TDM_CONTAINER_LAYER"}]},{"uuid":"TDM-2147352963","layer-protocol":[{"local-id":"2147352963","layer-protocol-name":"tdm-container-2-0:LAYER_PROTOCOL_NAME_TYPE_TDM_CONTAINER_LAYER"}]},{"uuid":"TDM-2147352841","layer-protocol":[{"local-id":"2147352841","layer-protocol-name":"tdm-container-2-0:LAYER_PROTOCOL_NAME_TYPE_TDM_CONTAINER_LAYER"}]},{"uuid":"TDM-2147352962","layer-protocol":[{"local-id":"2147352962","layer-protocol-name":"tdm-container-2-0:LAYER_PROTOCOL_NAME_TYPE_TDM_CONTAINER_LAYER"}]},{"uuid":"MAC-2134638850","layer-protocol":[{"local-id":"2134638850","layer-protocol-name":"mac-interface-1-0:LAYER_PROTOCOL_NAME_TYPE_MAC_LAYER"}]},{"uuid":"MAC-2134638978","layer-protocol":[{"local-id":"2134638978","layer-protocol-name":"mac-interface-1-0:LAYER_PROTOCOL_NAME_TYPE_MAC_LAYER"}]},{"uuid":"TDM-2147352840","layer-protocol":[{"local-id":"2147352840","layer-protocol-name":"tdm-container-2-0:LAYER_PROTOCOL_NAME_TYPE_TDM_CONTAINER_LAYER"}]},{"uuid":"TDM-2147352961","layer-protocol":[{"local-id":"2147352961","layer-protocol-name":"tdm-container-2-0:LAYER_PROTOCOL_NAME_TYPE_TDM_CONTAINER_LAYER"}]},{"uuid":"TDM-2147352960","layer-protocol":[{"local-id":"2147352960","layer-protocol-name":"tdm-container-2-0:LAYER_PROTOCOL_NAME_TYPE_TDM_CONTAINER_LAYER"}]},{"uuid":"TDM-2147352839","layer-protocol":[{"local-id":"2147352839","layer-protocol-name":"tdm-container-2-0:LAYER_PROTOCOL_NAME_TYPE_TDM_CONTAINER_LAYER"}]},{"uuid":"TDM-2147352838","layer-protocol":[{"local-id":"2147352838","layer-protocol-name":"tdm-container-2-0:LAYER_PROTOCOL_NAME_TYPE_TDM_CONTAINER_LAYER"}]},{"uuid":"TDM-2147352833","layer-protocol":[{"local-id":"2147352833","layer-protocol-name":"tdm-container-2-0:LAYER_PROTOCOL_NAME_TYPE_TDM_CONTAINER_LAYER"}]},{"uuid":"TDM-2147352832","layer-protocol":[{"local-id":"2147352832","layer-protocol-name":"tdm-container-2-0:LAYER_PROTOCOL_NAME_TYPE_TDM_CONTAINER_LAYER"}]},{"uuid":"TDM-2147352837","layer-protocol":[{"local-id":"2147352837","layer-protocol-name":"tdm-container-2-0:LAYER_PROTOCOL_NAME_TYPE_TDM_CONTAINER_LAYER"}]},{"uuid":"TDM-2147352836","layer-protocol":[{"local-id":"2147352836","layer-protocol-name":"tdm-container-2-0:LAYER_PROTOCOL_NAME_TYPE_TDM_CONTAINER_LAYER"}]},{"uuid":"TDM-2147352835","layer-protocol":[{"local-id":"2147352835","layer-protocol-name":"tdm-container-2-0:LAYER_PROTOCOL_NAME_TYPE_TDM_CONTAINER_LAYER"}]},{"uuid":"TDM-2147352834","layer-protocol":[{"local-id":"2147352834","layer-protocol-name":"tdm-container-2-0:LAYER_PROTOCOL_NAME_TYPE_TDM_CONTAINER_LAYER"}]},{"uuid":"MAC-2134638723","layer-protocol":[{"local-id":"2134638723","layer-protocol-name":"mac-interface-1-0:LAYER_PROTOCOL_NAME_TYPE_MAC_LAYER"}]},{"uuid":"ETH-2134647012","layer-protocol":[{"local-id":"2134647012","layer-protocol-name":"ethernet-container-2-0:LAYER_PROTOCOL_NAME_TYPE_ETHERNET_CONTAINER_LAYER"}]},{"uuid":"ETH-2147434624","layer-protocol":[{"local-id":"2147434624","layer-protocol-name":"ethernet-container-2-0:LAYER_PROTOCOL_NAME_TYPE_ETHERNET_CONTAINER_LAYER"}]}]}}

```
