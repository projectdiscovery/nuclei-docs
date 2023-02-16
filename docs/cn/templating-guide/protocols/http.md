 !!! info "Requests"

    Nuclei 对与 HTTP 相关的各种功能提供了广泛的支持，支持原始请求（Raw requests）、模板化定义请求及 RFC 标准之外（畸形）的请求；还能在原始请求中指定 Payload。本页文档后面会展示更多。

    HTTP 请求始于 `request` 块。

```yaml
# 请求从这里开始
requests:
```

!!! info "请求方法"
    根据需要，请求方法可以是 **GET**、**POST**、**PUT**、**DELETE** 等等。

```yaml
# 定义请求方法
method: GET
```

!!! info "重定向"

    每个模板中都能指定重定向条件，默认情况下不会重定向；如有所需，在 requests 中增加 `redirects: true` 即可。默认情况下最多允许 10 次重定向（防止进入重定向死循环），大多数情况下这个数值应该足够了，也可以设置 `max-redirects` 字段来改变次数限制。

示例：

```yaml
requests:
  - method: GET
    path:
      - "{{BaseURL}}/login.php"
    redirects: true
    max-redirects: 3
```

!!! warning
    重定向选项是作用于整个模板的，而不是某个请求。

!!! info "Path"

    下一块是 **path**，用于指定请求的路径，它会在运行时被动态变量替换掉。

    动态变量都以 `{{` 开头、`}}` 结尾，变量名区分大小写。

    **{{BaseURL}}** - 目标 URL。

    **{{RootURL}}** - URL 的根路径。

    **{{Hostname}}** - 主机名（也包含指定的端口）。

    **{{Host}}** - 主机地址。

    **{{Port}}** - 端口号。

    **{{Path}}** - URI 路径。

    **{{File}}** - 文件名。

    **{{Scheme}}** - 网络协议。

下面提供一个例子 - https://example.com:443/foo/bar.php

| 变量         | 值                                  |
|--------------|-------------------------------------|
| {{BaseURL}}  | https://example.com:443/foo/bar.php |
| {{RootURL}}  | https://example.com:443             |
| {{Hostname}} | example.com:443                     |
| {{Host}}     | example.com                         |
| {{Port}}     | 443                                 |
| {{Path}}     | /foo                                |
| {{File}}     | bar.php                             |
| {{Scheme}}   | https                               |

一些动态变量的实例：

```yaml
path: "{{BaseURL}}/.git/config"
# 在执行时 BaseURL 会被替换为实际的
# 如果 BaseURL 被设置为 https://abc.com 时
# path 的值将会被替换为： https://abc.com/.git/config
```

在一个请求中指定多个路径也是能行的。

#### Headers

也能为请求指定头信息，由键/值组合，如下所示：

```yaml
# headers 包含了请求时的头
headers:
  # 自定义 User-Agent 字段
  User-Agent: Some-Random-User-Agent
  # 自定义 Origin 字段
  Origin: https://google.com
```

#### Body

Body 指定了发送的数据内容，例如：

```yaml
# Body 是随请求一起发出的字符串
body: "{\"some random JSON\"}"

body: "admin=test"
```

#### Session

在模板中指定 `cookie-reuse: true` 就能像浏览器一样在多个请求之间基于 Cookie 来维持会话。如，你希望完成身份验证后，在请求之间需要保持会话才可完成漏洞利用链，这就很有用。

```yaml
# cookie-reuse 接收一个布尔值，默认为 false
cookie-reuse: true
```

#### 请求条件

请求条件允许在多个 HTTP 请求中完成条件检查，以编写复杂的漏洞利用链。

如果基于 DSL 的匹配器/提取器包含了具有相同属性、并以数字结尾的后缀，则自动启用该功能。

