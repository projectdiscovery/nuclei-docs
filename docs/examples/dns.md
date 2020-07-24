## DNS  

### Basic template

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
          # The response must contains a CNAME record
          - "IN\tCNAME"
```

### Multiple matcher

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
