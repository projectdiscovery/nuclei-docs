### Headless 请求

Nuclei 支持使用简单 DSL 的让浏览器自动化。无头浏览器（Headless）引擎可以完全自定义，允许用户写脚本来操控浏览器，搞出各种独的特自定义工作流程。

```yaml
# 请求从这里开始
headless:
```

#### 动作

动作（Action）是 Nuclei Headless 引擎的单个任务，每个动作都代表着某种操控浏览器的方式。

Nuclei 支持多种动作，下面一一列举。

#### navigate

Navigate 访问指定的 URL，url 字段支持诸如 `{{BaseURL}}`、`{{Hostname}}` 变量。

```yaml
action: navigate
args:
  url: "{{BaseURL}}
```

##### script

Script 在当前页面运行段 JS 代码。最简单的层面上，你只需要在 `code` 参数中提供想要执行的 JS 代码即可，它将在页面上执行。

```yaml
action: script
args:
  code: alert(document.domain)
```

假如你想在 JS 对象上做取值匹配，Nuclei 也能支持。例如，假设应用程序设置了个名为 `window.random-object` 对象的值，并且我们想匹配它的值：

```yaml
- action: script
  args:
    code: window.random-object
  name: script-name
...
matchers:
  - type: word
    part: script-name
    words:
      - "some-value"
```

Nuclei 支持在加载页面之前先运行一段自定义代码，只需使用 `hook` 参数即可，它将在加载任何页面之前运行。

该示例 Hook 了 `window.alert`，以防页面弹出的警报会干扰爬虫工作。

```yaml
- action: script
  args:
    code: (function() { window.alert=function(){} })()
    hook: true
```

这只是其中一个用例，函数挂钩的例子特别多，比如 DOM XSS 检测、基于 Javascript 注入的测试等，示例页面上提供了更多的实例。

##### click

模拟鼠标左键单击指定的页面元素。

```yaml
action: click
args:
  by: xpath
  xpath: /html/body/div[1]/div[3]/form/div[2]/div[1]/div[1]/div/div[2]/input
```

