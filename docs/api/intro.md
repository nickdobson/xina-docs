---
id: api-intro
title: API Introduction
---

The XINA API provides direct access to the XINA server.

## XINA Tunnel

XINA Tunnel is a Java application which provides a bridge between application(s) using the XINA API and the XINA server.

To access the XINA server, the user must first launch the XINA Tunnel application, which will prompt the user to log in
to the XINA server. Once this step is completed XINA Tunnel launches a local server, which provides a tunnel to the XINA
server. Applications implementing the XINA API may then open a standard TCP connection to XINA Tunnel and begin
communicating with the XINA server.

## XProtocol

**XProtocol** is the TCP protocol used to communicate with the XINA server.

XProtocol is designed to be simple and easy to implement. It uses a basic data encoding method which can be parsed
in-place as a stream is read. All communication takes place in the form of UTF-8 encoded strings. The strings are
encoded in **tokens** of the following format:

* prefix length: one byte ASCII digit indicating length of the prefix in bytes
* prefix: ASCII digits indicating the length of the string in bytes
* content: UTF-8 encoded string or binary data of (prefix count) bytes

For example:

* `14cake` = `"cake"`
* `213big hamburger` = `"big hamburger"`

The maximum allowed length of a single token is 2GB. A token may also be empty, which can be denoted with either the
prefix `10` or the shorthand prefix `0`.

Tokens are combined together into **packets**, which form all communication between the client and server.

**Client packets** are sent from the client to server. They use the following format:

* packet type :: one byte ASCII character
* header token :: JSON object containing header information (only used currently for system functions, should be empty)
* content token :: UTF-8 encoded JSON object, binary data, or empty depending on token type

Client packets use the following packet types:

|Type        |Code |Description 
|------------|-----|--------------------------------------------------------
|`ACTION`    | `A` | contains an API action (most common packet type)
|`BINARY`    | `B` | contains binary data (used for transmitting file data)
|`CLOSE`     | `X` | closes the connection
|`CONTINUE`  | `C` | prompts continuing a data stream from the server 
|`END`       | `E` | indicates the end of a series of binary packets
|`INIT`      | `I` | initializes the connection
|`KEEPALIVE` | `K` | ignored by both server and client, keeps connection open
|`OBJECT`    | `O` | indicates the start of a binary object

**Server packets**, inversely, are sent from the server to clients. They use the following format:

* packet type :: one byte ASCII character
* header token :: JSON object containing header information (only used currently for system functions, typically empty)
* status token :: JSON object containing status information
* content token :: UTF-8 encoded JSON object, binary data, or empty depending on token type

Server packets use the following packet types:

|Type        |Code |Description 
|------------|-----|----------------------------------------------------------
|`KEEPALIVE` | `K` | ignored by both server and client, keeps connection open
|`SERVER`    | `S` | primary server packet type, used for all functions

The server packet status token is a JSON object in the following format:

```
 {
  "type"    : "OK" or "ER",
  "code"    : <int>,
  "message" : <string, optional>
 }
```

The `"type"` indicates if an action succeeded (`"OK"`) or failed (`"ER"`). The `"code"` is a numeric identifier for the
status and will be in the range of 100 to 500.

| Code  | Description
|-------|------------------------------
| `1XX` | Success, more data available
| `2XX` | Success, data ended
| `4XX` | Content error
| `5XX` | Server error

The optional `"message"` contains a plain text description of the status or error.

## Control Flow

In practice the general design of XProtocol is call and response. Each packet (of most types) sent by a client will
receive a single server packet in response.

### Initialization

When an application opens a connection with the XINA Tunnel, it must first send a single `INIT` packet containing a
JSON object:

```json
{ "version": "3.0" }
```

Currently the only attribute for this object is the XProtocol version number, which is currently 3.0. More attributes
may be added in the future. The XINA Tunnel will then respond with a server packet indicating if the initialization is
accepted. If it is not, the connection will then be closed by XINA Tunnel. If it is accepted the application may then
begin sending other XProtocol commands.

### Action

The bulk of the XProtocol syntax consists of a collection of discrete **actions**. Actions are fully **transactional**;
any changes performed by an action must **all** be successful or **no** changes will be committed.

Each action is encoded as a single JSON object. The format for each action depends on the action type, which is listed
later in this documentation. However there are two basic forms of actions; **read** actions, which return data, and
**write** actions, which do not.

For write actions, the server will respond with a single server packet, indicating success or failure for the write
action:

|client  |    |server  |
|--------|----|--------|
|`ACTION`|`->`|        |
|        |`<-`|`SERVER`|

For read actions, the server will respond with a single server packet, indicating success or failure for the read
action, and including the requested data. If a large data set is selected it may span multiple packets. This is
indicated by a `1XX` status code from the server. In this case the client can respond with a `CONTINUE` packet, and
the server will respond with another `SERVER` packet. This can continue until all data is sent, indicated by a `2XX`
status code.

|client    |    |server        |
|----------|----|--------------|
|`ACTION`  |`->`|              |
|          |`<-`|`SERVER` `1XX`|
|`CONTINUE`|`->`|              |
|          |`<-`|`SERVER` `1XX`|
|`CONTINUE`|`->`|              |
|          |`<-`|`SERVER` `2XX`|