### Network Requests

Nuclei can act as an automatable **Netcat**, allowing users to send bytes across the wire and receive them, while providing matching and extracting capabilities on the response.

Network Requests start with a **network** block which specifies the start of the requests for the template.

```yaml
# Start the requests for the template right here
tcp:
```

#### Inputs

First thing in the request is **inputs**. Inputs are the data that will be sent to the server, and optionally any data to read from the server.

At it's most simple, just specify a string, and it will be sent across the network socket.

```yaml
# inputs is the list of inputs to send to the server
inputs: 
  - data: "TEST\r\n"
```

You can also send hex encoded text that will be first decoded and the raw bytes will be sent to the server.

```yaml
inputs:
  - data: "50494e47"
    type: hex
  - data: "\r\n"
```

Helper function expressions can also be defined in input and will be first evaluated and then sent to the server. The last Hex Encoded example can be sent with helper functions this way - 

```yaml
inputs:
  - data: 'hex_decode("50494e47")\r\n'
```

One last thing that can be done with inputs is reading data from the socket. Specifying `read-size` with a non-zero value will do the trick. You can also assign the read data some name, so matching can be done on that part.

```yaml
inputs:
  - read-size: 8
```

Example with reading a number of bytes, and only matching on them.

```yaml
inputs:
  - read-size: 8
    name: prefix
...
matchers:
  - type: word
    part: prefix
    words: 
      - "CAFEBABE"
```

Multiple steps can be chained together in sequence to do network reading / writing.

#### Host

The next part of the requests is the **host** to connect to. Dynamic variables can be placed in the path to modify its value on runtime. Variables start with `{{` and end with `}}` and are case-sensitive.

1. **Hostname** - variable is replaced by the hostname provided on command line.

An example name value:

```yaml
host: 
  - "{{Hostname}}"
```

Nuclei can also do TLS connection to the target server. Just add `tls://` as prefix before the **Hostname** and you're good to go.

```yaml
host:
  - "tls://{{Hostname}}"
```

If a port is specified in the host, the user supplied port is ignored and the template port takes precedence.

#### Matchers / Extractor Parts

Valid `part` values supported by **Network** protocol for Matchers / Extractor are - 
    
| Value            | Description                         |
|------------------|-------------------------------------|
| request          | Network Request                     |
| data             | Final Data Read From Network Socket |
| raw / body / all | All Data received from Socket       |


#### **Example Network Template**

The final example template file for a `hex` encoded input to detect MongoDB running on servers with working matchers is provided below.

```yaml
id: input-expressions-mongodb-detect

info:
  name: Input Expression MongoDB Detection
  author: pd-team
  severity: info
  reference: https://github.com/orleven/Tentacle

tcp:
  - inputs:
      - data: "{{hex_decode('3a000000a741000000000000d40700000000000061646d696e2e24636d640000000000ffffffff130000001069736d6173746572000100000000')}}"
    host:
      - "{{Hostname}}"
    read-size: 2048
    matchers:
      - type: word
        words:
          - "logicalSessionTimeout"
          - "localTime"
```

More complete examples are provided [here](../../template-examples/network.md)