举例，`status_code` 指向了当前请求/响应的状态码，在这之前的某次请求的状态码可以用 `_n` 来访问，其中 n 取值为 1~n。假设模板共计发出 4 次请求，而当前处于第 3 次：
- `status_code`: 指向第 3 次（当前）响应的状态码
- `status_code_1` 和 `status_code_2` 指向第 1 次和第 2 次响应的状态码

例如，用上了 `status_code_1`、`status_code_3` 和 `body_2`：

```yaml
    matchers:
      - type: dsl
        dsl:
          - "status_code_1 == 404 && status_code_2 == 200 && contains((body_2), 'secret_string')"
```

注意：请求条件会占用更多的内存，因为需要将前后所有的响应状态属性都保存在内存中的。

#### **HTTP 模板实例**

前面提到过的 `.git/config` 模板最终如下：

```yaml
id: git-config

info:
  name: Git Config File
  author: Ice3man
  severity: medium
  description: Searches for the pattern /.git/config on passed URLs.

requests:
  - method: GET
    path:
      - "{{BaseURL}}/.git/config"
    matchers:
      - type: word
        words:
          - "[core]"
```

### 原始（Raw） HTTP 请求

另一种更加灵活的创建请求方法是使用原始请求，它也能用上 DSL 的辅助函数。如下所示（截至目前，还是建议保留 `Host` 字段，例如下例中设置为 `{{Hostname}}` 变量），所有匹配器、提取器能干的事都能以前面所述方式用在原始请求中。

```yaml
requests:
  - raw:
    - |
        POST /path2/ HTTP/1.1
        Host: {{Hostname}}
        Content-Type: application/x-www-form-urlencoded

        a=test&b=pd
```

可以根据具体的任务来对 requests 略微调整，反正 requests 是完全可以配置的，你为每个发送到服务器的单请求要干什么事做出定义。

