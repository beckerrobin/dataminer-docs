---
uid: ConnectionsWebSocketsImplementation
---

# Implementing a WebSocket

DataMiner supports WebSockets since version 8.5.7 (RN 9962). To create a WebSocket, define a new WebSocket connection in the protocol. The Connection tag needs to be included in the Protocol.Connections tag.

```xml
<Connection id="1" name="WebSocket Connection Panel">
   <Http>
      <CommunicationOptions>
         <WebSocket>true</WebSocket>
         <NotifyConnectionPIDs>
            <Connections>7</Connections>
         </NotifyConnectionPIDs>
         <MakeCommandByProtocol>true</MakeCommandByProtocol>
      </CommunicationOptions>
      <UserSettings>
         <BusAddress>
            <DefaultValue>bypassProxy</DefaultValue>
         </BusAddress>
         <IPport>
            <DefaultValue>80</DefaultValue>
         </IPport>
         <TimeoutTime>
            <DefaultValue>1500</DefaultValue>
         </TimeoutTime>
      </UserSettings>
  </Http>
</Connection>
```

By default, DataMiner will construct a default opening handshake. However, it is possible to define a custom WebSocket opening handshake, by defining an HTTP request in the protocol. This must be a Session with only one Connection and every value must be a configured parameter where the value is a parameter ID that contains the value. The custom WebSocket opening handshake is then referred to through the WebSocketHandshake tag.

For example:

```xml
<HTTP>
   <Session id="1">
      <Connection id="1">
         <Request verb="GET" url="/">
            <Headers>
               <Header key="Use-Cookie" pid="80" />
            </Headers>
         </Request>
      </Connection>
   </Session>
</HTTP>
```

This results in the following opening handshake:

```xml
GET /chat HTTP/1.1
Host: server.example.com
Use-Cookie: [value of parameter 80]
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Key: x3JJHMbDL1EzLkh9GBhXDw==
Sec-WebSocket-Protocol: chat
Sec-WebSocket-Version: 13
```

If the URL used to set up the connection comes from a parameter value, e.g. from parameter 10, then specify url="10" (hard-coded values are not supported for the url attribute).

For example, suppose you need to connect to ws://10.4.2.8:4601/x-nmos/events, then there are two possibilities:

- If the IP address/host and port are known by the operator and the last part of the URL is fixed:
  - Set “ws://10.4.2.8” as the IP address/host of the element in DataMiner Cube.
  - Set “4601” as the IP port of the element in DataMiner Cube.
  - Set `<Request verb="GET" url="10">`.
  - Set parameter 10 to the value “/x-nmos/events”.
- If the URL first needs to be polled, use a parameter with `<Type options="dynamic ip">read</Type>` and set as value the complete URL: ws://10.4.2.8:4601/x-nmos/events

## Behavior

When the WebSocket server closes the connection for some reason, the element goes into timeout. Sent commands will still appear in the Stream Viewer, but no outgoing communication will be displayed. To have communication again, the element needs to be restarted or you need to use a "dynamic ip" parameter and fill in the value again.

## Binary vs. Text Data Frames

By default, DataMiner sends the WebSocket messages as binary data (i.e. a frame with Opcode 0x2, RFC 6455). Some WebSocket servers will reply with an \[ACK\] packet but ignore the message as the server does not support binary formatted messages.

If the message you want to send only contains text and the server does not seem to support binary formatted messages, try to add `<WebSocketMessageType>text</WebSocketMessageType>` to the `<Command>`. This will result in the command being sent as UTF-8 encoded text (Opcode 0x1, RFC 6455).

In case the server supports text frames, it should now respond to this command. Note that the WebSocketMessageType tag is only supported since DataMiner 9.5.1 (RN 14177).
