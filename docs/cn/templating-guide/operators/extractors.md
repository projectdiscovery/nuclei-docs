### 提取器

提取器用于从响应体中提取匹配结果，在 Nuclei 扫描结果上展示出来。

#### 类型

一个请求中可以指定多个提取器，目前支持的有：

1. **regex** - 用正则表达式从响应体中提取数据
2. **kval** - 从响应头或 Cookie 中提取 `key: value` / `key=value` 格式的数据
3. **json** - 从 JSON 中提取数据，类似 jq 的语法
4. **xpath** - 基于 XPath 从 HTML 中提取数据
4. **dsl** - 基于 DSL 表示式提取数据

#### 正则表达式提取器

示例，用 **regex** 从 HTTP 响应体中提取数据 -

```yaml
extractors:
  - type: regex # 提取器类型
    part: body  # 提取的部位（值可以是 header、body 或者 all）
    regex:
      - "(A3T[A-Z0-9]|AKIA|AGPA|AROA|AIPA|ANPA|ANVA|ASIA)[A-Z0-9]{16}"  # 匹配所用的正则表达式
```

#### Kval 提取器

示例，从 HTTP 响应头中提取 `content-type` 数据：

```yaml
extractors:
  - type: kval # 提取器类型
    kval:
      - content_type
```

注意，`content-type` 已经被换成 `content_type`，因为 **kval** 提取器不支持破折号（`-`），改用下划线（`_`）代替。

#### JSON 提取器

示例，用 **json** 提取器从 JSON 块中取 `id` 对象。

```yaml
      - type: json # 提取器类型
        part: body
        name: user
        json:
          - '.[] | .id'  # 类似 jq 的语法
```

有关 jq 的文档请参考 - https://github.com/stedolan/jq

#### Xpath 提取器

一个使用 **xpath** 提取 HTML 响应体中 `href` 属性的示例：

```yaml
extractors:
  - type: xpath # 提取器类型
    attribute: href # 需要提取的属性
    xpath:
      - '/html/body/div/p[2]/a' # XPath 表达式
```

在[浏览器中进行简单的复制粘贴](https://www.scientecheasy.com/2020/07/find-xpath-chrome.html/)，就能从任何网页内容中获取 XPath。

#### DSL 提取器

示例，用 DSL 获取 `body` 的长度：

```yaml
extractors:
  - type: dsl  # 提取器类型
    dsl:
      - len(body) # DSL 表达式
```

#### 动态提取器

提取器也能在具有多条请求的模板中捕获运行时的动态值，比如 CSRF Token、Session 等。此功能仅仅适合用在原始（RAW）请求。

示例，定义一个名为 `api` 的提取器，它用正则表达式从请求中捕获。

```yaml
    extractors:
      - type: regex
        name: api
        part: body
        internal: true # 需要使用动态变量
        regex:
          - "(?m)[0-9]{3,10}\\.[0-9]+"
```

提取出来的值会保存在 **api** 变量中，这个变量可以在模板后面的任何地方中引用。

如果你想把提取出来的东西赋值在一个动态变量中，就必须指定 `internal: true`，这样提取的数据就不会在终端中打印出来了（而是赋值给变量了）。

**match-group** 选项可以为正则表达式指定分组，用来做更复杂的匹配。

```yaml
extractors:
  - type: regex  # 匹配类型
    name: csrf_token # 定义一个变量
    part: body # 从返回体中查找
    # 使用正则表达式的分组匹配
    # match 数组保存了分组匹配后的结果
    # match[0] 是匹配的完整内容
    # match[n] 是第 1 个分组的内容，大多数我们都是想要第 1 个分组的内容，如下所示
    group: 1
    regex:
      - '<input\sname="csrf_token"\stype="hidden"\svalue="([[:alnum:]]{16})"\s/>'
```

上述名为 `csrf_token` 的提取器将会把 `([[:alnum:]]{16})` 匹配的值保存下来，例如值为 `abcdefgh12345678`。

若未指定分组，将保存完整的匹配结果（通过 `<input name="csrf_token"\stype="hidden"\svalue="([[:alnum:]]{16})" />` 提取出的 `<input name="csrf_token" type="hidden" value="abcdefgh12345678" />`）。
