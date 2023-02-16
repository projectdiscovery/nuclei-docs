## 模板 **预处理**

某些预处理可以在模板任何位置全局指定，一旦模板加载就被执行，用来实现生成随机数之类的东西。

### randstr

!!! abstract "Info"

	每次 Nuclei 运行时都为模板生成一个[随机 ID](https://github.com/rs/xid)，且在模板任何位置上都是相同的值。`randstr` 可以指定一个后缀数，新的随机 ID 将使用它创建。例如 `{{randstr_1}}` 生成的值在模板中将一直不变。

	`randstr` 还支持在 matchers 中匹配输入。

举例：-

```yaml
requests:
  - method: POST
    path:
      - "{{BaseURL}}/level1/application/"
    headers:
      cmd: echo '{{randstr}}'

    matchers:
      - type: word
        words:
          - '{{randstr}}'
```
