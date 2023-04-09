### Variables (변수)

variable을 사용하여 템플릿 전체에서 일정하게 유지되는 일부 값을 선언할 수 있습니다. 한 번 계산된 variable의 값은 변경되지 않습니다. variable은 단순 문자열 또는 DSL helper function 일 수 있습니다. variable이 helper function인 경우, 이중 중괄호 `{{<expression>}}`로 묶입니다. variable은 템플릿 수준에서 선언됩니다.

예시 variables - 

```yaml
variables:
  a1: "test" # A string variable
  a2: "{{to_lower(rand_base(5))}}" # A DSL function variable
```

현재 `dns`, `http`, `headless` 및 `network` 프로토콜은 variable을 지원합니다. 

variable을 사용한 템플릿 예시 -

```yaml
# Variable example using HTTP requests
id: variables-example

info:
  name: Variables Example
  author: pdteam
  severity: info

variables:
  a1: "value"
  a2: "{{base64('hello')}}"

http:
  - raw:
      - |
        GET / HTTP/1.1
        Host: {{FQDN}}
        Test: {{a1}}
        Another: {{a2}}
    stop-at-first-match: true
    matchers-condition: or
    matchers:
      - type: word
        words: 
          - "value"
          - "aGVsbG8="
```

```yaml
# Variable example for network requests
id: variables-example

info:
  name: Variables Example
  author: pdteam
  severity: info

variables:
  a1: "PING"
  a2: "{{base64('hello')}}"

tcp:
  - host: 
      - "{{Hostname}}"
    inputs:
      - data: "{{a1}}"
    read-size: 8
    matchers:
      - type: word
        part: data
        words:
          - "{{a2}}"
```