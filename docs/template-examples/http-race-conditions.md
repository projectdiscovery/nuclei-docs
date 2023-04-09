### Race condition testing with single POST request.

This template makes a defined POST request in RAW format to `/coupons` endpoint, as the `race_count`is defined as `10`, this will make 10 requests at same time by holding last bytes for all the requests which sent together for all requests synchronizing the send event.

You can also define the matcher as any other template for the expected output which helps to identify if the race condition exploit worked or not.


```yaml
id: race-condition-testing

info:
  name: Race Condition testing
  author: pdteam
  severity: info

http:
  - raw:
      - |
        POST /coupons HTTP/1.1
        Host: {{Hostname}}
        Pragma: no-cache
        Cache-Control: no-cache, no-transform
        User-Agent: Mozilla/5.0 (Windows NT 6.1; Win64; x64; rv:47.0) Gecko/20100101 Firefox/47.0
        Cookie: user_session=42332423342987567896

        promo_code=20OFF        

    race: true
    race_count: 10

    matchers:
      - type: status
        part: header
        status:
          - 200
```

### Race condition testing with multiple requests.

This template makes the defined and multiple POST requests in RAW format with `threads` sets to `5`, `threads` can be utilized in race condition templates when multiple requests needs to be sent to exploit the race condition, `threads` number should be same as the number of you are making with template and not needed if you're only making single request.

```yaml
id: race-condition-testing

info:
  name: Race condition testing with multiple requests
  author: pdteam
  severity: info

http:
  - raw:  
      - |
        POST / HTTP/1.1
        Pragma: no-cache
        Host: {{Hostname}}
        Cache-Control: no-cache, no-transform
        User-Agent: Mozilla/5.0 (Windows NT 6.1; Win64; x64; rv:47.0) Gecko/20100101 Firefox/47.0

        id=1
        
      - |
        POST / HTTP/1.1
        Pragma: no-cache
        Host: {{Hostname}}
        Cache-Control: no-cache, no-transform
        User-Agent: Mozilla/5.0 (Windows NT 6.1; Win64; x64; rv:47.0) Gecko/20100101 Firefox/47.0

        id=2

      - |
        POST / HTTP/1.1
        Pragma: no-cache
        Host: {{Hostname}}
        Cache-Control: no-cache, no-transform
        User-Agent: Mozilla/5.0 (Windows NT 6.1; Win64; x64; rv:47.0) Gecko/20100101 Firefox/47.0

        id=3

      - |
        POST / HTTP/1.1
        Pragma: no-cache
        Host: {{Hostname}}
        Cache-Control: no-cache, no-transform
        User-Agent: Mozilla/5.0 (Windows NT 6.1; Win64; x64; rv:47.0) Gecko/20100101 Firefox/47.0

        id=4

      - |
        POST / HTTP/1.1
        Pragma: no-cache
        Host: {{Hostname}}
        Cache-Control: no-cache, no-transform
        User-Agent: Mozilla/5.0 (Windows NT 6.1; Win64; x64; rv:47.0) Gecko/20100101 Firefox/47.0

        id=5

    threads: 5
    race: true

    matchers:
      - type: status
        status:
          - 200
```