原始请求也支持[各类辅助函数](https://nuclei.projectdiscovery.io/templating-guide/helper-functions/)，来看个例子：

```yaml
    - raw:
      - |
        GET /manager/html HTTP/1.1
        Host: {{Hostname}}
        Authorization: Basic {{base64('username:password')}} # 在运行时用辅助函数进行编码
```

若是希望对用户提供的原始 URL 发出请求（而不是在模板中定义一个 URI），那就对 URI 留空，这样就能向用户指定的 URL 发出请求了。

```yaml
    - raw:
      - |
        GET HTTP/1.1
        Host: {{Hostname}}
```

### HTTP 载荷

!!! info
    Nuclei 引擎支持各种类型的载荷，可以用简单的占位符（或用 `{{helper_function(variable)}}`）配合执行 **batteringram**、**pitchfork** 和 **clusterbomb** 的攻击，这些攻击类型需要把字典定义在 Payload 字段中，Nuclei 支持基于文件以及基于模板的字典，也完整支持 DSL 操作。

    Payload 使用变量名定义，并在请求中的 `§ §` 或 `{{ }}` 标记中引用。

一个使用本地字典的示例：

```yaml
    # 基于本地字段的 HTTP 暴力破解

    payloads:
      paths: params.txt
      header: local.txt
```

基于模板的字典示例：

```yaml
    # HTTP 暴力破解，字典定义在模板中

    payloads:
      password:
        - admin
        - guest
        - password
```

**注意：** 攻击类型要谨慎选择，可能会引起模板异常。例如你使用了 `clusterbomb` 或 `pitchfork` 攻击类型，并在 Payload 中只定义了一个变量，模板就没法编译了，因为 `clusterbomb` 或 `pitchfork` 需要在模板中用上多个变量。

#### 攻击模式

Nuclei 引擎内置了多种攻击类型，“batteringram”通常用于 fuzz 单个参数；“clusterbomb”和“pitchfork”用于 fuzz 多个参数，其工作方式与那经典的 Burp intruder 相同。

<table>
  <tr>
    <th>类型</th>
    <td>batteringram</td>
    <td>pitchfork</td>
    <td>clusterbomb</td>

  </tr>
  <tr>
    <th>支持</th>
    <td><svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" width="24" height="24"><path fill-rule="evenodd" d="M1 12C1 5.925 5.925 1 12 1s11 4.925 11 11-4.925 11-11 11S1 18.075 1 12zm16.28-2.72a.75.75 0 00-1.06-1.06l-5.97 5.97-2.47-2.47a.75.75 0 00-1.06 1.06l3 3a.75.75 0 001.06 0l6.5-6.5z"></path></svg>
    </td>
    <td><svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" width="24" height="24"><path fill-rule="evenodd" d="M1 12C1 5.925 5.925 1 12 1s11 4.925 11 11-4.925 11-11 11S1 18.075 1 12zm16.28-2.72a.75.75 0 00-1.06-1.06l-5.97 5.97-2.47-2.47a.75.75 0 00-1.06 1.06l3 3a.75.75 0 001.06 0l6.5-6.5z"></path></svg>
    </td>
    <td><svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" width="24" height="24"><path fill-rule="evenodd" d="M1 12C1 5.925 5.925 1 12 1s11 4.925 11 11-4.925 11-11 11S1 18.075 1 12zm16.28-2.72a.75.75 0 00-1.06-1.06l-5.97 5.97-2.47-2.47a.75.75 0 00-1.06 1.06l3 3a.75.75 0 001.06 0l6.5-6.5z"></path></svg>
    </td>
  </tr>
</table>

!!! abstract "batteringram"
    在所有位置都放置相同的载荷，它在循环遍历载荷时只使用一个载荷值做替换。


!!! abstract "pitchfork"
    为每个位置都使用一组载荷，将第一个载荷值放在第一个位置、第二个载荷值放在第二个位置，依次类推。

    而后遍历所有载荷集，对第一个请求使用第一个有效载荷值、第二个请求使用第二个有效载荷值，依次类推。

!!! abstract "clusterbomb"
    会对所有载荷尝试不同的组合方式，它仍然是把第一个有效载荷放在第一个位置，第二个有效载荷放在第二个位置，但是它是循环遍历完所有载荷、尝试不同的组合方式。

    这种攻击类型尤其适用于暴力破解的场景，第一个有效载荷用于加载用户名，第二个有效载荷用于加载密码字典，最后按不同的组合方式集中轮番轰炸。

    更多信息请参考[这里](https://www.sjoerdlangkemper.nl/2017/08/02/burp-intruder-attack-types/)。

一个使用 `clusterbomb` 的例子：

```yaml
requests:
  - raw:
      - |
        POST /?file={{path}} HTTP/1.1
        User-Agent: {{header}}
        Host: {{Hostname}}

    payloads:
      path: helpers/wordlists/prams.txt
      header: helpers/wordlists/header.txt
    attack: clusterbomb # Defining HTTP fuzz attack type
```

#### 不安全的 HTTP 请求

Nuclei 支持 [rawhttp](https://github.com/projectdiscovery/rawhttp) 来控制和定制完整的请求，允许 **任何畸形的请求**，如 HTTP 走私请求、Host 头注入、畸形 CRLF 字符等等。

**rawhttp** 库默认处于禁用状态，需在模板中指定 `unsafe: true` 来启用它。

一个使用 `rawhttp` 的 HTTP 走私请求实例：

```yaml
requests:
  - raw:
    - |+
        POST / HTTP/1.1
        Host: {{Hostname}}
        Content-Type: application/x-www-form-urlencoded
        Content-Length: 150
        Transfer-Encoding: chunked

        0

        GET /post?postId=5 HTTP/1.1
        User-Agent: a"/><script>alert(1)</script>
        Content-Type: application/x-www-form-urlencoded
        Content-Length: 5

        x=1
    - |+
        GET /post?postId=5 HTTP/1.1
        Host: {{Hostname}}

    unsafe: true # Enables rawhttp client
    matchers:
      - type: dsl
        dsl:
          - 'contains(body, "<script>alert(1)</script>")'
```

### 高级请求

我们对 Nuclei 做了强化，能对网络服务器用更高级的方式扫描，用户能指定多个选项来优化 HTTP 工作流。

#### Pipelining

HTTP 管线化（HTTP Pipelining）支持将多个 HTTP 请求发送到同一网络连接上，灵感源于[http-desync-attacks-request-smuggling-reborn](https://portswigger.net/research/http-desync-attacks-request-smuggling-reborn).

需要确保服务端能支持 HTTP 管线化，否则 Nuclei 还是会退回到标准的 HTTP 请求引擎。

如果你想确认提供的域名列表中是否支持 HTTP 管线化，可以用 [httpx](https://github.com/projectdiscovery/) 的 `-pipeline` 参数探测。

一个管线化的配置实例：

```yaml
    unsafe: true
    pipeline: true
    pipeline-concurrent-connections: 40
    pipeline-requests-per-connection: 25000
```

一个管线化的实例 -

```yaml
id: pipeline-testing
info:
  name: pipeline testing
  author: pdteam
  severity: info

requests:
  - raw:
      - |+
        GET /{{path}} HTTP/1.1
        Host: {{Hostname}}
        Referer: {{BaseURL}}

    attack: batteringram
    payloads:
      path: path_wordlist.txt

    unsafe: true
    pipeline: true
    pipeline-concurrent-connections: 40
    pipeline-requests-per-connection: 25000

    matchers:
      - type: status
        part: header
        status:
          - 200
```

#### 连接池

虽然早期的 Nuclei 不用连接池，但现在用户可以决定是否开启连接池了，根据需要来做快速扫描。

要启用连接池，可以用 `threads` 属性定义线程数。

不要在启用了连接池的模板中设置 `Connection: Close` 字段，否则会让引擎退回到标准的 HTTP 请求池。

一个使用 HTTP 连接池的实例 -

```yaml
id: fuzzing-example
info:
  name: Connection pooling example
  author: pdteam
  severity: info

requests:

  - raw:
      - |
        GET /protected HTTP/1.1
        Host: {{Hostname}}
        Authorization: Basic {{base64('admin:§password§')}}

    attack: batteringram
    payloads:
      password: password.txt
    threads: 40

    matchers-condition: and
    matchers:
      - type: status
        status:
          - 200

      - type: word
        words:
          - "Unique string"
        part: body
```

#### HTTP 走私

HTTP 走私是在 [Portswigger’s Research](https://portswigger.net/research/http-desync-attacks-request-smuggling-reborn) 最近讨论的主题中开始流行起来的一类 Web 攻击，如需深入学习，请参考原文。

在开源领域中，要检测 HTTP 走私是很困难的，尤其是请求本身是畸形的。Nuclei 可以依靠 [rawhttp](https://github.com/projectdiscovery/rawhttp) 引擎来可靠地检测出 HTTP 走私漏洞。

HTTP 走私的最基本例子是 CL.TE 走私，本例提供一个 CL.TE 走私漏洞检测的模板，设置 `unsafe: true` 属性来指定基于 rawhttp 引擎的请求。

```yaml
id: CL-TE-http-smuggling

info:
  name: HTTP request smuggling, basic CL.TE vulnerability
  author: pdteam
  severity: info
  reference: https://portswigger.net/web-security/request-smuggling/lab-basic-cl-te

requests:
  - raw:
    - |+
      POST / HTTP/1.1
      Host: {{Hostname}}
      Connection: keep-alive
      Content-Type: application/x-www-form-urlencoded
      Content-Length: 6
      Transfer-Encoding: chunked

      0

      G
    - |+
      POST / HTTP/1.1
      Host: {{Hostname}}
      Connection: keep-alive
      Content-Type: application/x-www-form-urlencoded
      Content-Length: 6
      Transfer-Encoding: chunked

      0

      G

    unsafe: true
    matchers:
      - type: word
        words:
          - 'Unrecognized method GPOST'
```

更多的例子请见[实例模板](https://nuclei.projectdiscovery.io/template-examples/http-smuggling/)。

#### 竞态条件

竞态条件是另一个在传统的安全工具中不能自动化能检测出来的问题，Burp Suite 为 Turbo Intruder 引入了一种名为 Gate 的机制，其中所有请求的所有字节都被发出去，但除了最后一个字节，它只为同步发送事件的所有请求一起发送。

我们也在 Nuclei 引擎中实现了 **Gate** 机制，并能在模板中使用，这让针对竞态条件的检测变得简单。

把 `race` 属性设为 `true`，以及 `race_count` 属性定义要发出请求的数量即可。

以下实例将相同的请求使用 Gate 重复了 10 次：

```yaml
id: race-condition-testing

info:
  name: Race condition testing
  author: pdteam
  severity: info

requests:
  - raw:
      - |
        POST /coupons HTTP/1.1
        Host: {{Hostname}}

        promo_code=20OFF

    race: true
    race_count: 10

    matchers:
      - type: status
        part: header
        status:
          - 200
```

你也能简单地将 `POST` 替换为其他类型的请求，并根据需要修改 `race_count` 即可。

```bash
nuclei -t race.yaml -target https://api.target.com
```

**多请求竞争条件测试**

对于要发送多个请求以利用竞争条件的场景，我们可以使用多线程。

```yaml
    threads: 5
    race: true
```

`threads` 是指定要做执行竞争条件测试的请求总数。

以下实例，使用了 Gate 的逻辑同时发送了 5 次请求：

```yaml
id: multi-request-race

info:
  name: Race condition testing with multiple requests
  author: pd-team
  severity: info

requests:
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
```

### 请求注解

内联注解允许对每个请求的属性/行为进行覆盖，与 Python 或 Java 的注解非常相似，必须放在 RFC 行之前，目前支持以下注解：

- `@Host:` 覆盖最终请求的目标 Host（通常是输入的域名或 IP），它支持带有 IP 或域名、端口和协议，例如：`domain.tld`、`domain.tld:port`、`http://domain.tld:port`
- `@tls-sni:` 覆盖 TLS 请求中的 SNI 名称（通常是输入的主机名），它支持任何文字，特殊值 `request.host` 使用了 `Host` 的值
- `@timeout:` 将请求的超时覆盖为自定义持续时间。它支持格式化为字符串的时间，如未指定持续时间，则使用默认的 Timeout 值。

示例，在请求中的使用注释：

```yaml
- |
  @Host: https://projectdiscovery.io:443
  POST / HTTP/1.1
  Pragma: no-cache
  Host: {{Hostname}}
  Cache-Control: no-cache, no-transform
  User-Agent: Mozilla/5.0 (Windows NT 6.1; Win64; x64; rv:47.0) Gecko/20100101 Firefox/47.0
```

这特别有用，比如在具有多个请求的模板的情况中，在初始请求后，需要在特定目标主机请求次（比如检查 API 有效性）：

```yaml
requests:
  - raw:
      # 给 {{Hostname}} 发送请求来获得 Token
      - |
        GET /getkey HTTP/1.1
        Host: {{Hostname}}

      # 请求将被发送到 https://api.target.com:443 去验证 Token 的有效性
      - |
        @Host: https://api.target.com:443
        GET /api/key={{token} HTTP/1.1
        Host: api.target.com:443

    extractors:
      - type: regex
        name: token
        part: body
        regex:
          # 随机提取 prefix 和 suffix 之间的字符串
          - 'prefix(.*)suffix'

    matchers:
      - type: word
        part: body
        words:
          - valid token
```

自定义 `timeout` 注解 -

```yaml
- |
  @timeout: 25s
  POST /conf_mail.php HTTP/1.1
  Host: {{Hostname}}
  Content-Type: application/x-www-form-urlencoded

  mail_address=%3B{{cmd}}%3B&button=%83%81%81%5B%83%8B%91%97%90M
```
