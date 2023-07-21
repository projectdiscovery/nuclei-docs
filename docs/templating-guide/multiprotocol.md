# Multi Protocol Execution (beta)

Nuclei supports many protocol such as HTTP,DNS,Network etc and one can write nuclei template for a vulnerability in any of these protocols, but what if a vulnerability requires execution of multiple protocols in sync to test/exploit it. A classic example of this is **Subdomain Takeovers** where one needs to check for CNAME record of a subdomain and then check if the service is vulnerable to takeover or not. This was partially possible in nuclei with workflows but with **Nuclei v3.0** it is now possible to easily write a **Template** that can execute multiple protocols in sync and then perform checks on the results of each protocol along with other improvements.

## Example

```yaml
id: dns-http-template

info:
  name: dns + http takeover template
  author: pdteam
  severity: info

dns:
  - name: "{{FQDN}}" # DNS Request
    type: cname

    extractors:
      - type: dsl
        name: exported_cname
        dsl:
          - cname
        internal: true

http:
  - method: GET # http request
    path:
      - "{{BaseURL}}"

    matchers:
      - type: dsl
        dsl:
          - contains(body,'Domain not found') # check for http string
          - contains(exported_cname, 'github.io') # check for cname (extracted information from dns response)
        condition: and
```

As we can see in above example there is no new logic or syntax just write logic for each protocol and then use [dynamic extractor](./operators/extractors.md#dynamic-extractor) to export that variable and it is shared across all protocols and we call this **Template Context** which contains all variables that are scoped at template Level.

## Features

!!! example  "Features that make multi protocol execution powerful"

    * Export Data Using Dynamic Extractors
    * Template Scoped Protocol Responses
    * Shared Variables Across Protocols

## Export Data Using Dynamic Extractors

If you are not familiar with dynamic extractors then please read [dynamic extractor](./operators/extractors.md#dynamic-extractor) section first.

Earlier Dynamic Extractors support was only available for specific protocol or Workflows but with multi protocol execution dynamic extracted values are stored in template context and can be used across all protocols.

### Example

```yaml
id: dns-http-template

info:
  name: dns + http takeover template
  author: pdteam
  severity: info

dns:
  - name: "{{FQDN}}" # DNS Request
    type: cname

    extractors:
      - type: dsl
        name: exported_cname
        dsl:
          - cname
        internal: true

http:
  - method: GET # http request
    path:
      - "{{BaseURL}}"

    matchers:
      - type: dsl
        dsl:
          - contains(body,'Domain not found') # check for http string
          - contains(exported_cname, 'github.io') # check for cname (extracted information from dns response)
        condition: and
```

## Template Scoped Protocol Responses

In Previous example we saw how to export dns cname and use it in http request but what if there are more than 4 protocols in a template and we want to export `subject_dn`,`ns`,`cname` , `all_headers` and what not. this can be done just by adding more dynamic extractors but that will add lot of noise to template and redundant logic and at some point it will hard to track and maintain all those variables.

To address this Multi protocol execution brings support for template scoped protocol responses i.e all response fields of all protocols in template are available in template context with protocol prefix.

Ex:

| Protocol | Response Field | Exported Variable |
| -------- | -------------- | ----------------- |
| ssl      | subject_dn     | ssl_subject_dn    |
| dns      | ns             | dns_ns            |
| dns      | cname          | dns_cname         |
| http     | all_headers    | http_all_headers  |

!!! info "Note"

This is just a example but response fields of all protocols used in multi protocol template are exported

### Example

```yaml
id: dns-ssl-http-proto-prefix

info:
  name: multi protocol request with response fields
  author: pdteam
  severity: info

dns:
  - name: "{{FQDN}}" # DNS Request
    type: cname

ssl:
  - address: "{{Hostname}}" # ssl request

http:
  - method: GET # http request
    path:
      - "{{BaseURL}}"

    matchers:
      - type: dsl
        dsl:
          - contains(http_body,'ProjectDiscovery.io') # check for http string
          - trim_suffix(dns_cname,'.ghost.io.') == 'projectdiscovery' # check for cname (extracted information from dns response)
          - ssl_subject_cn == 'blog.projectdiscovery.io'
        condition: and
```

!!! tip "List all available response fields"

 To list all exported response fields write a multi protocol template and run it with `-debug -svd` flag and it will print all exported response fields
 Ex:
 
  `nuclei -t multi-protocol-template.yaml -u scanme.sh -debug -svd`

### Shared Variables Across Protocols

As we can tell now multi protocol execution brings lot of flexibility and power to write complex templates but there are some cases where logic requires some kind of preprocessing to variables before they are used, again this can be achieved by using variables and helper functions directly in request but that will make template ugly and unreadable and this is especially true with network templates.

For example consider [Apache Airflow <=1.10.10 - Command Injection](https://github.com/projectdiscovery/nuclei-templates/blob/main/network/cves/2020/CVE-2020-11981.yaml) which requires base64-encoding,decoding,concat,interactsh_urls and then payload is ready to use, if all this logic is implemented in protocol request itself then it will be hard to read and maintain i.e why we have [variables](variables.md), to reuse variables and make payloads readable and easy to maintain, it is also necessary for other cases where generated interactsh urls needs to be same across multiple requests/protocols etc.

For this reason multi protocol execution supports [variables](variables.md) and they are also scoped at template context and can be used across all protocols for preprocessing, payload building etc.

### Example

```yaml
id: dns-ssl-http-with-variables

info:
  name: multi protocol request with variables
  author: pdteam
  severity: info


variables:
  cname_filtered: '{{trim_suffix(dns_cname,".ghost.io.")}}'

dns:
  - name: "{{FQDN}}" # DNS Request
    type: cname

ssl:
  - address: "{{Hostname}}" # ssl request

http:
  - method: GET # http request
    path:
      - "{{BaseURL}}"

    matchers:
      - type: dsl
        dsl:
          - contains(http_body,'ProjectDiscovery.io') # check for http string
          - cname_filtered == 'projectdiscovery' # check for cname (extracted information from dns response)
          - ssl_subject_cn == 'blog.projectdiscovery.io'
        condition: and
```

The above template and previous one are same but in this case we are using variables to preprocess `dns_cname` and then use it in http request later on

!!! info "Note"

 This is just a example of how variables can be used in multi protocol templates, there are many other use cases where variables can be used to make templates more readable and maintainable


## How Multi Protocol Templates Work & Things to Note

At this point we have seen how multi protocol templates look like and what are the features it brings to the table. Now let's see how multi protocol templates work and things to keep in mind while writing them.

- Multi Protocol Templates are executed in order of protocols defined in template
- Protocols in multi protocol templates are executed in serial i.e one after another
- Response fields of protocols are exported to template context as soon as that protocol is executed
- Variables are scoped at template level and evaluated after each protocol execution
- Multi protocol brings limited indirect support for preprocessing(using variables) and postprocessing(using dynamic extractors+dsl) for protocols


### Known Limitations and Workarounds

Currently there is no direct way to handle arrays (i.e if exported variable is not just a single element but an array). A possible workaround for this is to index elements of array

Example consider `ssl_subject_an` which is subject alternative names present in ssl certificate of target, if target has more than 1 Subject Alternative Name then `ssl_subject_an` will be an array of all Subject Alternative Names and assume vulnerabilty needs to check cname records of all Subject Alternative Names then that is not possible directly and only the first element in array is used but if we want to use all elements of array then we can use index to access each element of array starting from 1

```
// example
// ssl_subject_an = [github.io,projectdiscovery.io,scanme.sh] then
ssl_subject_an = github.io
ssl_subject_an1 = projectdiscovery.io
ssl_subject_an2 = scanme.sh
```

```yaml
dns:
  - name: "{{ssl_subject_an}}" # DNS Request
    type: cname
  - name: "{{ssl_subject_an1}}" # DNS Request
    type: cname
  - name: "{{ssl_subject_an2}}" # DNS Request
    type: cname
  - name: "{{ssl_subject_an3}}" # DNS Request
    type: cname
```

In Above dns request example we are using index to access each element of array and send dns request for each element of array if array only contains 2 elements then last 2 requests are skipped with unresolved variables warning

!!! info "Note"

there are multiple ways to achieve this. An alternative implementation for this is to use `payloads`

> Features are implemented based on use cases and feedback from community, if you have any usecase that requires improvements to multi protocol execution or regarding array variables then please open a issue with details and proper usecase


### FAQ

??? info "What Protocols are supported in Multi-Protocol Execution Mode ?"

There is no restriction around any protocol and any protocol available/implemented in nuclei engine can be used in multi protocol templates

??? info "How many protocols can be used in Multi-Protocol Execution Mode ?"

There is no restriction around number of protocols but currently duplicated protocols are not supported i.e dns -> http -> ssl -> http. Please open a issue if you have a vulnerabilty/usecase that requires duplicated protocols

??? info "What happens if a protocol fails ?"

Multi Protocol Execution follows exit on error policy i.e if protocol fails to execute then execution of remaining protocols is skipped and template execution is stopped

??? info "How is multi protocol execution different from workflows ?"

Workflow as name suggest is a workflow that executes templates based on workflow file

- Workflow does not contain actual logic of vulnerability but just a workflow that executes different templates
- Workflow supports conditional execution of multiple templates
- Workflow has limited supported for variables and dynamic extractors

To summarize workflow is a step higher than template and manages execution of templates based on workflow file

??? info "Is it supported in nuclei v2 ?"

No, Multi Protocol Execution is only supported in nuclei v3 and above


