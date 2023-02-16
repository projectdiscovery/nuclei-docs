### DNS 请求

Nuclei 可以轻松模拟 DNS 协议，发出自定义 DNS 请求到 DNS 服务器上，而后对服务器的响应内容做匹配或提取。

DNS 请求起始于 **dns** 块。

```yaml
# 模板中这里开始定义 DNS 请求
dns:
```

#### Type 字段

第一件事是通过 **type** 指定请求类型，可以是 **A**、**NS**、**CNAME**、**SOA**、**PTR**、**MX**、**TXT** 和 **AAAA**。

```yaml
# 定义请求类型
type: A
```

#### Name 字段

下一部分是要配置 DNS 解析的 **name**，可以设置为动态变量，这样在运行时就会被替换掉，变量以 `{{` 开头、`}}` 结尾。

1. **FQDN** - 变量在运行时会替换为目标的主机名 / FQDN。

示例：

```yaml
name: {{FQDN}}.com
# 这个值在执行时会用 FQDN 替换
# 当 FQDN 是 https://this.is.an.example 时
# name 将被替换为 this.is.an.example.com
```

目前只支持每次请求用一个名称。

#### Class 字段

值可以是 **INET**、**CSNET**、**CHAOS**、**HESIOD**、**NONE** 和 **ANY**。通常用 **INET** 就足够了。

```yaml
# 定义 class
class: inet
```

#### 递归查询

递归是一个布尔值，决定是否需要递归整个 DNS 树来检索新的结果。通常最好将其保留为 true 就好。

```yaml
# 递归查询
recursion: true
```

#### 重试

放弃查询之前的重试次数，推荐把 **3** 作为一个合理的数值。

```yaml
# 设置重试次数
retries: 3
```

#### 匹配 / 提取

**DNS** 协议支持的匹配 / 提取的有效值 -

| 值               | 描述                       |
|------------------|----------------------------|
| request          | DNS Request                |
| rcode            | DNS Rcode                  |
| question         | DNS Question 消息          |
| extra            | DNS Message Extra 字段     |
| answer           | DNS Message Answer 字段    |
| ns               | DNS Message Authority 字段 |
| raw / all / body | 原始的 DNS 消息            |


#### **实例**

最后一个实例，用 `A` 记录查询，并检查响应中是否包含 CNAME 和 A 记录：

```yaml
id: dummy-cname-a

info:
  name: Dummy A dns request
  author: mzack9999
  severity: none
  description: Checks if CNAME and A record is returned.

dns:
  - name: "{{FQDN}}"
    type: A
    class: inet
    recursion: true
    retries: 3
    matchers:
      - type: word
        words:
          # The response must contain a CNAME record
          - "IN\tCNAME"
          # and also at least 1 A record
          - "IN\tA"
        condition: and
```

[这里](../../template-examples/dns.md)提供了更多的实例。
