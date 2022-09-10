### 기본 템플릿

입력에 대한 CNAME 레코드가 존재하는지 감지하기 위한 기본적인 DNS 요청입니다.

```yaml
id: basic-dns-example

info:
  name: Test DNS Template
  author: pdteam
  severity: info

dns:
  - name: "{{FQDN}}"
    type: CNAME
    class: inet
    recursion: true
    retries: 3
    matchers:
      - type: word
        words:
          # 응답은 CNAME 레코드를 반드시 포함해야 합니다.
          - "IN\tCNAME"
```

### Multiple matcher

zendesk.com 또는 github.io을 가리키는 CNAME 레코드로 서브도메인을 탐지할 수 있는 여러 Matcher들을 보여주는 예시입니다.

```yaml
id: multiple-matcher

info:
  name: Test DNS Template
  author: pdteam
  severity: info

dns:
  - name: "{{FQDN}}"
    type: CNAME
    class: inet
    recursion: true
    retries: 5
    matchers-condition: or
    matchers:
      - type: word
        name: zendesk
        words:
          - "zendesk.com"
      - type: word
        name: github
        words:
          - "github.io"
```
