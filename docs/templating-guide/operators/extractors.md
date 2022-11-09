### Extractors

Extractors can be used to extract and display in results a match from the response returned by a module.

#### Types

Multiple extractors can be specified in a request. As of now we support two type of extractors.

1. **regex** - Extract data from response based on a Regular Expression.
2. **kval** - Extract `key: value`/`key=value` formatted data from Response Header/Cookie
3. **json** - Extract data from JSON based response in JQ like syntax.
4. **xpath** - Extract xpath based data from HTML Response
4. **dsl** - Extract data from the response based on a DSL expressions.

#### Regex Extractor

Example extractor for HTTP Response body using **regex** - 

```yaml
extractors:
  - type: regex # type of the extractor
    part: body  # part of the response (header,body,all)
    regex:
      - "(A3T[A-Z0-9]|AKIA|AGPA|AROA|AIPA|ANPA|ANVA|ASIA)[A-Z0-9]{16}"  # regex to use for extraction.
```

#### Kval Extractor

A **kval** extractor example to extract `content-type` header from HTTP Response.

```yaml
extractors:
  - type: kval # type of the extractor
    kval:
      - content_type # header/cookie value to extract from response
```

Note that `content-type` has been replaced with `content_type` because **kval** extractor does not accept dash (`-`) as input and must be substituted with underscore (`_`).

#### JSON Extractor

A **json** extractor example to extract value of `id` object from JSON block.

```yaml
      - type: json # type of the extractor
        part: body
        name: user
        json:
          - '.[] | .id'  # JQ like syntax for extraction
```

For more details about JQ - https://github.com/stedolan/jq

#### Xpath Extractor

A **xpath** extractor example to extract value of `href` attribute from HTML response. 

```yaml
extractors:
  - type: xpath # type of the extractor
    attribute: href # attribute value to extract (optional)
    xpath:
      - '/html/body/div/p[2]/a' # xpath value for extraction
```

With a simple [copy paste in browser](https://www.scientecheasy.com/2020/07/find-xpath-chrome.html/), we can get the **xpath** value form any web page content.

#### DSL Extractor

A **dsl** extractor example to extract the effective `body` length through the `len` helper function from HTTP Response.

```yaml
extractors:
  - type: dsl  # type of the extractor
    dsl:
      - len(body) # dsl expression value to extract from response
```

#### Dynamic Extractor

Extractors can be used to capture Dynamic Values on runtime while writing Multi-Request templates. CSRF Tokens, Session Headers, etc. can be extracted and used in requests. This feature is only available in RAW request format.

Example of defining a dynamic extractor with name `api` which will capture a regex based pattern from the request.

```yaml
    extractors:
      - type: regex
        name: api
        part: body
        internal: true # Required for using dynamic variables
        regex:
          - "(?m)[0-9]{3,10}\\.[0-9]+"
```

The extracted value is stored in the variable **api**, which can be utilised in any section of the subsequent requests.

If you want to use extractor as a dynamic variable, you must use `internal: true` to avoid printing extracted values in the terminal.

An optional regex **match-group** can also be specified for the regex for more complex matches.

```yaml
extractors:
  - type: regex  # type of extractor
    name: csrf_token # defining the variable name
    part: body # part of response to look for
    # group defines the matching group being used. 
    # In GO the "match" is the full array of all matches and submatches 
    # match[0] is the full match
    # match[n] is the submatches. Most often we'd want match[1] as depicted below
    group: 1
    regex:
      - '<input\sname="csrf_token"\stype="hidden"\svalue="([[:alnum:]]{16})"\s/>'
```

The above extractor with name `csrf_token` will hold the value extracted by `([[:alnum:]]{16})` as `abcdefgh12345678`. 

If no group option is provided with this regex, the above extractor with name `csrf_html_tag` will hold the full match (by `<input name="csrf_token"\stype="hidden"\svalue="([[:alnum:]]{16})" />`) as `<input name="csrf_token" type="hidden" value="abcdefgh12345678" />`.

