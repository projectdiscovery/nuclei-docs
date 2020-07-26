
## Worflow 

### Basic Template


```yaml
id: workflow-example
info:
  name: Test Workflow Template
  author: pdteam

variables:
        jira_detect: technologies/jira-detect.yaml
        jira_cve_1: cves/CVE-2019-8449.yaml
        jira_cve_2: cves/CVE-2019-8451.yaml
        jira_cve_3: cves/CVE-2017-9506.yaml
        jira_cve_4: cves/CVE-2018-20824.yaml
        jira_cve_5: cves/CVE-2019-3396.yaml

  # Template/s can be defined as variables.
  # Variables names are user-defind.
  # Relative/full path can be used to define template path.
  # Dash (-) can not be used in variable name.
  # Logics can be used to define condition execution.
  # As listed below, if jira_detect is true, then only all other templates will be checked. 


logic:
        |
        if jira_detect(){
                jira_cve_1()
                jira_cve_2()
                jira_cve_3()
                jira_cve_4()
                jira_cve_5()
        }
```

### Multiple Matchers


```yaml
id: workflow-multiple-matcher
info:
  name: Test Workflow Template
  author: pdteam

variables:
        tech_detect: technologies/tech-detect.yaml
        wp_users: files/wordpress-user-enumeration.yaml

  # Template/s can be defined as variables.
  # Variables names are user-defind.
  # Relative/full path can be used to define template path.
  # Dash (-) can not be used in variable name.
  # Logics can be used to define condition execution.
  # As listed below, if jira_detect is true, then only all other templates will be checked.

logic:
        |
        if tech_detect["wordpress"] {
                wp_users()

        }
```


### Multiple Matcher Chain


```yaml
id: workflow-multiple-matcher
info:
  name: Test Workflow Template
  author: pdteam

variables:
        tech_detect: technologies/tech-detect.yaml
        wp_users: files/wordpress-user-enumeration.yaml
        wp_xss: vulnerabilities/wordpress-xss.yaml
        drupal_rce: vulnerabilities/drupal-rce.yaml
        joomla_xss: vulnerabilities/joomla-xss.yaml


  # Template/s can be defined as variables.
  # Variables names are user-defind.
  # Relative/full path can be used to define template path.
  # Dash (-) can not be used in variable name.
  # Logics can be used to define condition execution.
  # As listed below, if jira_detect is true, then only all other templates will be checked.

logic:
        |
        if tech_detect["wordpress"] {
                wp_users()
                wp_xss()
        }

        if tech_detect["drupal"] {
                drupal_rce()
        }

        if tech_detect["joomla"] {
                joomla_xss()
        }
```

### Custom Workflow

```yaml
id: custom-workflows
info:
  name: Test Workflow Template
  author: pdteam

  # Cookie-Reuse and custom headers within workflow

cookie-reuse: true
variables:
  bruteforce: nuclei-templates/examples/bruteforce-login.yaml
  pwnemail: nuclei-templates/examples/pwn-email.yaml
logic:
  |
  // module import
  fmt := import("fmt")
  os := import("os")


  externalHeaders := {
    "x-bug-bounty": "mzack9999",
  }
  externalArgumentsBrute := {
    "username": "mzack9999",
    "passwords": ["pass1", "pass2", "pass3"]
  }
  externalArgumentsPwn := {
    "newemail": "attacker@pwn.something",
  }
  if bruteforce(externalHeaders, externalArgumentsBrute) {
    // template `pwnemail` will use cookies set by `bruteforce`
    pwnemail(externalHeaders, externalArgumentsPwn)
  }
```
