Since release of [Nuclei v2.3.6](https://github.com/projectdiscovery/nuclei/releases/tag/v2.3.6), Nuclei supports using the [interact.sh](https://github.com/projectdiscovery/interactsh) API to achieve OOB based vulnerability scanning with automatic Request correlation built in. It's as easy as writing `{{interactsh-url}}`  anywhere in the request, and adding a matcher for `interact_protocol`. Nuclei will handle correlation of the interaction to the template & the request it was generated from allowing effortless OOB scanning.


### Interactsh Placeholder

`{{interactsh-url}}` placeholder is supported in **http** and **network** requests.

An example of nuclei request with `{{interactsh-url}}` placeholders is provided below. These are replaced on runtime with unique interact.sh URLs.

```yaml
  - raw:
      - |
        GET /plugins/servlet/oauth/users/icon-uri?consumerUri=https://{{interactsh-url}} HTTP/1.1
        Host: {{Hostname}}
```

### Interactsh Matchers

Interactsh interactions can be used with `word`, `regex` or `dsl` matcher/extractor using following parts.

| part                |
|---------------------|
| interactsh_protocol |
| interactsh_request  |
| interactsh_response |


!!! abstract "interactsh_protocol"
    Value can be dns, http or smtp. This is the standard matcher for every interactsh based template with DNS often as the common value as it is very non-intrusive in nature.

!!! abstract "interactsh_request"
    The request that the interact.sh server received.

!!! abstract "interactsh_response"
    The response that the interact.sh server sent to the client.

Example of Interactsh DNS Interaction matcher:

```yaml
    matchers:
      - type: word
        part: interactsh_protocol # Confirms the DNS Interaction
        words:
          - "dns"
```

Example of HTTP Interaction matcher + word matcher on Interaction content

```yaml
matchers-condition: and
matchers:
    - type: word
      part: interactsh_protocol # Confirms the HTTP Interaction
      words:
        - "http"

    - type: regex
      part: interactsh_request # Confirms the retrieval of /etc/passwd file
      regex:
        - "root:[x*]:0:0:"
```
