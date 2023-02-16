### HTTP 模糊测试

Nuclei 支持基于 `fuzzing` 定义规则的 HTTP 模糊测试，这允许为通用 Web 应用漏洞创建检测模板，例如 SQL 注入、SSRF、命令注入等等，而不需要目标的任何信息，类似于经典的 Web 扫描器。

#### Part

Part 指定对哪一部分进行基于规则的模糊测试，可选的参数是 -

1. **query** (`默认`) - 对 URL 参数模糊测试

```yaml
fuzzing:
  - part: query # 对 URL 参数模糊测试
```

即将添加对 `路径`、`HTTP 头`、`HTTP 主体`、`Cookie` 等部分的支持。

#### Type

Type 指定为模糊测试的规则值做替换的类型，可用的参数有 -

1. **replace** (`默认`) - 用 Payload 替换参数的值
2. **prefix** - 在已有值前加上 Payload
3. **postfix** - 在已有值的后面追加 Pyaload
4. **infix** - 用有 Payload 的中缀值（放在两者之间）

```yaml
fuzzing:
  - part: query
    type: postfix # 在参数后面追加 Payload
```

#### Mode

指定可替换的模式，可用的模式有 -

1. **multiple** (`默认`) - 一次替换全部值
2. **single** - 一次替换一个值

```yaml
fuzzing:
  - part: query
    type: postfix
    mode: multiple # Fuzz query postfixing payloads to all parameters at once
```

> **注意**: 当没有设置时，用默认选项。

#### Filters

支持多个过滤器，可将模糊测试的范围限制在感兴趣的参数和值上，Nuclei HTTP 模糊测试引擎将请求部分转换为键和值，然后通过相关的选项进行过滤。

支持以下过滤字段 -

1. **keys** - 用来模糊测试的参数名称列表（完全匹配）
2. **keys-regex** - 用来模糊测试的参数名称列表（正则匹配）
3. **values** - 用来模糊测试的值的正则表达式列表

这些过滤器都可以组合应用，根据参数输入去运行高针对性的模糊测试。下面提供几个过滤的示例。

```yaml
# 基于参数名称的命令注入测试
fuzzing:
  - part: query
    type: replace
    mode: single
    keys:
      - "daemon"
      - "upload"
      - "dir"
      - "execute"
      - "download"
      - "log"
      - "ip"
      - "cli"
      - "cmd"
```

```yaml
# 基于参数名称正则的重定向测试
fuzzing:
  - part: query
    type: replace
    mode: single
    keys-regex:
      - "redirect.*"
```

```yaml
# 基于正则匹配参数值的 SSRF 模糊测试
fuzzing:
  - part: query
    type: replace
    mode: single
    values:
      - "https?://.*"
```

#### Fuzz

Fuzz 指定参数 `type` 替换值，它支持 Payload、DSL 函数等等，并允许用户充分利用 Nuclei 现有的核心功能进行模糊测试。

```yaml
# XSS Fuzzing，匹配即停止
payloads:
  reflection:
    - "6842'\"><9967"
stop-at-first-match: true
fuzzing:
  - part: query
    type: postfix
    mode: single
    fuzz:
      - "{{reflection}}"
```

```yaml
# 使用 interactsh-url 做 OOB 测试
payloads:
  redirect:
    - "{{interactsh-url}}"
fuzzing:
  - part: query
    type: replace
    mode: single
    keys:
      - "dest"
      - "redirect"
      - "uri"
    fuzz:
      - "https://{{redirect}}"
```

```yaml
# 使用模板级变量做 SSTI 测试
variables:
  first: "{{rand_int(10000, 99999)}}"
  second: "{{rand_int(10000, 99999)}}"
  result: "{{to_number(first)*to_number(second)}}"

requests:
    ...
    payloads:
      reflection:
        - '{{concat("{{", "§first§*§second§", "}}")}}'
    fuzzing:
      - part: query
        type: postfix
        mode: multiple
        fuzz:
          - "{{reflection}}"
```

#### 示例模板

下面提供了一个用于模糊测试 XSS 漏洞的示例模板。

```yaml
id: fuzz-reflection-xss

info:
  name: Basic Reflection Potential XSS Detection
  author: pdteam
  severity: low

requests:
  - method: GET
    path:
      - "{{BaseURL}}"
    payloads:
      reflection:
        - "6842'\"><9967"
    stop-at-first-match: true
    fuzzing:
      - part: query
        type: postfix
        mode: single
        fuzz:
          - "{{reflection}}"
    matchers-condition: and
    matchers:
      - type: word
        part: body
        words:
          - "{{reflection}}"
      - type: word
        part: header
        words:
          - "text/html"
```

[这里](../../template-examples/http-fuzzing.md)提供了更多的参考实例。
