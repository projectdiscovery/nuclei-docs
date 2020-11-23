### Generic workflows

```yaml
id: workflow-example
info:
  name: Test Workflow Template
  author: pdteam

workflows:
  - template: /root/jira-detect.yaml
  - template: /root/confluence-detect.yaml
`


### Basic conditional workflows


```yaml

id: springboot-workflow

info:
  name: Springboot Security Checks
  author: dwisiswant0

workflows:

  - template: security-misconfiguration/springboot-detect.yaml
    subtemplates:
      - template: cves/CVE-2018-1271.yaml
      - template: cves/CVE-2018-1271.yaml
      - template: cves/CVE-2020-5410.yaml
      - template: vulnerabilities/springboot-actuators-jolokia-xxe.yaml
      - template: vulnerabilities/springboot-h2-db-rce.yaml
```

### Conditional workflows with matcher


```yaml
id: workflow-example
info:
  name: Test Workflow Template
  author: pdteam

workflows:

  - template: technologies/tech-detect.yaml
    matchers:
      - name: wordpress
        subtemplates:
          - template: cves/CVE-2019-6715.yaml
          - template: cves/CVE-2019-9978.yaml
          - template: files/wordpress-db-backup.yaml
          - template: files/wordpress-debug-log.yaml
          - template: files/wordpress-directory-listing.yaml
          - template: files/wordpress-emergency-script.yaml
          - template: files/wordpress-installer-log.yaml
          - template: files/wordpress-tmm-db-migrate.yaml
          - template: files/wordpress-user-enumeration.yaml
          - template: security-misconfiguration/wordpress-accessible-wpconfig.yaml
          - template: vulnerabilities/sassy-social-share.yaml
          - template: vulnerabilities/w3c-total-cache-ssrf.yaml
          - template: vulnerabilities/wordpress-duplicator-path-traversal.yaml
          - template: vulnerabilities/wordpress-social-metrics-tracker.yaml
          - template: vulnerabilities/wordpress-wordfence-xss.yaml
          - template: vulnerabilities/wordpress-wpcourses-info-disclosure.yaml
```


### Multiple Matcher workflow


```yaml
id: workflow-multiple-matcher
info:
  name: Test Workflow Template
  author: pdteam

workflows:
  - template: technologies/tech-detect.yaml
    match:
      - value: vbulletin
        subtemplates:
          - template: /root/vbulletin-exp1.yaml
          - template: /root/vbulletin-exp2.yaml
      - value: jboss
        subtemplates:
          - template: /root/jboss-exp1.yaml
          - template: /root/jboss-exp2.yaml
```

### Custom headers

```yaml
id: custom-headers
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

  // defining custom headers 
  externalHeaders := {
    "x-bug-bounty": "mzack9999",
  }

  // defining custom parameters

  externalArgumentsBrute := {
    "username": "mzack9999",
    "passwords": ["pass1", "pass2", "pass3"]
  }

  // defining custom parameters

  externalArgumentsPwn := {
    "newemail": "attacker@pwn.something",
  }


  if bruteforce(externalHeaders, externalArgumentsBrute) {
    // template `pwnemail` will use cookies set by `bruteforce`
    pwnemail(externalHeaders, externalArgumentsPwn)
  }
```


### Custom workflow 

```yaml
id: custom-workflows
info:
  name: Test Workflow Template
  author: pdteam

variables:
  google_key: tokens/google-api-key.yaml

logic:
  |
  fmt := import("fmt")
  os := import("os")

    if google_key() {

      // send notification via slack
      // apikey is the name of extractor defind in google-api-key.yaml

      slacktoken := os.getenv("slacktoken")
      slackCmd :=  os.exec("slacknotify", "-key", slacktoken, google_key["apikey"])
      slackCmd.run()

      // saving data on the system

      file := os.create("workflow.output.txt")
      file.write_string(google_key["apikey"])
      file.close()
    }
  }
```
