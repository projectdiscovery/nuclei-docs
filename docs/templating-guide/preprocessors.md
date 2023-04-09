## Template **Preprocessors**

Certain pre-processors can be specified globally anywhere in the template that run as soon as the template is loaded to achieve things like random ids generated for each template run.


### randstr

!!! abstract "Info"

	Generates a [random ID](https://github.com/rs/xid) for a template on each nuclei run. This can be used anywhere in the template and will always contain the same value. `randstr` can be suffixed by a number, and new random ids will be created for those names too. Ex. `{{randstr_1}}` which 	will remain same across the template.

	`randstr` is also supported within matchers and can be used to match the inputs.

For example:-

```yaml
http:
  - method: POST
    path:
      - "{{BaseURL}}/level1/application/"
    headers:
      cmd: echo '{{randstr}}'

    matchers:
      - type: word
        words:
          - '{{randstr}}'
```