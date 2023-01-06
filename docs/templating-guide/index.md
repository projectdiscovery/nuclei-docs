# Templating Guide

**Nuclei** is based on the concepts of `YAML` based template files that define how the requests will be sent and processed. This allows easy extensibility capabilities to nuclei.

The templates are written in `YAML` which specifies a simple human-readable format to quickly define the execution process.

**Guide to write your own nuclei template -**

----------

## Template Details

Each template has a unique ID which is used during output writing to specify the template name for an output line.

The template file ends with **YAML** extension. The template files can be created any text editor of your choice.

```yaml
id: git-config
```

ID must not contain spaces. This is done to allow easier output parsing.

### Information

Next important piece of information about a template is the **info** block. Info block provides **name**, **author**, **severity**, **description**, **reference**, **tags** and `metadata`. It also contains **severity** field which indicates the severity of the template, **info** block also supports dynamic fields, so one can define N number of `key: value` blocks to provide more useful information about the template. **reference** is another popular tag to define external reference links for the template.

Another useful tag to always add in `info` block is **tags**. This allows you to set some custom tags to a template, depending on the purpose like `cve`, `rce` etc. This allows nuclei to identify templates with your input tags and only run them.

Example of an info block - 

```yaml
info:
  name: Git Config File Detection Template
  author: Ice3man
  severity: medium
  description: Searches for the pattern /.git/config on passed URLs.
  reference: https://www.acunetix.com/vulnerabilities/web/git-repository-found/
  tags: git,config
```

Actual requests and corresponding matchers are placed below the info block, and they perform the task of making requests to target servers and finding if the template request was successful.

Each template file can contain multiple requests to be made. The template is iterated and one by one the desired requests are made to the target sites.


The best part of this is you can simply share your crafted template with your teammates, triage/security team to replicate the issue on the other side with ease.

#### Metadata

It's possible to add metadata nodes, for example, to integrates with [uncover](https://github.com/projectdiscovery/uncover) (cf. [Uncover Integration](https://nuclei.projectdiscovery.io/nuclei/get-started/#uncover-integration)).

The metadata nodes are crafted this way: `<engine>-query: '<query>'` where:

- `<engine>` is the search engine, equivalent of the value of the `-ue` option of nuclei or the `-e` option of uncover
- `<query>` is the search query, equivalent of the value of the `-uq` option of nuclei or the `-q` option of uncover

For example for Shodan:

```
info:
  metadata:
    shodan-query: 'vuln:CVE-2021-26855'
```
