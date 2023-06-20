# RESTCONF

[TOC]

## Overview

RESTCONF is described in [RESTCONF RFC 8040](https://tools.ietf.org/html/rfc8040). Simple said, RESTCONF represents REST API to access datastores and operations.

### Datastores

There are two datastores:

1.  **Config**: Contains data representing intended state - it is possible to read and write it via RESTCONF.

2.  **Operational**: Contains data representing actual state - it is possible to only read it via RESTCONF.


**Note**: Each request must start with the URI _`/restconf/`_. By default, RESTCONF listens on port `8888` for HTTP requests.

### REST operations

RESTCONF supports **OPTIONS**, **GET**, **PUT**, **POST**, **PATCH**, and **DELETE** operations. Request and response data can either be in the XML or JSON format.

-   XML structures according to YANG are defined at: [XML-YANG](https://tools.ietf.org/html/rfc6020).

-   JSON structures are defined at: [JSON-YANG](https://tools.ietf.org/html/draft-lhotka-netmod-yang-json-02).


Data in the request must have a correctly set **Content-Type** field in the http header with the allowed value of the media type. The media type of the requested data has to be set in the **Accept** field. Get the media types for each resource by calling the OPTIONS operation.

Most of the paths use [Instance Identifier](https://wiki.opendaylight.org/view/OpenDaylight_Controller:MD-SAL:Concepts#Instance_Identifier). **<identifier>** is used in the explanation of the operations and has to keep these rules:

-   Identifier must start with <moduleName>:<nodeName>> where <moduleName> is a name of the YANG module and <nodeName> is the name of a node in the module. If the next node name is placed in the same namespace like the previous one, it is sufficient to just use <nodeName> after the first definition of <moduleName>:<nodeName>. Each <nodeName> has to be separated by /.

-   <nodeName> can represent a data node which is a list node, container, leaf, or leaf-list YANG built-in type. If the data node is a list, there must be defined ordered keys of the list behind the data node name, for example, <nodeName>=<valueOfKey1>,<valueOfKey2>.


Note

The following example shows how reserved characters are percent-encoded within a key value. The value of “key1” contains a comma, single-quote, double-quote, colon, double-quote, space, and forward slash (,`”:” /). Note that double-quote is not a reserved character and does not need to be percent-encoded. The value of “key2” is the empty string, and the value of “key3” is the string “foo”.

Example URL: `/restconf/data/example-top:top/list1=%2C%27”%3A”%20%2F,,foo`

-   The format <moduleName>:<nodeName> has to be used in this case as well. Module A has node A1. Module B augments node A1 by adding node X. Module C augments node A1 by adding node X. For clarity, it has to be known which node is X (for example: C:X).


### Mount point

Mount point usually represents an external system. A node can be behind a mount point. In this case, the URI has to be in the format <identifier>/**yang-ext:mount**/<identifier>. The first <identifier> is the path to a mount point and the second <identifier> is the path to a node behind the mount point. An URI can end in a mount point itself by using <identifier>/yang-ext:mount. More information on how to actually use mountpoints is available at: [OpenDaylight Controller:Config:Examples:Netconf](https://wiki.opendaylight.org/view/OpenDaylight_Controller:Config:Examples:Netconf).

## HTTP methods

### OPTIONS /RESTCONF

-   Returns the XML description of the resources with the required request and response media types in Web Application Description Language (WADL).


### GET /restconf/data/<identifier>?content=config

-   Returns a data node from the Config datastore.

-   <identifier> points to a data node which must be retrieved.


### GET /restconf/data/<identifier>?content=nonconfig

-   Returns the value of the data node from the Operational datastore.

-   <identifier> points to a data node which must be retrieved.


### GET /restconf/data/<identifier>

-   Returns a data node from both Config and Operational datastores. The outputs from both datastores are merged to one output.

-   <identifier> points to a data node which must be retrieved.


### PUT /restconf/data/<identifier>

-   Updates or creates data in the Config datastore and returns the state about success.

-   <identifier> points to a data node which must be stored.

-   Content type does not have to be specified in URI - it can only be the Configuration datastore.


Example:

PUT http://localhost:8888/restconf/data/module1:foo/bar
Content-Type: `applicaton/xml`
```xml
<bar>
  …
</bar>
```

Example with mount point:

PUT http://localhost:8888/restconf/data/module1:foo1/foo2/yang-ext:mount/module2:foo/bar
Content-Type: `applicaton/xml`

```xml
<bar>
  …
</bar>
```

### POST /restconf/data/<identifier>

-   Creates the data if it does not exist in the Config datastore, and returns the state about success.

-   <identifier> points to a data node where data must be stored.

-   The root element of data must have the namespace (data is in XML) or module name (data is in JSON).

Example:

POST http://localhost:8888/restconf/data/<identifier>
Content-Type: `applicaton/xml`
```xml
<bar xmlns=“module1namespace”\>
  …
</bar>
```

Example with mount point:

http://localhost:8888/restconf/data/module1:foo1/foo2/yang-ext:mount/module2:foo
Content-Type: `applicaton/xml`
```xml
<bar xmlns=“module2namespace”\>
  …
</bar>
```

### POST /restconf/data

-   Creates the data if it does not exist under data root.

-   In the following example,`toaster` module is root container in YANG (it does not have any parent). This example also makes it clear that URI does not contain`toaster` node in comparison to PUT request that must contain name of the created node in URI.


Example:

POST URL: http://localhost:8888/restconf/data
content-type: `application/json`
JSON payload:

```json
   {
     "toaster:toaster" :
     {
       "toaster:toasterManufacturer" : "General Electric",
       "toaster:toasterModelNumber" : "123",
       "toaster:toasterStatus" : "up"
     }
   }
```

### DELETE /restconf/data/<identifier>

-   Removes the data node in the Config datastore and returns the state about success.

-   <identifier> points to a data node which must be removed.


### PATCH /restconf/data/<identifier>

-   The patch request merges the contents of the message-body with the target resource in Configuration datastore (content-type query parameter is not specified).

-   <identifier> points to a data node on which PATCH operations is invoked.

-   This request is implemented by Plain PATCH functionality - see more details on the following page: [RFC-8040 documentation - Plain PATCH operation](https://tools.ietf.org/html/rfc8040#section-4.6.1).

-   Plain patch can be used to create or update, but not delete, a child resource within the target resource. Any pre-existing data which is not explicitly overwritten will be preserved. This means that if you store a container, its child entities will also be merge recursively.


The following example shows the PATCH request used for modification of Ethernet interface IP address and two connection settings. Note that other settings under system:system container are left untouched including other leaves under`connection` container and`ethernet` list item.
```bash
curl --location --request PATCH 'http://localhost:8888/restconf/data/network-topology:network-topology/topology=uniconfig/node=n1/configuration/system:system' \\
--header 'Content-Type: `application/json`' \\
--data-raw '{
 "system:system": {
 "wan": {
 "endpoint": {
 "interfaces": {
 "ethernet": [
 {
 "name": "ethernet1",
 "inet": {
 "address": "192.168.10.1/24"
 }
 }
 ]
 }
 }
 },
 "connection": {
 "syn-flood-check": true,
 "ack-flood-check": true
 }
 }
}'
```

### POST /restconf/operations/<moduleName>:<rpcName>

-   Invokes RPC on the specified path.

-   <moduleName>:<rpcName> - <moduleName> is the name of the module and <rpcName> is the name of the RPC in this module. This may also include the NETCONF standard RPCs including ietf-netconf:get, ietf-netconf:get-config, ietf-netconf:edit-config, ietf-netconf:lock, ietf-netconf:unlock etc

-   The Root element of the data sent to RPC must have the name “input”.

-   The result have the status code and optionally retrieved data having the root element “output”.


Example:

POST http://localhost:8888/restconf/operations/module1:fooRpc
Content-Type: `applicaton/xml`
Accept: `applicaton/xml`
```xml
<input>
  …
</input>
```

The answer from the server could be:
```xml
<output>
  …
</output>
```

An example using a JSON payload:

POST http://localhost:8888/restconf/operations/toaster:make-toast
Content-Type: application/yang.data+json
```json
{
  "input" :
  {
     "toaster:toasterDoneness" : "10",
     "toaster:toasterToastType":"wheat-bread"
  }
}
```

Note

**GET /restconf/operations** request can be used to retrieve all available RPCs that are registered in distribution.

Note

More information is available in the [RESTCONF RFC 8040](https://tools.ietf.org/html/rfc8040).

### POST /restconf/data/<path-to-operation>

-   Invokes action on the specified path in the data tree.

-   Placeholder <path-to-operation> represents data path to operation definition that is specified under composite data schema node in YANG (only containers and lists may contain action definition).

-   Content query parameter doesn`t have to be specified (it will be ignored) - action is represented equally in Operational and Config datastore.

-   The body of the action invocation request may contain root`input` container. If action definition has no specified input container, it is not required to specify body in the request.

-   The response contains the status code and optionally retrieved data having the root element`output`.

-   Structure of `input` and`output` elements are the same like structure of these containers when we invoke YANG RPC.


Assume the following YANG snippet with root container named`interfaces`:
```
container interfaces {
    action compute-stats {
        input {
            leaf month {
                type string;
            }
            leaf year {
                type uint16;
            }
            leaf percentile {
                type uint8;
            }
        }
        output {
            leaf avg-rx-kbps {
                type uint32;
            }
            leaf avg-tx-kbps {
                type uint32;
            }
        }
    }
}
```

Invocation of action named`compute-stats` that is placed under`interfaces` container of NETCONF mountpoint:
```bash
curl --location --request POST 'http://localhost:8888/restconf/data/network-topology:network-topology/topology=topology-netconf/node=dev/yang-ext:mount/interfaces:interfaces/compute-stats' \\
--header 'Content-Type: `application/json`' \\
--data-raw '{
 "input": {
 "month": "April",
 "year": 2018,
 "percentile": 50
 }
}'
```

The response body:
```json
{
    "interfaces:output": {
        "avg-rx-kbps": 14524,
        "avg-tx-kbps": 47787
    }
}
```

Note

Difference between RPCs and actions: Actions are bound to data tree and they can be placed under containers and lists (they cannot be specified as root entities in YANG schema). RPCs are not placed in the data tree and from this reason they can only be specified as root entities in the YANG schema.

## Filtering data

For filtering and identifying data is good to use query parameter **fields**. This parameter has to be used only with **GET** method. The response body is output filtered by field-expression as value of fields parameter.

The example of using fields parameter:

Note

**path?fields=field\_expression**

There are several rules, that needs to be followed:

1.  For filtering more than one field of the same parent, “;” needs to be used. Example : **path?fields=field1;field2**, where field1 and field2 has the same parent, which is the very last part of path.

2.  For nesting, “/” needs to be used. Example : **path?fields=field1;pathField/field2**, where field1 and field2 has not the same parent, but pathField is on the same level as field1.

3.  Third character “(” , “)” is used to specify sub-selectors.

    Example: **path ? fields = pathField( field1; pathField2( field2; pathField3( field3 ) ) )**

    This is different approach to do nesting, however the difference between “(” and “/” is that once we use “/” for specifying some field, we cannot identify another field from upper layers.

    Example: **path ? fields = pathField / field1; pathField2 / field2**

    This is the case where pathField1 and pathField2 have the same parent, this is not allowed, because once we use “;” it is expected to specify fields on the same layer as field1


Examples - with 2 approaches (nesting, sub-selecting)

<u>Note</u>

Example of filtering whole configuration of all interfaces (name, with whole config)

> **Using sub-selectors**
`http://localhost:8888/restconf/data/network-topology:network-topology/topology\=uniconfig/node\=IOSXR/configuration?fields\=frinx-openconfig-interfaces:interfaces(interface)&content\=nonconfig`

> **Using nesting**
` http://localhost:8888/restconf/data/network-topology:network-topology/topology\=uniconfig/node\=IOSXR/configuration?fields\=frinx-openconfig-interfaces:interfaces/interface/name;config&content\=nonconfig`

<u>Note</u>

Example of filtering all names of interfaces and all names of configs of interfaces

> **Using sub-selectors**
http://localhost:8888/restconf/data/network-topology:network-topology/topology\=uniconfig/node\=IOSXR/configuration?fields\=frinx-openconfig-interfaces:interfaces(interface(name;config(name)))&content\=nonconfig`
>
> **Using nesting**
`http://localhost:8888/restconf/data/network-topology:network-topology/topology\=uniconfig/node\=IOSXR/configuration?fields\=frinx-openconfig-interfaces:interfaces/interface/name;config/name&content\=nonconfig`

<u>Note</u>

Example of filtering all names of interfaces with type from config of interfaces

> **Using sub-selectors**
`http://localhost:8888/restconf/data/network-topology:network-topology/topology\=uniconfig/node\=IOSXR/configuration?fields\=frinx-openconfig-interfaces:interfaces(interface(name;config(type)))&content\=nonconfig`

> **Using nesting**
`http://localhost:8888/restconf/data/network-topology:network-topology/topology\=uniconfig/node\=IOSXR/configuration?fields\=frinx-openconfig-interfaces:interfaces/interface/name;config/type&content\=nonconfig`

## JSON attributes

Node attributes can be encoded in JSON by wrapping of all attributes in the`@` container and values or arrays in the`#` JSON element. This notation is inspired by one that is used in the`js2xmlparser` open-source tool (conversion between JSON and XML structures): [js2xmlparser](https://github.com/michaelkourlas/node-js2xmlparser#readme)

RESTCONF supports both serialization and deserialization of attributes - GET response shows all set attributes in the read data-tree and PUT/POST/PLAIN PATCH methods can be used for writing of data nodes with attributes. Warning: attributes cannot be directly addressed using RESTCONF URI that would contain`@` element in the path, because attributes are always bound to some data node - they are not represented by distinct nodes in the data-tree.

Reserved`@` container may contain multiple attributes. Each attribute is encoded in the same fashion like leaf nodes - there is an identifier of the attribute and attribute value.

Format of the attribute that is defined in the [module]:

`"[module]:[attribute-name]": [value]`

Format of the attribute that is defined in the the same module like the parent data entity:

`"[attribute-name]": [value]`

### Example - leaf with attributes

Leaf without attributes:
```json
{
  "sample-leaf": 3
}
```

The same leaf with set 2 attributes - `m1:attribute-1` and `m1:attribute-2`:
```json
{
  "sample-leaf": {
    "@": {
      "m1:attribute-1": "value",
      "m1:attribute-2": 7
    },
    "#": 3
  }
}
```

### Example - container with attributes

Container without attributes:
```json
{
  "sample-container": {
    "nested-leaf-1": "str1",
    "nested-container": {
      "l1": 10,
      "l2": true
    }
  }
}
```

The same container with set 2 attributes - `m1:switch` and`m2:multiplier`:
```json
{
  "sample-container": {
    "@": {
      "m1:switch": true,
      "m2:multiplier": 10
    },
    "nested-leaf-1": "str1",
    "nested-container": {
      "l1": 10,
      "l2": true
    }
  }
}
```

### Example - leaf-list with attributes

Leaf-list without attributes:
```json
{
  "sample-leaf-list": [10, 20, 30]
}
```

The same leaf with set 1 attribute -`mx:split`:
```json
{
  "sample-leaf-list": {
    "@": {
      "mx:split": true
    },
    "#": [10, 20, 30]
  }
}
```

### Example - leaf-list entry with attributes

Leaf-list without attributes:
```json
{
  "sample-leaf-list": [10, 20]
}
```

Two leaf-list entries - leaf-list entry with value`10` has one attribute with identifier`m1:prefix`. The second leaf-list entry`20` doesn`t have any attributes assigned.
```json
{
  "sample-leaf-list": [
    {
      "@": {
        "m1:prefix": "arp-"
      },
      "#": 10
    },
    20
  ]
}
```
### Example - list with attributes

List without attributes:
```json
{
  "sample-list": [
    {
      "key": "k1",
      "value": 1
    },
    {
      "key": "k2",
      "value": 2
    }
  ]
}
```

The same list with applied single attribute -`constraints:length`.
```json
{
  "sample-list": {
    "@": {
      "constraints:length": 100
    },
    "#": [
      {
        "key": "k1",
        "value": 1
      },
      {
        "key": "k2",
        "value": 2
      }
    ]
  }
}
```

### Example - list entry with attributes

List with two list entries without attributes:
```json
{
  "sample-list": [
    {
      "key": "k1",
      "value": 1
    },
    {
      "key": "k2",
      "value": 2
    }
  ]
}
```

The same list entries - the first list entry doesn`t contain any attribute, but the second list entry contains 2 attributes`m1:switch` and`m2:multiplier`.
```json
{
  "sample-list":  [
    {
      "key": "k1",
      "value": 1
    },
    {
      "key": "k2",
      "value": 2,
      "@": {
        "m1:switch": true,
        "m2:multiplier": 10
      }
    }
  ]
}
```

## Device schema filters

By default, all input and output data produced by RESTCONF for the selected device is fully compliant with its YANG models. Any violation of the yang schema definitions will result in an error. Some of these restrictions can be addressed by adding the`schemaFilters` configuration parameter for the RESTCONF.

### Configuration options overview

Following configuration options for`schemaFilters` make RESTCONF processing less restrictive:

| Name | Type | Description |
| ---- | ---- | ----------- |
|    ignoredDataOnWriteByExtensions  |   array   |    RESTCONF will ignore definitions with any of the selected extensions during writing to the datastore         |
|  hiddenDataOnReadByExtensions    |  array    |   RESTCONF will not include definition with the selected extensions in the responses to the GET requests.          |
|  ignoreUnsupportedDefinitionsOnWrite    |  boolean    |  Any unknown element provided in the body for POST/PUT/PATCH requests will be ignored by RESTCONF           |
|  hideDeprecatedDefinitionsOnRead    | boolean     |  RESTCONF will not include definitions marked with`status deprecated` in the responses to the GET requests.           |


### Configuration example

The following example demonstrates how to enable schema filters for selected extensions and make RESTCONF ignore unknown definitions and definitions with a`deprecated status` attribute.
```json
"schemaFilters": {
  "ignoredDataOnWriteByExtensions": [
    "tailf:hidden full"
  ],
  "hiddenDataOnReadByExtensions": [
    "tailf:hidden deprecated",
    "tailf:hidden debug"
  ],
    "ignoreUnsupportedDefinitionsOnWrite": true,
    "hideDeprecatedDefinitionsOnRead": true
}
```

### Unhide parameter for read operation

RESTCONF supports the`unhide` query parameter for the GET requests to include hidden definitions into the response. This parameter value can be populated with a comma-separated list of extensions to unhide or the keyword`all` to include all possible hidden definitions in the response.

Example of using the`unhide` parameter for the GET request.

**Using unhide with a list of extensions**
`http://localhost:8888/restconf/data/network-topology:network-topology/topology=uniconfig/node=device/configuration?unhide=tailf:hidden debug,tailf:hidden deprecated`

**Using unhide parameter to unhide all hidden definitions**
` http://localhost:8888/restconf/data/network-topology:network-topology/topology=uniconfig/node=device/configuration?unhide=all`

