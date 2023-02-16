### 网络请求

Nuclei 可以充当自动化的 **Netcat**，允许用户发送和接收网络数据包，同时提供匹配和提取响应体数据的能力。

网络请求以 **network** 块开始。

```yaml
# 请求从这里开始
network:
```

#### 输入

第一件是定义 **inputs**，指出需要发送的数据，也可以从服务端读取任意数据。

最简单的情况下，只用指定一个字符串，就能通过套接字发出去。

```yaml
# inputs 中列出要发给服务端的数据
inputs:
  - data: "TEST\r\n"
```

你还能发送十六进制的数据，这些数据会先被解码，再发给服务端。

```yaml
inputs:
  - data: "50494e47"
    type: hex
  - data: "\r\n"
```

也能用上辅助函数 -

```yaml
inputs:
  - data: 'hex_decode("50494e47")\r\n'
```

最后是从套接字中读取数据，只需给 `read-size` 指定一个非 0 的数值。你还能对取到的数据命个名，便于后面对其做匹配。

```yaml
inputs:
  - read-size: 8
```

示例，读取一个字节的数据，并做匹配。

```yaml
inputs:
  - read-size: 8
    name: prefix
...
matchers:
  - type: word
    part: prefix
    words:
      - "CAFEBABE"
```

也能将多个读 / 写网络的步骤按顺序排列在一起。

#### Host

接下来是 **host** 字段，这里能使用动态变量，运行时会被替换为主机地址。

1. **Hostname** - 其值为命令行时提供的主机名。

示例:

```yaml
host:
  - "{{Hostname}}"
```

Nuclei 也能用 TLS 连接服务器，只需要加上 `tls://` 作为 **Hostname** 的前缀。

```yaml
host:
  - "tls://{{Hostname}}"
```

如果模板中指定了端口号，那么用户提供的端口信息将会被忽略，优先用模板中定义的。

#### 匹配 / 提取

能做匹配和提取的值有 -

| 值               | 描述                     |
|------------------|--------------------------|
| request          | 网络请求                 |
| data             | 从套接字中读取的数据     |
| raw / body / all | 从套接字中接收的所有数据 |


#### **实例**

最后提供个以十六进制数据做输入的实例，用来识别 MongoDB 服务：

```yaml
id: input-expressions-mongodb-detect

info:
  name: Input Expression MongoDB Detection
  author: pd-team
  severity: info
  reference: https://github.com/orleven/Tentacle

network:
  - inputs:
      - data: "{{hex_decode('3a000000a741000000000000d40700000000000061646d696e2e24636d640000000000ffffffff130000001069736d6173746572000100000000')}}"
    host:
      - "{{Hostname}}"
    read-size: 2048
    matchers:
      - type: word
        words:
          - "logicalSessionTimeout"
          - "localTime"
```

[这里](../../template-examples/network.md)提供了更多的实例。
