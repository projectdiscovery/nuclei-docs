自 [Nuclei v2.3.6](https://github.com/projectdiscovery/nuclei/releases/tag/v2.3.6) 起，Nuclei 支持使用 [interact.sh](https://github.com/projectdiscovery/interactsh) 的 API 来实现基于 OOB 的自动化漏洞扫描，只需在模板中的任意位置中使用 `{{interactsh-url}}` ，并加上 `interact_protocol` 匹配即可，Nuclei 会自己处理它们之间的关联性，使得 OOB 扫描更加轻松。


### Interactsh 占位符

`{{interactsh-url}}` 占位符支持 **http** 和 **network** 请求。

下面的示例展示了 `{{interactsh-url}}` 占位符，在运行时会被自动替换：

```yaml
  - raw:
      - |
        GET /plugins/servlet/oauth/users/icon-uri?consumerUri=https://{{interactsh-url}} HTTP/1.1
        Host: {{Hostname}}
```

### Interactsh 匹配

Interactsh 可以在 matcher 或 extractor 部分中使用 `word`、`regex` 和 `dsl`。

| 部分                |
|---------------------|
| interactsh_protocol |
| interactsh_request  |
| interactsh_response |


!!! abstract "interactsh_protocol"
    值可以是 DNS、HTTP 或 SMTP。这是每个基于 interactsh 的模板的标准匹配器，一般情况下是把 DNS 作为通用值，因为它本质上是非侵入式的。

!!! abstract "interactsh_request"
    interact.sh 服务器收到的请求。

!!! abstract "interactsh_response"
    interact.sh 服务器发给客户端的响应。

示例，Interactsh DNS 匹配：

```yaml
    matchers:
      - type: word
        part: interactsh_protocol # 确认 DNS 交互
        words:
          - "dns"
```

一个基于 HTTP 的实例：

```yaml
matchers-condition: and
matchers:
    - type: word
      part: interactsh_protocol # 确认 HTTP 交互
      words:
        - "http"

    - type: regex
      part: interactsh_request # 确认 /etc/passwd 的内容
      regex:
        - "root:[x*]:0:0:"
```
