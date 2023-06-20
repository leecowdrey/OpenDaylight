# KVM DaNOS (1908)

## DaNOS

![DaNOS 1908](https://danosproject.atlassian.net/wiki/spaces/DAN/pages/1310721/Features+supported+in+DANOS-1908)
![DaNOS 2105 ISO](https://github.com/danos/build-iso/releases/download/danos%2F2105/danos-2105-base-amd64.iso)
![DaNOS 2105 Project/Release Notes](https://danosproject.atlassian.net/wiki/spaces/DAN/pages/1347649542/DANOS+2105)

```
qemu-img create -f qcow2 -o preallocation=metadata /var/lib/libvirt/images/danos1908.qcow2 4G

curl --remote-name http://repos.danosproject.org.s3-website-us-west-1.amazonaws.com/images/danos-1908-amd64-vrouter.iso

virt-install --name danos1908 \
--cpu host \
--os-type linux \
--vcpus=2 \
--ram 2048 \
--disk path=/var/lib/libvirt/images/danos1908.qcow2,bus=virtio,size=4 \
--cdrom /var/lib/libvirt/images/danos-1908-amd64-vrouter.iso \
--graphics vnc,password=01Dodford,listen=192.168.30.2,port=5903 \
--network bridge=br0,model=virtio,mac=52:54:00:91:35:09 \
--network bridge=br2,model=virtio,mac=52:54:00:a0:d0:5e

virsh autostart danos1908

lsccli nodes mount danos1908 --host 192.168.90.253 --username admin --password admin --port 830 --cache DanOS --cshema

# add NETCONF node
curl -X PUT \
  http://192.168.90.171:8181/restconf/config/network-topology:network-topology/topology/topology-netconf/node/danos1908 \
  -H 'Accept: application/xml' \
  -H 'Authorization: Basic YWRtaW46YWRtaW4=' \
  -H 'Content-Type: application/xml' \
  -H 'cache-control: no-cache' \
  -d '<node xmlns="urn:TBD:params:xml:ns:yang:network-topology">
    <node-id>danos1908</node-id>
    <username xmlns="urn:opendaylight:netconf-node-topology">vyatta</username>
    <password xmlns="urn:opendaylight:netconf-node-topology">vyatta</password>
    <host xmlns="urn:opendaylight:netconf-node-topology">192.168.90.4</host>
    <schema-cache-directory xmlns="urn:opendaylight:netconf-node-topology">danos1908</schema-cache-directory>
    <port xmlns="urn:opendaylight:netconf-node-topology">830</port>
    <tcp-only xmlns="urn:opendaylight:netconf-node-topology">false</tcp-only>
    <schemaless xmlns="urn:opendaylight:netconf-node-topology">false</schemaless>
    <max-connection-attempts xmlns="urn:opendaylight:netconf-node-topology">0</max-connection-attempts>
    <connection-timeout-millis xmlns="urn:opendaylight:netconf-node-topology">20000</connection-timeout-millis>
    <default-request-timeout-millis xmlns="urn:opendaylight:netconf-node-topology">60000</default-request-timeout-millis>
    <sleep-factor xmlns="urn:opendaylight:netconf-node-topology">1.1</sleep-factor>
    <between-attempts-timeout-millis xmlns="urn:opendaylight:netconf-node-topology">2000</between-attempts-timeout-millis>
    <reconnect-on-changed-schema xmlns="urn:opendaylight:netconf-node-topology">false</reconnect-on-changed-schema>
    <keepalive-delay xmlns="urn:opendaylight:netconf-node-topology">60</keepalive-delay>
    <concurrent-rpc-limit xmlns="urn:opendaylight:netconf-node-topology">0</concurrent-rpc-limit>
    <actor-response-wait-time xmlns="urn:opendaylight:netconf-node-topology">300</actor-response-wait-time>
</node>'


Headers: GET whole config in XML:
Accept:application/xml
Content-Type:application/xml
Authorization:Basic YWRtaW46YWRtaW4=

Headers: GET whole config in JSON:
Accept:application/json
Content-Type:application/json
Authorization:Basic YWRtaW46YWRtaW4=

PUTs and DELETEs work on the same path as the GETs; just change the HTTP Method.

#
curl -X GET \
  http://192.168.90.171:8181/restconf/operational/network-topology:network-topology/topology/topology-netconf/ \
  -H 'Accept: application/xml' \
  -H 'Authorization: Basic YWRtaW46YWRtaW4=' \
  -H 'Content-Type: application/xml' \
  -H 'cache-control: no-cache'

# get node config
curl -X GET \
http://192.168.90.171:8181/restconf/config/network-topology:network-topology/topology/topology-netconf/node/danos1908/yang-ext:mount/ \
  -H 'Accept: application/xml' \
  -H 'Authorization: Basic YWRtaW46YWRtaW4=' \
  -H 'Content-Type: application/xml' \
  -H 'cache-control: no-cache'

# get available NETCONF modules on device/node
curl -X GET \
http://{sdn-controller-restconf-IP}:8181/restconf/config/network-topology:network-topology/topology/topology-netconf/node/{node}/ <http://%7Bsdn-controller-restconf-ip%7D:8181/restconf/config/network-topology:network-topology/topology/topology-netconf/node/%7Bnode%7D/>

# get node available capabilities
curl -X GET \
http://{sdn-controller-restconf-IP}:8181/restconf/operational/network-topology:network-topology/topology/topology-netconf/node/{node}/available-capabilities/ <http://%7Bsdn-controller-restconf-ip%7D:8181/restconf/operational/network-topology:network-topology/topology/topology-netconf/node/%7Bnode%7D/available-capabilities/> \
  -H 'Accept: application/xml' \
  -H 'Authorization: Basic YWRtaW46YWRtaW4=' \
  -H 'Content-Type: application/xml' \
  -H 'cache-control: no-cache'

# execute RPC on node
curl -X POST \
http://{sdn-controller-restconf-IP}:8181/restconf/config/network-topology:network-topology/topology/topology-netconf/node/{node}/yang-ext:mount/{module}:{operation <http://%7Bsdn-controller-restconf-ip%7D:8181/restconf/config/network-topology:network-topology/topology/topology-netconf/node/%7Bnode%7D/yang-ext:mount/%7Bmodule%7D:%7Boperation>} \
  -H 'Accept: application/xml' \
  -H 'Authorization: Basic YWRtaW46YWRtaW4=' \
  -H 'Content-Type: application/xml' \
  -H 'cache-control: no-cache' \
  -d '<required data for RPC call>'

# API explorer
http://192.168.90.171:8181/apidoc/explorer/index.html

# get vyatta services
curl -X GET \
http://192.168.90.171:8181/restconf/config/network-topology:network-topology/topology/topology-netconf/node/danos1908/yang-ext:mount/vyatta-services-v1:service \
  -H 'Accept: application/json' \
  -H 'Authorization: Basic YWRtaW46YWRtaW4=' \
  -H 'cache-control: no-cache'


# get Danos config
curl --location --request GET 'http://192.168.90.188:8181/restconf/config/network-topology:network-topology/topology/topology-netconf/node/danos1909/yang-ext:mount/vyatta-interfaces-v1:interfaces ' \
--header 'Accept: application/json' \
--header 'Content-Type: application/json' \
--header 'Authorization: Basic YWRtaW46bHVtaW5hJHNkbg==' \
--header 'Cookie: JSESSIONID=node05lwsu0kc8q4357t56up125sd4.node0'

# Add interface IP
curl --location --request PUT 'http://192.168.90.188:8181/restconf/config/network-topology:network-topology/topology/topology-netconf/node/danos1909/yang-ext:mount/vyatta-interfaces-v1:interfaces/dataplane/dp0s4' \
--header 'Accept: application/json' \
--header 'Content-Type: application/json' \
--header 'Authorization: Basic YWRtaW46bHVtaW5hJHNkbg==' \
--header 'Cookie: JSESSIONID=node01acykxmvqjy8619l0md0bkgsts3.node0' \
--data-raw '{ "vyatta-interfaces-dataplane-v1:dataplane":
     {
                "address": [
                    "192.168.10.100/24"
                ],
                "tagnode": "dp0s4"
            }
}'

# Delete interface IP
curl --location --request PUT 'http://192.168.90.188:8181/restconf/config/network-topology:network-topology/topology/topology-netconf/node/danos1909/yang-ext:mount/vyatta-interfaces-v1:interfaces/dataplane/dp0s4' \
--header 'Accept: application/json' \
--header 'Content-Type: application/json' \
--header 'Authorization: Basic YWRtaW46bHVtaW5hJHNkbg==' \
--header 'Cookie: JSESSIONID=node01acykxmvqjy8619l0md0bkgsts3.node0' \
--data-raw '{ "vyatta-interfaces-dataplane-v1:dataplane":
     {
                "address": [],
                "tagnode": "dp0s4"
            }
}'

```
