### Basic Template

This template requests `/` path of URL and match string in the response.


```yaml
id: basic-example

info:
  name: Test HTTP Template
  author: pdteam
  severity: info

http:
  - method: GET
    path:
      - "{{BaseURL}}"
    matchers:
      - type: word
        words:
          - "This is test matcher text"
```

### Multiple matchers

This template requests `/` path of URL and run multiple OR based matchers against response.


```yaml
id: http-multiple-matchers

info:
  name: Test HTTP Template
  author: pdteam
  severity: info

http:
  - method: GET
    path:
      - "{{BaseURL}}"

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

This template requests `/` path of URL and runs two matchers, one with AND conditions with string match in header and another matcher against response body.


```yaml
id: matchers-conditions

info:
  name: Test HTTP Template
  author: pdteam
  severity: info

http:
  - method: GET
    path:
      - "{{BaseURL}}"

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

This template requests `/` path of URL and runs two matchers with AND conditions, one with OR conditions with string match in header and another matcher against response body, both condition has to be true in order to match this template.

```yaml
id: multiple-matchers-conditions

info:
  name: Test HTTP Template
  author: pdteam
  severity: info

http:
  - method: GET
    path:
      - "{{BaseURL}}"

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

This template requests `/` path of the URL as GET request with additional custom headers defined in the template.   

```yaml
id: custom-headers

info:
  name: Test HTTP Template
  author: pdteam
  severity: info

http:
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

This template makes POST request to `/admin` endpoint with defined data as body parameter in the template.   



```yaml
id: post-request

info:
  name: Test HTTP Template
  author: pdteam
  severity: info

http:
  - method: POST
    path:
      - "{{BaseURL}}/admin"

    body: 'admin=test'

    matchers:
      - type: word
        words:
          - Welcome Admin
```

### Time based Matcher

This template is example of DSL based duration matcher that returns `true` when the response time matched the defined duration, in this case 6 or more than 6 seconds.

```yaml
id: time-based-matcher

info:
  name: DSL based response time matcher
  author: pdteam
  severity: none

http:
  - raw:
      - |
        GET /slow HTTP/1.1

    matchers:
      - type: dsl
        dsl:
          - 'duration>=6'
```