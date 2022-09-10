# Templating Guide

**Nuclei**는 request를 보내고 처리하기 위해 `YAML` 기반 템플릿 파일의 컨셉을 기반으로 합니다. 이를 통해 nuclei로 쉽게 확장할 수 있습니다.

템플릿은 실행 프로세스를 빠르게 정의하기 위해 사람이 읽을 수 있는 간단한 형식의 `YAML`로 작성되었습니다.

**nuclei 템플릿 직접 작성을 위한 가이드 -**

----------

## 템플릿 세부 정보

각 템플릿에는 출력 라인에 대한 템플릿 이름을 지정하기 위해 출력 자성 중에 사용되는 고유 ID가 있습니다.

템플릿 파일은 **YAML** 확장자로 끝납니다. 템플릿 파일은 원하는 텍스트 편집기로 만들 수 있습니다.

```yaml
id: git-config
```

ID는 공백을 포함할 수 없습니다. 이것은 더 쉬운 출력 구문 분석을 허용하기 위해 수행됩니다.

### Information

템플릿에 대한 다음으로 중요한 정보는 **info** 블록입니다. Info 블록은 **name**, **author**, **severity**, **description**, **reference** 및 **tags**를 제공합니다. 또한 템플릿의 심각도를 나타내는 **severity** 필드가 포함되어 있으며, **info** 블록은 동적 필드도 지원하므로 N 개의 `key: value` 블록을 정의하여 템플릿에 대한 보다 유용한 정보를 제공할 수 있습니다.

`info` 블록에 항상 추가할 수 있는 또 다른 유용한 태그는 **tags** 입니다. 이를 통해 `cve`, `rce` 등과 같은 목적에 따라 일부 사용자 정의 태그를 템플릿에 설정할 수 있습니다. 이를 통해 nuclei는 입력 태그가 있는 템플릿만을 식별하고 실행할 수 있습니다.

info block 예시 - 

```yaml
info:
  name: Git Config File Detection Template
  author: Ice3man
  severity: medium
  description: Searches for the pattern /.git/config on passed URLs.
  reference: https://www.acunetix.com/vulnerabilities/web/git-repository-found/
  tags: git,config
```

실제 requests 및 해당되는 matchers는 info block 아래에 위치하며, 대상 서버에 request를 만들고 템플릿 request가 성공했는지 확인하는 작업을 수행합니다.

각 템플릿 파일에는 수행할 여러 request들이 포함될 수 있습니다. 템플릿이 반복되면서 원하는 request가 대상 사이트에 하나씩 만들어집니다.


이것의 가장 좋은 점은 제작된 템플릿을 팀원, 분류/보안 팀과 간단히 공유하여 다른 쪽에서 문제를 쉽게 복제할 수 있다는 점입니다.

