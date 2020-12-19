### Generic workflows

```yaml
id: workflow-example
info:
  name: Test Workflow Template
  author: pdteam

workflows:
  - template: /root/jira-detect.yaml
  - template: /root/confluence-detect.yaml
```


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

### Multi conditional workflows

```yaml

id: springboot-workflow

info:
  name: Springboot Security Checks
  author: dwisiswant0

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
