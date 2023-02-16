### 匹配器

匹配器能灵活地对响应体做不同类型的匹配，这块写起来也很简单，还能根据需求添加多个检查来有效地扫描。

#### 类型

一个请求可以指定多个匹配器，基本上有这 6 类：

| 类型   | 匹配部分     |
|--------|--------------|
| status | 整数比较     |
| size   | 内容长度     |
| word   | 协议的一部分 |
| regex  | 协议的一部分 |
| binary | 协议的一部分 |
| dsl    | 协议的一部分 |

匹配响应的状态码的语法如下：

```yaml
matchers:
  # 匹配状态码
  - type: status
    # 期望做匹配的状态码
    status:
      - 200
      - 302
```

匹配返回的二进制内容（用十六进制编码即可）：

```yaml
matchers:
  - type: binary
    binary:
      - "504B0304" # Zip 文件
      - "526172211A070100" # RAR 5.0
      - "FD377A585A0000" # xz tar.xz 包
    condition: or
    part: body
```

也支持用十六进解码后再做匹配：

```yaml
matchers:
  - type: word
    encoding: hex
    words:
      - "50494e47"
    part: body
```

可以根据需求使用 **Word** 和 **Regex**。

**dsl** 能借助辅助函数来构造更加精细、复杂的表达式，并能直接对不同协议的不同字段做取值。

```yaml
matchers:
  - type: dsl
    dsl:
      - "len(body)<1024 && status_code==200" # 返回体大小小于 1024，且状态码为 200
      - "contains(toupper(body), md5(cookie))" # 把返回体中的字母都转成大写的，然后检查 Cookie 的 MD5 值是否包含在其中
```

协议响应的每个部分都能用 DSL 匹配，例如 -

| 响应部分       | 描述                                                            | 示例                   |
|----------------|-----------------------------------------------------------------|------------------------|
| content_length | Content-Length 头                                               | content_length >= 1024 |
| status_code    | 响应状态码                                                      | status_code==200       |
| all_headers    | 完整头                                                          | len(all_headers)       |
| body           | 响应体                                                          | len(body)              |
| 头名字         | 全部小写，其中 `-` 会被转为 `_`，例如 User-Agent，用 user_agent | len(user_agent)        |
| raw            | 完整头 + 响应体                                                 | len(raw)               |


#### 条件组合

一个匹配器中可以指定多个 words 和正则表达式，并用 **AND** 和 **OR** 来组合。

1. **AND** - 只有全都匹配到了，才算匹配成功。
2. **OR** - 只要其中一个匹配到了，才算匹配成功。

#### 匹配部位

响应体的很多部位都可以匹配，如果没有定义就默认匹配 `body`。

使用 AND 条件去匹配 HTTP 响应体的例子：

```yaml
matchers:
  # 匹配 body
  - type: word
   # 一些想要匹配的字符
   words:
     - "[core]"
     - "[config]"
   # 意味着上面的单词必须全部匹配到
   condition: and
   # 欲从 body 中匹配（默认就是 body）
   part: body
```

照葫芦画瓢，你能写个匹配器在响应体中查找任何你想要的内容，创意无限，且随意扩展。

#### 否定式匹配

也支持用否定条件来排除某些项，只用在 **matchers** 中加上 `negative: true`。

一个使用 `negative` 条件的示例——检测返回头中是否包含 `PHPSESSID`：

```yaml
matchers:
  - type: word
    words:
      - "PHPSESSID"
    part: header
    negative: true
```

#### 多个匹配

单个模板也能定义多个匹配器，允许拿到响应数据后，用多个条件去做指纹识别，示例：

```yaml
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
    condition: or
    part: header
```

#### 匹配条件

当使用多匹配时，每个匹配条件之间默认是 OR 条件，可以指定 AND 条件来期望所有条件都满足时返回 true。

```yaml
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
          - "PHP"
        part: body
```
