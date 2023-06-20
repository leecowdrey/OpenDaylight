# OpenDaylight NETCONF Trace

```
NETCONF ENCODE >>>>>
LOG FILE: /Users/peter/telefonica-tsp/test-controller/request-response-karaf-examples.log
LOG LINE: 4510
    TIME: 2020-02-20T17:35:58,474
  DEVICE: 172.29.145.45:33001
PACKETS: 45
<rpc xmlns="urn:ietf:params:xml:ns:netconf:base:1.0" message-id="m-6">
  <lock>
    <target>
      <candidate/>
    </target>
  </lock>
</rpc>
NETCONF DECODE <<<<<
LOG FILE: /Users/peter/telefonica-tsp/test-controller/request-response-karaf-examples.log
LOG LINE: 4525
    TIME: 2020-02-20T17:35:58,478
  DEVICE: 172.29.145.45:33001
PACKETS: 101
<rpc-reply xmlns="urn:ietf:params:xml:ns:netconf:base:1.0" xmlns:nc="urn:ietf:params:xml:ns:netconf:base:1.0" message-id="m-6">
  <ok/>
</rpc-reply>
NETCONF ENCODE >>>>>
LOG FILE: /Users/peter/telefonica-tsp/test-controller/request-response-karaf-examples.log
LOG LINE: 4519
    TIME: 2020-02-20T17:35:58,477
  DEVICE: 172.29.145.45:33001
PACKETS: 46
<rpc xmlns="urn:ietf:params:xml:ns:netconf:base:1.0" message-id="m-7">
  <get-config>
    <source>
      <running/>
    </source>
    <filter xmlns:ns0="urn:ietf:params:xml:ns:netconf:base:1.0" ns0:type="subtree">
      <control-construct xmlns="urn:onf:yang:core-model-1-4">
        <logical-termination-point>
          <uuid>LTP-MWPS-TTP-RADIO-1A</uuid>
          <layer-protocol>
            <local-id>LP-MWPS-TTP-RADIO-1A</local-id>
            <air-interface-pac xmlns="urn:onf:yang:air-interface-2-0">
              <air-interface-configuration>
                <problem-kind-severity-list>
                  <problem-kind-name>radioIsFaulty</problem-kind-name>
                </problem-kind-severity-list>
              </air-interface-configuration>
            </air-interface-pac>
          </layer-protocol>
        </logical-termination-point>
      </control-construct>
    </filter>
  </get-config>
</rpc>
NETCONF DECODE <<<<<
LOG FILE: /Users/peter/telefonica-tsp/test-controller/request-response-karaf-examples.log
LOG LINE: 4550
    TIME: 2020-02-20T17:35:58,491
  DEVICE: 172.29.145.45:33001
PACKETS: 102
<rpc-reply xmlns="urn:ietf:params:xml:ns:netconf:base:1.0" xmlns:nc="urn:ietf:params:xml:ns:netconf:base:1.0" last-modified="2020-02-20T14:16:27Z" message-id="m-7">
  <data>
    <control-construct xmlns="urn:onf:yang:core-model-1-4">
      <logical-termination-point>
        <uuid>LTP-MWPS-TTP-RADIO-1A</uuid>
        <layer-protocol>
          <local-id>LP-MWPS-TTP-RADIO-1A</local-id>
          <air-interface-pac xmlns="urn:onf:yang:air-interface-2-0">
            <air-interface-configuration>
              <problem-kind-severity-list>
                <problem-kind-name>radioIsFaulty</problem-kind-name>
                <problem-kind-severity xmlns:air-interface="urn:onf:yang:air-interface-2-0">air-interface:SEVERITY_TYPE_MINOR</problem-kind-severity>
              </problem-kind-severity-list>
            </air-interface-configuration>
          </air-interface-pac>
        </layer-protocol>
      </logical-termination-point>
    </control-construct>
  </data>
</rpc-reply>
NETCONF ENCODE >>>>>
LOG FILE: /Users/peter/telefonica-tsp/test-controller/request-response-karaf-examples.log
LOG LINE: 4591
    TIME: 2020-02-20T17:35:58,502
  DEVICE: 172.29.145.45:33001
PACKETS: 47
<rpc xmlns="urn:ietf:params:xml:ns:netconf:base:1.0" message-id="m-8">
  <edit-config>
    <target>
      <candidate/>
    </target>
    <error-option>rollback-on-error</error-option>
    <config>
      <control-construct xmlns="urn:onf:yang:core-model-1-4">
        <logical-termination-point>
          <uuid>LTP-MWPS-TTP-RADIO-1A</uuid>
          <layer-protocol>
            <local-id>LP-MWPS-TTP-RADIO-1A</local-id>
            <air-interface-pac xmlns="urn:onf:yang:air-interface-2-0">
              <air-interface-configuration/>
            </air-interface-pac>
          </layer-protocol>
        </logical-termination-point>
      </control-construct>
    </config>
  </edit-config>
</rpc>
NETCONF ENCODE >>>>>
LOG FILE: /Users/peter/telefonica-tsp/test-controller/request-response-karaf-examples.log
LOG LINE: 4748
    TIME: 2020-02-20T17:35:58,511
  DEVICE: 172.29.145.45:33001
PACKETS: 48
<rpc xmlns="urn:ietf:params:xml:ns:netconf:base:1.0" message-id="m-9">
  <edit-config>
    <target>
      <candidate/>
    </target>
    <error-option>rollback-on-error</error-option>
    <config>
      <control-construct xmlns="urn:onf:yang:core-model-1-4">
        <logical-termination-point>
          <uuid>LTP-MWPS-TTP-RADIO-1A</uuid>
          <layer-protocol>
            <local-id>LP-MWPS-TTP-RADIO-1A</local-id>
            <air-interface-pac xmlns="urn:onf:yang:air-interface-2-0">
              <air-interface-configuration>
                <problem-kind-severity-list xmlns:a="urn:ietf:params:xml:ns:netconf:base:1.0" a:operation="replace">
                  <problem-kind-name>radioIsFaulty</problem-kind-name>
                  <problem-kind-severity>SEVERITY_TYPE_MAJOR</problem-kind-severity>
                </problem-kind-severity-list>
              </air-interface-configuration>
            </air-interface-pac>
          </layer-protocol>
        </logical-termination-point>
      </control-construct>
    </config>
  </edit-config>
</rpc>
NETCONF ENCODE >>>>>
LOG FILE: /Users/peter/telefonica-tsp/test-controller/request-response-karaf-examples.log
LOG LINE: 4768
    TIME: 2020-02-20T17:35:58,514
  DEVICE: 172.29.145.45:33001
PACKETS: 49
<rpc xmlns="urn:ietf:params:xml:ns:netconf:base:1.0" message-id="m-10">
  <commit/>
</rpc>
NETCONF DECODE <<<<<
LOG FILE: /Users/peter/telefonica-tsp/test-controller/request-response-karaf-examples.log
LOG LINE: 4775
    TIME: 2020-02-20T17:35:58,534
  DEVICE: 172.29.145.45:33001
PACKETS: 103
<rpc-reply xmlns="urn:ietf:params:xml:ns:netconf:base:1.0" xmlns:nc="urn:ietf:params:xml:ns:netconf:base:1.0" message-id="m-8">
  <ok/>
</rpc-reply>
NETCONF DECODE <<<<<
LOG FILE: /Users/peter/telefonica-tsp/test-controller/request-response-karaf-examples.log
LOG LINE: 4789
    TIME: 2020-02-20T17:35:58,554
  DEVICE: 172.29.145.45:33001
PACKETS: 104
<rpc-reply xmlns="urn:ietf:params:xml:ns:netconf:base:1.0" xmlns:air-interface="urn:onf:yang:air-interface-2-0" xmlns:core-model="urn:onf:yang:core-model-1-4" xmlns:nc="urn:ietf:params:xml:ns:netconf:base:1.0" message-id="m-9">
  <ok/>
</rpc-reply>
NETCONF DECODE <<<<<
LOG FILE: /Users/peter/telefonica-tsp/test-controller/request-response-karaf-examples.log
LOG LINE: 4805
    TIME: 2020-02-20T17:35:58,618
  DEVICE: 172.29.145.45:33001
PACKETS: 105
<rpc-reply xmlns="urn:ietf:params:xml:ns:netconf:base:1.0" xmlns:air-interface="urn:onf:yang:air-interface-2-0" xmlns:core-model="urn:onf:yang:core-model-1-4" xmlns:nc="urn:ietf:params:xml:ns:netconf:base:1.0" message-id="m-10">
  <ok/>
</rpc-reply>
NETCONF DECODE <<<<<
LOG FILE: /Users/peter/telefonica-tsp/test-controller/request-response-karaf-examples.log
LOG LINE: 4821
    TIME: 2020-02-20T17:35:58,622
  DEVICE: 172.29.145.45:33001
PACKETS: 106
<notification xmlns="urn:ietf:params:xml:ns:netconf:notification:1.0">
  <eventTime>2020-02-20T16:35:58Z</eventTime>
  <netconf-config-change xmlns="urn:ietf:params:xml:ns:yang:ietf-netconf-notifications">
    <changed-by>
      <username>admin</username>
      <session-id>5</session-id>
      <source-host>172.29.145.222</source-host>
    </changed-by>
    <edit>
      <target xmlns:air-interface="urn:onf:yang:air-interface-2-0" xmlns:core-model="urn:onf:yang:core-model-1-4">/core-model:control-construct/core-model:logical-termination-point[core-model:uuid='LTP-MWPS-TTP-RADIO-1A']/core-model:layer-protocol[core-model:local-id='LP-MWPS-TTP-RADIO-1A']/air-interface:air-interface-pac/air-interface:air-interface-configuration/air-interface:problem-kind-severity-list[air-interface:problem-kind-name='radioIsFaulty']</target>
      <operation>replace</operation>
    </edit>
  </netconf-config-change>
</notification>
```
