### Variables

Variables can be used to declare some values which remain constant throughout the template. The value of the variable once calculated does not change. Variables can be either simple strings or DSL helper functions. If the variable is a helper function, it is enclosed in double-curly brackets `{{<expression>}}`. Variables are declared at template level.

Example variables - 

```yaml
variables:
  a1: "test" # A string variable
  a2: "{{to_lower(rand_base(5))}}" # A DSL function variable
```

Currently, `dns`, `http`, `headless` and `network` protocols support variables.

Example of templates with variables - 

```yaml
# Variable example using HTTP requests
id: variables-example

info:
  name: Variables Example
  author: pdteam
  severity: info

variables:
  a1: "value"
  a2: "{{base64('hello')}}"

http:
  - raw:
      - |
        GET / HTTP/1.1
        Host: {{FQDN}}
        Test: {{a1}}
        Another: {{a2}}
    stop-at-first-match: true
    matchers-condition: or
    matchers:
      - type: word
        words: 
          - "value"
          - "aGVsbG8="
```

```yaml
# Variable example for network requests
id: variables-example

info:
  name: Variables Example
  author: pdteam
  severity: info

variables:
  a1: "PING"
  a2: "{{base64('hello')}}"

tcp:
  - host: 
      - "{{Hostname}}"
    inputs:
      - data: "{{a1}}"
    read-size: 8
    matchers:
      - type: word
        part: data
        words:
          - "{{a2}}"
```

### Constants
In addition to variables, you can also define constants in templates. Constants are similar to variables but have a distinct difference: their values cannot be overridden or changed, even by command-line variables. They are used to declare scalar values that remain fixed throughout the template. Constants are helpful when you want to ensure that a specific value remains unchanged across multiple template executions.

To declare a constant, use the `constants` keyword followed by the constant name and its value. Constants are declared at the template level, just like variables.

Example constants:

```yaml
constants:
  p: test
```

In the above example, `p` is a constant with the values `test`

Here's an example template that uses constants:

```yaml
id: constants-example

info:
  name: Constants Example
  author: pdteam
  severity: info

constants:
  p: test

http:
  - raw:
      - |
        GET /api/data?param={{p}} HTTP/1.1
        Host: {{Hostname}}
    matchers:
      - type: word
        words: 
          - "OK"
```

If the same variable is passed via CLI, the value remains `test` (`nuclei -V p=anothervalue ...`).

Constants provide a way to ensure that specific values remain constant and cannot be modified, providing consistency and predictability in your templates.