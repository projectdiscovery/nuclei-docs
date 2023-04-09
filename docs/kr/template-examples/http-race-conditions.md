### 단일 POST 요청으로 경쟁 조건 테스트.

이 템플릿은 `/coupons` 엔드포인트에 대한 RAW 형식의 정의된 POST 요청을 만듭니다. `race_count`가 `10`으로 정의되므로 모든 요청에 ​​대해 함께 보낸 모든 요청에 ​​대한 마지막 바이트를 보유하여 동시에 10개의 요청을 만듭니다. send 이벤트 동기화를 요청합니다.

또한 경쟁 조건 악용이 작동하는지 여부를 식별하는 데 도움이 되는 예상 출력에 대한 다른 템플릿으로 matcher를 정의할 수 있습니다.


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

### 여러 요청으로 경쟁 조건 테스트.

이 템플릿은 `threads`가 `5`로 설정된 RAW 형식의 정의된 여러 POST 요청을 만듭니다. 경쟁 조건을 악용하기 위해 여러 요청을 보내야 할 때 `threads`를 경쟁 조건 템플릿에서 사용할 수 있습니다. `threads` 번호는 다음과 같아야 합니다. 템플릿으로 만드는 수와 동일해야 하며 단일 요청만 하는 경우에는 필요하지 않습니다.

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
