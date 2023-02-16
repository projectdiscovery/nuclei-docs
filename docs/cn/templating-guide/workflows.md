### 工作流

工作流允许用户定义模板的执行顺序，这些模板将按定义的顺行运行，这是使用 Nuclei 最佳的方式——所有的模板都是根据用户需求来配置的。这意味着，你可以基于技术或基于目标来创建工作流，比如针对 WordPress 创建一个工作流，亦或是创建个针对 Jira 的工作流。

工作流中定义的所有模板都共享一个上下文环境，因此其他模板中只需用引用名称就能访问到。

对于已知的技术栈，我们推荐你创建自定义的工作流来运行你的扫描，不仅能缩短扫描时间，还能获得更好的结果。

用 `workflows` 来定义工作流，后面能紧随 `template`、`subtemplates` 和 `tags`：

```yaml
workflows:
  - template: technologies/template-to-execute.yaml
```

**工作流类型**

1、通用工作流

2、条件工作流

#### 通用工作流

在通用工作流中，一个工作流文件中能定义至少一个要被加载的模板文件，或是保存了模板文件的文件夹。

运行一个工作流，就能把配置的所有模板都用到指定的 URL 上，例如：

```yaml
workflows:
  - template: files/git-config.yaml
  - template: files/svn-config.yaml
  - template: files/env-file.yaml
  - template: files/backup-files.yaml
  - tags: xss,ssrf,cve,lfi
```

你也能从项目的角度来定义工作流，例如：

```yaml
workflows:
  - template: cves/
  - template: exposed-tokens/
  - template: exposures/
  - tags: exposures
```

#### 条件工作流

你也能创建条件模板，只有当作为先决条件的模板都匹配成功后才会继续运行下一个，这种思路主要在漏洞检测 + 漏洞利用的场景上。当然场景是多种多样的。

**基于条件检查的模板**

只有当基本模板匹配成功后，才执行后面的子模板。下例，仅当识别出 Jira 后，才会进一步执行 exploits/jira/ 目录下的模板：

```yaml
workflows:
  - template: technologies/jira-detect.yaml
    subtemplates:
      - tags: jira
      - template: exploits/jira/
```

**基于名字检查的条件模板**

只有当基本模板成功提取了名字后，才执行后面的子模板：

```yaml
workflows:
  - template: technologies/tech-detect.yaml
    matchers:
      - name: vbulletin # 只有识别出 vbulletin 指纹后，才进一步执行后续的子模板
        subtemplates:
          - template: exploits/vbulletin-exp1.yaml
          - template: exploits/vbulletin-exp2.yaml
      - name: jboss # 只有识别出 jboss 指纹后，才进一步执行后续的子模板
        subtemplates:
          - template: exploits/jboss-exp1.yaml
          - template: exploits/jboss-exp2.yaml
```

可照葫芦画瓢，根据具体的需求来创建更多的嵌套检测。

**基于子模板匹配后的多级检测**

这里展示了工作流中的模板执行链，仅当前一个模板匹配成功后才运行：

```yaml
workflows:
  - template: technologies/tech-detect.yaml
    matchers:
      - name: lotus-domino
        subtemplates:
          - template: technologies/lotus-domino-version.yaml
            subtemplates:
              - template: cves/xx-yy-zz.yaml
                subtemplates:
                  - template: cves/xx-xx-xx.yaml
```

条件工作流是个不错的例子，以最有效的方式去执行验证和漏洞检查，而不是一股脑地把所有模板都往目标上怼，这样对目标来说也比较温柔；从时间角度上来说，回报率也不错。

#### 模板的上下文共享

Nuclei 引擎支持透明工作流，cookiejar 和键值跨模板共享，下例中，第二个模板的变量值取自于第一个模板：

```yaml
id: key-value-sharing-example
info:
  name: Key Value Sharing Example
  author: pdteam
  severity: info

workflows:
  - template: template-with-named-extractor.yaml
    subtemplates:
      - template: template-using-named-extractor.yaml
```

template-with-named-extractor.yaml 从页面源码中提取 `href` 的值，并赋值给变量 `extracted`：

```yaml
# template-with-named-extractor.yaml

id: value-sharing-template1

info:
  name: value-sharing-template1
  author: pdteam
  severity: info

requests:
  - path:
      - "{{BaseURL}}/path1"
    extractors:
      - type: regex
        part: body
        name: extracted
        regex:
          - 'href="(.*)"'
        group: 1
```

第二个模板 template-using-named-extractor.yaml 能直接引用变量 `extracted`：

```yaml
# template-using-named-extractor.yaml

id: value-sharing-template2

info:
  name: value-sharing-template2
  author: pdteam
  severity: info

requests:
  - raw:
      - |
        GET /path2 HTTP/1.1
        Host: {{Hostname}}

        {{extracted}}
```

[这里](../template-examples/workflow.md)提供了更完整的实例。
