### Basic Template

```yaml
id: basic-example

info:
  name: Test HTTP Template
  author: pdteam
  severity: info

requests:
  - method: GET
    path:
      - "{{BaseURL}}/"
    matchers:
      - type: word
        words:
          - "This is test matcher text"
```

### Multiple matchers

```yaml
id: http-multiple-matchers

info:
  name: Test HTTP Template
  author: pdteam
  severity: info

requests:
  - method: GET
    path:
      - "{{BaseURL}}/"
    matchers:
      - type: word
        name: php
        words:
          - "X-Powered-By: PHP"
          - "PHPSESSID"
        part: header

      - type: word
        name: node
        words:
          - "Server: NodeJS"
          - "X-Powered-By: nodejs"
        condition: or
        part: header

      - type: word
        name: python
        words:
          - "Python/2."
          - "Python/3."
        part: header
```

### Matchers with conditions

```yaml
id: matchers-conditions

info:
  name: Test HTTP Template
  author: pdteam
  severity: info

requests:
  - method: GET
    path:
      - "{{BaseURL}}/"

    matchers:
      - type: word
        words:
          - "X-Powered-By: PHP"
          - "PHPSESSID"
        condition: and
        part: header

      - type: word
        words:
          - "PHP"
        part: body
```

### Multiple matcher conditions

```yaml
id: multiple-matchers-conditions

info:
  name: Test HTTP Template
  author: pdteam
  severity: info

requests:
  - method: GET
    path:
      - "{{BaseURL}}/"

    matchers-condition: and
    matchers:

      - type: word
        words:
          - "X-Powered-By: PHP"
          - "PHPSESSID"
        condition: or
        part: header

      - type: word
        words:
          - PHP
        part: body
```

### Custom headers

```yaml
id: custom-headers

info:
  name: Test HTTP Template
  author: pdteam
  severity: info

requests:
  - method: GET

    # Example of sending some headers to the servers

    headers:

      X-Client-IP: 127.0.0.1
      X-Remote-IP: 127.0.0.1
      X-Remote-Addr: 127.0.0.1
      X-Forwarded-For: 127.0.0.1
      X-Originating-IP: 127.0.0.1

    path:
      - "{{BaseURL}}/server-status"

    matchers:
      - type: word
        words:
          - Apache Server Status
          - Server Version
        condition: and
```

### POST requests

```yaml
id: post-request

info:
  name: Test HTTP Template
  author: pdteam
  severity: info

requests:
  - method: POST
    path:
      - "{{BaseURL}}/admin"

    body: 'admin=test'

    matchers:
      - type: word
        words:
          - Welcome Admin
```