### HTTP Fuzzing

Nuclei supports fuzzing of HTTP requests based on rules defined in the `fuzzing` section of the HTTP request. This allows creating templates for generic Web Application vulnerabilities like SQLi, SSRF, CMDi, etc without any information of the target like a classic web fuzzer.

#### Part

Part specifies what part of the request should be fuzzed based on the specified rules. Available options for this parameter are - 

1. **query** - fuzz query parameters for URL

```yaml
fuzzing:
  - part: query # fuzz parameters in URL query
```

Support will be added for `path`,`header`,`body`,`cookie`, etc parts soon.

#### Type

Type specifies the type of replacement to perform for the fuzzing rule value. Available options for this parameter are - 

1. **replace** - replace the value with payload
2. **prefix** - prefix the value with payload
3. **postfix** - postfix the value with payload
4. **infix** - infix the value with payload (place in between)

```yaml
fuzzing:
  - part: query
    type: postfix # Fuzz query and postfix payload to params
```

#### Mode

Mode specifies the mode in which to perform the replacements. Available modes are - 

1. **single** - replace one value at a time
2. **multiple** - replace all values at once

```yaml
fuzzing:
  - part: query
    type: postfix
    mode: multiple # Fuzz query postfixing payloads to all parameters at once
```

#### Filters

Multiple filters are supported to restrict the scope of fuzzing to only interesting parameter keys and values. Nuclei HTTP Fuzzing engine converts request parts into Keys and Values which then can be filtered by their related options.

The following filter fields are supported - 

1. **keys** - list of parameter names to fuzz (exact match)
2. **keys-regex** - list of parameter regex to fuzz 
3. **values** - list of value regex to fuzz

These filters can be used in combination to run highly targeted fuzzing based on the parameter input. A few examples of such filtering are provided below.

```yaml
# fuzzing command injection based on parameter name value
fuzzing:
  - part: query
    type: replace
    mode: single
    keys:
      - "daemon"
      - "upload"
      - "dir"
      - "execute"
      - "download"
      - "log"
      - "ip"
      - "cli"
      - "cmd"
```

```yaml
# fuzzing openredirects based on parameter name regex
fuzzing:
  - part: query
    type: replace
    mode: single
    keys-regex:
      - "redirect.*"
```

```yaml
# fuzzing ssrf based on parameter value regex
fuzzing:
  - part: query
    type: replace
    mode: single
    values:
      - "https?://.*"
```

#### Fuzz

Fuzz specifies the values to replace with a `type` for a parameter. It supports payloads, DSL functions, etc and allows users to fully utilize the existing nuclei feature-set for fuzzing purposes.

```yaml
# fuzz section for xss fuzzing with stop-at-first-match
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
```

```yaml
# using interactsh-url placeholder for oob testing
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
    fuzz:
      - "https://{{redirect}}"
```

```yaml
# using template-level variables for SSTI testing
variables:
  first: "{{rand_int(10000, 99999)}}"
  second: "{{rand_int(10000, 99999)}}"
  result: "{{to_number(first)*to_number(second)}}"

requests:
    ...
    payloads:
      reflection:
        - '{{concat("{{", "§first§*§second§", "}}")}}'
    fuzzing:
      - part: query
        type: postfix
        mode: multiple
        fuzz:
          - "{{reflection}}"
```

#### Example **Fuzzing** template

An example sample template for fuzzing XSS vulnerabilities is provided below.

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

More complete examples are provided [here](../../template-examples/http-fuzzing.md)