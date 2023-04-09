### 기본 템플릿

이 템플릿은 RAW 형식의 `/` 경로에 대한 GET 요청을 만들고 응답에 대한 문자열 일치를 확인합니다.


```yaml
id: basic-raw-example
info:
  name: Test RAW Template
  author: pdteam
  severity: info

http:
  - raw:
      - |
        GET / HTTP/1.1
        Host: {{Hostname}}
        Origin: {{BaseURL}}
        Connection: close
        User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_4) AppleWebKit/537.36 (KHTML, like Gecko)
        Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8
        Accept-Language: en-US,en;q=0.9

    matchers:
      - type: word
        words:
          - "Test is test matcher text"
```

### 여러 RAW 요청

이 템플릿은 RAW 형식으로 GET 및 POST 요청을 순차적으로 수행하고 응답에 대한 문자열 일치를 확인합니다.


```yaml
id: multiple-raw-example
info:
  name: Test RAW Template
  author: pdteam
  severity: info

http:
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

    matchers:
      - type: word
        words:
          - "Test is test matcher text"
```