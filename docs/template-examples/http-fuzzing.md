### Basic SSTI Template

A simple template to discover `{{<number>*<number>}}` type SSTI vulnerabilities.

```yaml
id: fuzz-reflection-ssti

info:
  name: Basic Reflection Potential SSTI Detection
  author: pdteam
  severity: low

variables:
  first: "{{rand_int(10000, 99999)}}"
  second: "{{rand_int(10000, 99999)}}"
  result: "{{to_number(first)*to_number(second)}}"

requests:
  - method: GET
    path:
      - "{{BaseURL}}"
    payloads:
      reflection:
        - '{{concat("{{", "§first§*§second§", "}}")}}'
    fuzzing:
      - part: query
        type: postfix
        mode: multiple
        fuzz:
          - "{{reflection}}"
    matchers:
      - type: word
        part: body
        words:
          - "{{result}}"
```

### Basic XSS Template

A simple template to discover XSS probe reflection in HTML pages.

```yaml
id: fuzz-reflection-xss

info:
  name: Basic Reflection Potential XSS Detection
  author: pdteam
  severity: low

requests:
  - method: GET
    path:
      - "{{BaseURL}}"
    payloads:
      reflection:
        - "6842'\"><9967"
    stop-at-first-match: true
    fuzzing:
      - part: query
        type: postfix
        mode: single
        fuzz:
          - "{{reflection}}"
    matchers-condition: and
    matchers:
      - type: word
        part: body
        words:
          - "{{reflection}}"
      - type: word
        part: header
        words:
          - "text/html"
```

### Basic OpenRedirect Template

A simple template to discover open-redirects issues.

```yaml
id: fuzz-open-redirect

info:
  name: Basic Open Redirect Detection
  author: pdteam
  severity: low

requests:
  - method: GET
    path:
      - "{{BaseURL}}"
    payloads:
      redirect:
        - "https://example.com"
    fuzzing:
      - part: query
        type: replace
        mode: single
        keys-regex:
          - "redirect.*"
        fuzz:
          - "{{redirect}}"
    matchers-condition: and
    matchers:
      - type: word
        part: header
        words:
          - "{{redirect}}"
      - type: status
        status:
          - 301
          - 302
          - 307
```

### Blind SSRF OOB Detection

A simple template to detect Blind SSRF in known-parameters using interact.sh with HTTP fuzzing.

```yaml
id: fuzz-ssrf

info:
  name: Basic Blind SSRF Detection
  author: pdteam
  severity: low

requests:
  - method: GET
    path:
      - "{{BaseURL}}"
    payloads:
      redirect:
        - "{{interactsh-url}}"
    fuzzing:
      - part: query
        type: replace
        mode: single
        keys:
          - "dest"
          - "redirect"
          - "uri"
          - "path"
          - "continue"
          - "url"
          - "window"
          - "next"
          - "data"
          - "reference"
          - "site"
          - "html"
          - "val"
          - "validate"
          - "domain"
          - "callback"
          - "return"
          - "page"
          - "feed"
          - "host"
          - "port"
          - "to"
          - "out"
          - "view"
          - "dir"
          - "show"
          - "navigation"
          - "open"
        fuzz:
          - "https://{{redirect}}"
    matchers-condition: and
    matchers:
      - type: word
        part: interactsh_protocol  # Confirms the DNS Interaction
        words:
          - "http"
```

### Blind CMDi OOB based detection

A simple template to detect blind CMDI using interact.sh

```yaml
id: fuzz-cmdi

info:
  name: Basic Blind CMDI Detection
  author: pdteam
  severity: low

requests:
  - method: GET
    path:
      - "{{BaseURL}}"
    payloads:
      redirect:
        - "{{interactsh-url}}"
    fuzzing:
        fuzz:
          - "nslookup {{redirect}}"
    matchers:
      - type: word
        part: interactsh_protocol  # Confirms the DNS Interaction
        words:
          - "dns"
```