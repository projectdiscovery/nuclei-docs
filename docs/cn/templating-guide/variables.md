### 变量

可以在模板中自己定义一些变量，变量的值在整个模板中都会保持不变，值可以是简单的字符串，或是 DSL 函数；如果是函数则需要用两个花括号包围 `{{<expression>}}`。变量在模板级中定义。

示例 -

```yaml
variables:
  a1: "test" # 字符串变量
  a2: "{{to_lower(rand_base(5))}}" # DSL 函数
```

目前 `dns`、`http`、`headless` 和 `network` 协议都支持定义变量。

在模板中使用变量的例子 -

```yaml
# 例，在 HTTP 请求中定义变量
id: variables-example

info:
  name: Variables Example
  author: pdteam
  severity: info

variables:
  a1: "value"
  a2: "{{base64('hello')}}"

requests:
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
# 例，在网络请求中定义变量
id: variables-example

info:
  name: Variables Example
  author: pdteam
  severity: info

variables:
  a1: "PING"
  a2: "{{base64('hello')}}"

network:
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
