### Workflows

Workflows allow users to define an execution sequence for templates. The templates will be run on the defined conditions. These are the most efficient way to use nuclei, where all the templates are configured based on needs of users. This means, you can create Technology Based / Target based workflows, like WordPress Workflow, Jira Workflow which only run when the specific technology is detected.

All templates part of a workflow share a common execution context, hence the named extractors from a template are accessible to other templates just by referring to it with its name.

If the tech stack is known, we recommend creating your custom workflows to run your scans. This leads to much lower scan times with better results.

Workflows can be defined with `workflows` attribute, following the `template` / `subtemplates` and `tags` to execute.

```yaml
workflows:
  - template: technologies/template-to-execute.yaml
```

**Type of workflows**

1. Generic workflows
2. Conditional workflows

#### Generic Workflows

In generic workflow one can define single or multiple template to be executed from a single workflow file. It supports both files and directories as input.

A workflow that runs all config related templates on the list of give URLs.

```yaml
workflows:
  - template: files/git-config.yaml
  - template: files/svn-config.yaml
  - template: files/env-file.yaml
  - template: files/backup-files.yaml
  - tags: xss,ssrf,cve,lfi
```

A workflow that runs specific list of checks defined for your project.

```yaml
workflows:
  - template: cves/
  - template: exposed-tokens/
  - template: exposures/
  - tags: exposures
```

#### Conditional Workflows

You can also create conditional templates which execute after matching the condition from a previous template. This is mostly useful for vulnerability detection and exploitation as well as tech based detection and exploitation. Use-cases for this kind of workflows are vast and varied.

**Templates based condition check**

A workflow that executes subtemplates when base template gets matched.

```yaml
workflows:
  - template: technologies/jira-detect.yaml
    subtemplates:
      - tags: jira
      - template: exploits/jira/
```

**Matcher Name based condition check**

A workflow that executes subtemplates when a matcher of base template is found in result.

```yaml
workflows:
  - template: technologies/tech-detect.yaml
    matchers:
      - name: vbulletin
        subtemplates:
          - template: exploits/vbulletin-exp1.yaml
          - template: exploits/vbulletin-exp2.yaml
      - name: jboss
        subtemplates:
          - template: exploits/jboss-exp1.yaml
          - template: exploits/jboss-exp2.yaml
```

In similar manner, one can create as many and as nested checks for workflows as needed.

**Subtemplate and matcher name based multi level conditional check**

A workflow showcasing chain of template executions that run only if the previous templates get matched.


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

Conditional workflows are great examples of performing checks and vulnerability detection in most efficient manner instead of spraying all the templates on all the targets and generally come with good ROI on your time and is gentle for the targets as well.

#### Shared Execution Context

Nuclei engine supports transparent workflow cookiejar and key-value sharing across templates parts of a same workflow. Here follow an example of a workflow that extract a value from the first template and use it in the second conditional one:

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

For example, the following templates extract `href` links from a target web page body and make the value available under the `extracted` key:

```yaml
# template-with-named-extractor.yaml

id: value-sharing-template1

info:
  name: value-sharing-template1
  author: pdteam
  severity: info

http:
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

Finally the second template in the workflow will use the obtained value by referencing the extractor name (`extracted`):

```yaml
# template-using-named-extractor.yaml

id: value-sharing-template2

info:
  name: value-sharing-template2
  author: pdteam
  severity: info

http:
  - raw:
      - |
        GET /path2 HTTP/1.1
        Host: {{Hostname}}
        
        {{extracted}}
```

More complete workflow examples are provided [here](../template-examples/workflow.md)