# OpenDaylighty HTTPS Cheat Sheet

Downloaded Fluorine-SR2 tarball into lumina user's homedir, and copied over a premade setenv file before starting.
Allows for quick wipe & restart.

```bash
tar -zxvf opendaylight-0.9.2.tar.gz
cd opendaylight-0.9.2/
cp ~/setenv bin/setenv
bin/start
bin/client "feature:install odl-netconf-connector-all"
bin/client "feature:install odl-restconf"
bin/stop
```



#First, be sure the short hostname is correct in /etc/hostname, and the FQDN is set in the /etc/hosts file (first entry for actual intf IP addr). ...or that local DNS resolves the local FQDN to the intf IP.

```
keytool -genkey -v -noprompt -keyalg RSA -alias jetty -dname "CN=$HOSTNAME, OU=odl, O=odl, L=NA, S=NA, C=NA" -keystore ./etc/.keystore -validity 999 -storepass 123456 -keypass 123456
vi etc/custom.properties
org.osgi.service.http.secure.enabled=true
org.osgi.service.http.port.secure=8443
org.ops4j.pax.web.ssl.keystore=./etc/.keystore
org.ops4j.pax.web.ssl.password=123456
org.ops4j.pax.web.ssl.keypassword=123456
vi etc/jetty.xml
```

```xml
<!-- Use this connector for many frequently idle connections and for
    threadless continuations. -->
<New id="http-default" class="org.eclipse.jetty.server.HttpConfiguration">
    <Set name="secureScheme">https</Set>
    <Set name="securePort">
        <Property name="jetty.secure.port" default="8443" />
    </Set>
    <Set name="outputBufferSize">32768</Set>
    <Set name="requestHeaderSize">8192</Set>
    <Set name="responseHeaderSize">8192</Set>

    <!-- Default security setting: do not leak our version -->
    <Set name="sendServerVersion">false</Set>

    <Set name="sendDateHeader">false</Set>
    <Set name="headerCacheSize">512</Set>
</New>

<Call name="addConnector">
    <Arg>
        <New class="org.eclipse.jetty.server.ServerConnector">
            <Arg name="server">
                <Ref refid="Server" />
            </Arg>
            <Arg name="factories">
                <Array type="org.eclipse.jetty.server.ConnectionFactory">
                    <Item>
                        <New class="org.eclipse.jetty.server.HttpConnectionFactory">
                            <Arg name="config">
                                <Ref refid="http-default"/>
                            </Arg>
                        </New>
                    </Item>
                </Array>
            </Arg>
            <Set name="host">
                <Property name="jetty.host"/>
            </Set>
            <Set name="port">
                <Property name="jetty.port" default="8181"/>
            </Set>
            <Set name="idleTimeout">
                <Property name="http.timeout" default="300000"/>
            </Set>
            <Set name="name">jetty-default</Set>
        </New>
    </Arg>
</Call>

<!-- =========================================================== -->
<!-- Configure Authentication Realms -->
<!-- Realms may be configured for the entire server here, or -->
<!-- they can be configured for a specific web app in a context -->
<!-- configuration (see $(jetty.home)/contexts/test.xml for an -->
<!-- example). -->
<!-- =========================================================== -->
<Call name="addBean">
    <Arg>
        <New class="org.eclipse.jetty.jaas.JAASLoginService">
            <Set name="name">karaf</Set>
            <Set name="loginModuleName">karaf</Set>
            <Set name="roleClassNames">
                <Array type="java.lang.String">
                    <Item>org.apache.karaf.jaas.boot.principal.RolePrincipal</Item>
                </Array>
            </Set>
        </New>
    </Arg>
</Call>
<Call name="addBean">
    <Arg>
       <New class="org.eclipse.jetty.jaas.JAASLoginService">
            <Set name="name">default</Set>
            <Set name="loginModuleName">karaf</Set>
            <Set name="roleClassNames">
                <Array type="java.lang.String">
                    <Item>org.apache.karaf.jaas.boot.principal.RolePrincipal</Item>
                </Array>
            </Set>
        </New>
    </Arg>
</Call>
```




bin/start
tail -F data/log/karaf.log




#Verified working up through Ubuntu 16.04.05-latest as of June 2020, Fluorine-SR2.

curl -v --insecure --user admin:admin -H "Content-Type: application/json" -H "Accept: application/json" -X GET "https://localhost:8443/restconf/operational/network-topology:network-topology/topology/topology-netconf" | python3 -mjson.tool




% Total % Received % Xferd Average Speed Time Time Time Current
Dload Upload Total Spent Left Speed
0 0 0 0 0 0 0 0 --:--:-- --:--:-- --:--:-- 0* Trying 127.0.0.1...




Connected to localhost (127.0.0.1) port 8443 (#0)
found 127 certificates in /etc/ssl/certs/ca-certificates.crt
found 508 certificates in /etc/ssl/certs
ALPN, offering http/1.1
SSL connection using TLS1.2 / ECDHE_RSA_AES_256_GCM_SHA384
   server certificate verification SKIPPED

   server certificate status verification SKIPPED

   common name: LSC-demo (does not match 'localhost')

   server certificate expiration date OK

   server certificate activation date OK

   certificate public key: RSA

   certificate version: #3

   subject: C=NA,ST=NA,L=NA,O=odl,OU=odl,CN=LSC-demo

   start date: Thu, 04 Jun 2020 16:55:28 GMT

   expire date: Tue, 28 Feb 2023 16:55:28 GMT

   issuer: C=NA,ST=NA,L=NA,O=odl,OU=odl,CN=LSC-demo

   compression: NULL

ALPN, server did not agree to a protocol
Server auth using Basic with user 'admin'




GET /restconf/operational/network-topology:network-topology/topology/topology-netconf HTTP/1.1
Host: localhost:8443
Authorization: Basic YWRtaW46YWRtaW4=
User-Agent: curl/7.47.0
Content-Type: application/json
Accept: application/json







< HTTP/1.1 200 OK
< Set-Cookie: JSESSIONID=jiscx6rp25kl1birhk6tpasp2;Path=/;Secure
< Expires: Thu, 01 Jan 1970 00:00:00 GMT
< Set-Cookie: rememberMe=deleteMe; Path=/; Max-Age=0; Expires=Wed, 03-Jun-2020 18:31:46 GMT
< Content-Type: application/json
< Content-Length: 49
<
{ [49 bytes data]
100 49 100 49 0 0 566 0 --:--:-- --:--:-- --:--:-- 569




Connection #0 to host localhost left intact
```json
{
"topology": [
{
"topology-id": "topology-netconf"
}
]
}
```