Nuclei 支持多种选择器类型，XPath、Regex、CSS 等等，更多的请参考[这里](#selectors)。

##### rightclick

模拟鼠标右键单击指定的页面元素。

```yaml
action: rightclick
args:
  by: xpath
  xpath: /html/body/div[1]/div[3]/form/div[2]/div[1]/div[1]/div/div[2]/input
```

##### text

对指定的输入框模拟键盘输入。

```yaml
action: text
args:
  by: xpath
  xpath: /html/body/div[1]/div[3]/form/div[2]/div[1]/div[1]/div/div[2]/input
  value: username
```

##### 截图

对页面截图，并保存到磁盘中，支持整页截图和普通屏幕截图。

```yaml
action: screenshot
args:
  to: /root/test/screenshot-web
```

如果需要对完整页面截屏，配置 `fullpage: true` 参数。

```yaml
action: screenshot
args:
  to: /root/test/screenshot-web
  fullpage: true
```

##### time

将时间以 RFC3339 格式输入到输入框中。

```yaml
action: time
args:
  by: xpath
  xpath: /html/body/div[1]/div[3]/form/div[2]/div[1]/div[1]/div/div[2]/input
  value: 2006-01-02T15:04:05Z07:00
```

##### select

对 HTML 的 select 标签做选择。

```yaml
action: select
args:
  by: xpath
  xpath: /html/body/div[1]/div[3]/form/div[2]/div[1]/div[1]/div/div[2]/input
  selected: true
  value: option[value=two]
  selector: regex
```

##### files

将本地文件路径置于页面的文件上传元素中。

```yaml
action: files
args:
  by: xpath
  xpath: /html/body/div[1]/div[3]/form/div[2]/div[1]/div[1]/div/div[2]/input
  value: /root/test/payload.txt
```

##### waitload

等页面加载完后进入空闲状态。

```yaml
action: waitload
```

Nuclei 的 `waitload` 动作等待 DOM 加载，收到 window.onload 事件后空闲等待 1 秒。

##### getresource

返回元素的 src 属性。

```yaml
action: getresource
name: extracted-value-src
args:
  by: xpath
  xpath: /html/body/div[1]/div[3]/form/div[2]/div[1]/div[1]/div/div[2]/input
```

##### extract

提取 HTML 节点的文本或用户指定的属性。

下面的代码将按 XPath 选择器提取文本，然后在匹配器和提取器可以引用 `extracted-value` 进行匹配。

```yaml
action: extract
name: extracted-value
args:
  by: xpath
  xpath: /html/body/div[1]/div[3]/form/div[2]/div[1]/div[1]/div/div[2]/input
```

也可以提取元素的属性，例如 -

```yaml
action: extract
name: extracted-value-href
args:
  by: xpath
  xpath: /html/body/div[1]/div[3]/form/div[2]/div[1]/div[1]/div/div[2]/input
  target: attribute
  attribute: href
```

##### setmethod

覆盖请求的方法。

```yaml
action: setmethod
args:
  part: request
  method: DELETE
```

##### addheader

向请求或响应头中添加字段，但不会覆盖任何已存在的头。

```yaml
action: addheader
args:
  part: response # 也可以是 request
  key: Content-Security-Policy
  value: "default-src * 'unsafe-inline' 'unsafe-eval' data: blob:;"
```

##### setheader

设置请求或响应头。

```yaml
action: setheader
args:
  part: response # 也可以是 request
  key: Content-Security-Policy
  value: "default-src * 'unsafe-inline' 'unsafe-eval' data: blob:;"
```

##### deleteheader

从请求或响应头中删除某个字段。

```yaml
action: deleteheader
args:
  part: response # 也可以是 request
  key: Content-Security-Policy
```

##### setbody

设置请求或响应体内容。

```yaml
action: setbody
args:
  part: response # 也可以是 request
  body: '{"success":"ok"}'
```

##### waitevent

等待页面触发事件。

```yaml
action: waitevent
args:
  event: 'Page.loadEventFired'
```

[这里](https://github.com/go-rod/rod/blob/master/lib/proto/definitions.go)完整地列举了支持的事件。

##### keyboard

模拟按下单个键。

```yaml
action: keyboard
args:
  keys: '\r' # 模拟回车键盘
```

`keys` 参数是一个键码。

##### debug

在每个无头操作之间添加了 5 秒的延迟，并显示了浏览器中发生的所有无头事件的轨迹。

> 注意：它仅用于调试目的，不要在生产模板中使用它。

```yaml
action: debug
```

##### sleep

使浏览器等待指定的时间（单位：秒），调试时也很有用。

```yaml
action: sleep
args:
  duration: 5
```

#### 选择器

选择器指定无头浏览器引擎如何识别要操作的元素，Nuclei 可以包含各种选项来获取选择器 -

| 选择器             | 描述                                    |
|--------------------|-----------------------------------------|
| `r` / `regex`      | 元素匹配 CSS 选择器及文本匹配正则表达式 |
| `x` / `xpath`      | 元素匹配 XPath 选择器                   |
| `js`               | 从一个 JS 函数返回一个元素              |
| `search`           | 搜索查询（适用于文本、XPath、CSS）      |
| `selector`（默认） | 元素匹配 CSS 选择器                     |


#### 匹配 / 提取

匹配器 / 提取器 的 **Headless** 协议支持的有效的值是 -

| 值                | 描述                  |
|-------------------|-----------------------|
| request           | Headless 请求         |
| `<out_names>`     | 具有存储值的动作名字  |
| raw / body / data | 浏览器最终的 DOM 内容 |


#### **实例模板**

以下是一个自动登录 DVWA 的实例 -

```yaml
id: dvwa-headless-automatic-login
info:
  name: DVWA Headless Automatic Login
  author: pdteam
  severity: high
headless:
  - steps:
      - args:
          url: "{{BaseURL}}/login.php"
        action: navigate
      - action: waitload
      - args:
          by: xpath
          xpath: /html/body/div/div[2]/form/fieldset/input
        action: click
      - action: waitload
      - args:
          by: xpath
          value: admin
          xpath: /html/body/div/div[2]/form/fieldset/input
        action: text
      - args:
          by: xpath
          xpath: /html/body/div/div[2]/form/fieldset/input[2]
        action: click
      - action: waitload
      - args:
          by: xpath
          value: password
          xpath: /html/body/div/div[2]/form/fieldset/input[2]
        action: text
      - args:
          by: xpath
          xpath: /html/body/div/div[2]/form/fieldset/p/input
        action: click
      - action: waitload
    matchers:
      - part: resp
        type: word
        words:
          - "You have logged in as"
```

[这里](../../template-examples/headless.md)提供更多的实例。
