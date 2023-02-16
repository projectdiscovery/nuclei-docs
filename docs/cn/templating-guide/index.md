# 模板编写指南

**Nuclei** 用 `YAML` 格式的模板（Template）文件来定义请求方式，以及如何处理返回结果。

`YAML` 是一种简单易读的格式，便于快速编写模板。

**本指南将指导你该如何去编写模板 -**

----------

## 模板说明

每个模板都需要定义个唯一的 ID 作为模板名字，它会出现在打印的扫描结果中。

模板文件以 **.yaml** 为扩展名，你可以用任意文本编辑器来编辑它。

```yaml
id: git-config
```

注意 id 不可包含空格，这样做是为了方便解析输出结果。

### 模板信息

下一个重点是 **info** 块。info 块中定义了 **name**、**author**、**severity**、**description**、**reference**、**tags** 和 `元数据` 字段，**info** 块也能动态地创建其他字段，可以定义多个 `key: value` 格式的字段来丰富模板信息。

**severity** 字段表示严重程度。

**reference** 也是一个常见字段，用来提供一些额外的参考链接。

**tags** 字段允许你根据目的来为模板定义些标签，例如 `cve`、`rce` 等等，运行 Nuclei 时还能按指定的标签来加载模板。

一个完整的 info 模块示例 -

```yaml
info:
  name: Git Config File Detection Template
  author: Ice3man
  severity: medium
  description: Searches for the pattern /.git/config on passed URLs.
  reference: https://www.acunetix.com/vulnerabilities/web/git-repository-found/
  tags: git,config
```

实际的请求和响应处理行为应定义在 info 块下面，指明向目标发出的请求，以及对请求成功的响应如何处理。

每个模板文件中能定义多个请求，然后由引擎逐个发往目标。

模板也能轻而易举地在你的小伙伴或者团队之间分享，他们可以拿着就快速做验证。

#### 元数据（Metadata）

可以定义一些元数据节点，例如，还能与 [uncover](https://github.com/projectdiscovery/uncover) (参考 [集成 Uncover](https://nuclei.projectdiscovery.io/nuclei/get-started/#uncover-integration)) 结合。

元数据节点定义格式：`<engine>-query: '<query>'`，其中：

- `<engine>` 为搜索引擎，等价于 Nuclei 的 `-ue` 或 uncover 的 `-e` 参数。
- `<query>` 为查询关键字，等价于 Nuclei 的 `-uq` 或 uncover 的 `-q` 参数。

以 Shodan 搜索引擎为例：

```
info:
  metadata:
    shodan-query: 'vuln:CVE-2021-26855'
```
