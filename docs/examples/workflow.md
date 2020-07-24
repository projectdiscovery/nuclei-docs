
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
