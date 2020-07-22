# Template Examples

## HTTP  

```yaml
id: basic-http-test

info:
  name: Test File
  author: pdteam
  severity: info

requests:
  - method: GET
    path:
      - "{{BaseURL}}/test.txt"
    matchers:
      - type: word
        words:
          - "This is test!"
```

## DNS  


```yaml
id: basic-dns-example

info:
  name: Basic DNS Request
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

## Worflow 

```yaml
id: workflow-example
info:
  name: Jira-Pawner
  author: mzack9999

variables:

  # defining temapltes using variables.
  # variables names as user defind, it could be anything.
  # relative path support is now added into nuclei engine for better UX.

  jira: panels/detect-jira.yaml
  jira_cve_1: cves/CVE-2018-20824.yaml
  jira_cve_2: cves/CVE-2019-3399.yaml
  jira_cve_3: cves/CVE-2019-11581.yaml
  jira_cve_4: cves/CVE-2017-18101.yaml

logic:
  |
 # defening conditionals templates.
  if jira() {

 # if above conditon retruns true, run all the below tempaltes.
 # if above conditon fails, no tempaltes will be executed.

    jira_cve_1()
    jira_cve_2()
    jira_cve_3()
    jira_cve_4()

  }
```
## Fuzzing 


```yaml
id: dummy-raw
info:
  name: Example-Fuzzing

requests:
  - payloads:
      param_a: /home/user/wordlist_param_a.txt
      param_b: /home/user/wordlist_param_b.txt
    attack: clusterbomb  # Available options: sniper, pitchfork and clusterbomb
    raw:
      # Request with simple param and header manipulation with DSL functions
      - |
        POST /?param_a={{param_a}}&paramb={{param_b}} HTTP/1.1
        User-Agent: {{param_a}}
        Host: {{Hostname}}
        another_header: {{base64(param_b)}}
        Accept: */*
        This is the Body
      # Request with body manipulation
      - |
        DELETE / HTTP/1.1
        User-Agent: nuclei
        Host: {{Hostname}}
        This is the body {{sha256(param_a)}}
      # Yet another one
      - |
        PUT / HTTP/1.1
        Host: {{Hostname}}
        This is again the request body {{html_escape(param_a)}} + {{hex_encode(param_b))}}
    matchers:
      - type: word
        words:
          - "title"
          - "body"
```