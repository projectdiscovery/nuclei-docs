### Workflows (작업흐름)

workflow를 통해 사용자는 템플릿의 실행 순서를 정의할 수 있습니다. 템플릿은 정의된 조건에서 실행됩니다. 모든 템플릿을 사용자의 요구에 따라 구성하는 방법이 nuclei를 사용하는 가장 효율적인 방법입니다. 즉, 특정 기술이 감지될 때만 실행되는 WordPress Workflow, Jira Workflow와 같은 기술 기반/대상 기반 workflow를 만들 수 있습니다.

기술 스택이 알려진 경우 스캔을 실행하기 위한 사용자 지정 workflow를 만드는 것이 좋습니다. 이 방법을 통해 더 나은 결과와 훨씬 적은 스캔 시간을 얻을 수 있습니다.

workflow는 실행할 `template` / `subtemplates` 및 `tags`를 따라 `workflows` 속성으로 정의할 수 있습니다.

```yaml
workflows:
  - template: technologies/template-to-execute.yaml
```

**Type of workflows**

1. Generic workflows (일반 작업흐름)
2. Conditional workflows (조건부 작업흐름)

#### Generic Workflows (일반 작업흐름)

generic workflow에서는 단일 workflow 파일에서 실행할 단일 또는 여러 템플릿을 정의할 수 있습니다. 파일과 디렉토리를 모두 입력으로 지원합니다.


주어진 URL 목록에서 모든 구성 관련 템플릿을 실행하는 workflow.

```yaml
workflows:
  - template: files/git-config.yaml
  - template: files/svn-config.yaml
  - template: files/env-file.yaml
  - template: files/backup-files.yaml
  - tags: xss,ssrf,cve,lfi
```

프로젝트에 대해 정의된 특정 검사 목록을 실행하는 workflow.

```yaml
workflows:
  - template: cves/
  - template: exposed-tokens/
  - template: exposures/
  - tags: exposures
```

#### Conditional Workflows (조건부 작업흐름)

이전 템플릿의 조건을 일치시킨 후 실행되는 조건 템플릿을 생성할 수 있습니다. 취약점 탐지 및 exploitation뿐만 아니라 기술 기반 탐지 및 exploitation에도 유용합니다. 이러한 종류의 workflow에 대한 사용 사례는 방대하고 다양합니다.


**템플릿 기반 조건 체크**

기본 템플릿이 일치할 때 하위 템플릿을 실행하는 workflow.


```yaml
workflows:
  - template: technologies/jira-detect.yaml
    subtemplates:
      - tags: jira
      - template: exploits/jira/
```

**matcher 이름 기반 조건 체크**

결과에서 기본 템플릿의 matcher가 발견되면 하위 템플릿을 실행하는 workflow.

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

비슷한 방식으로 필요한 만큼 workflow에 대한 중첩 검사를 만들 수 있습니다.


**하위 템플릿과 matcher 이름 기반 다중 레벨 조건 체크**

이전 템플릿이 일치하는 경우에만 실행되는 템플릿 실행 체인을 보여주는 workflow.


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

conditional workflows는 모든 대상에 모든 템플릿을 적용하는 대신 가장 효율적인 방식으로 검사 및 취약성 탐지를 수행하는 좋은 예이며 일반적으로 시간에 대한 ROI가 우수하고 대상에도 적합합니다.

더 완전한 workflow 예제들은 [here](../template-examples/workflow.md)에서 제공됩니다.
