### Extractors

Extractors can be used to extract and display in results a match from the response returned by a module.

#### Types

Multiple extractors can be specified in a request. As of now we support two type of extractors.

1. **regex** - Extract data from a **part** based on a Regular Expression.
2. **kval** - Extract a part from the protocol result.

Example extractor for HTTP Response body using **regex** - 

```yaml
# A list of extractors for text extraction
extractors:
  # type of the extractor.
  - type: regex
    # part of the response to extract (can be headers, all too)
    part: body
    # regex to use for extraction.
    regex:
      - "(A3T[A-Z0-9]|AKIA|AGPA|AROA|AIPA|ANPA|ANVA|ASIA)[A-Z0-9]{16}"
```

A **kval** extractor example to extract `content-type` header from HTTP Protocol Response.


```yaml
# A list of extractors for text extraction
extractors:
  # type of the extractor
      - type: kval
        part: header
        kval:
        # header value to extract from response
          - content-type
```

#### Dynamic extractor

Extractors can be used to capture Dynamic Values on runtime while writing Multi-Request templates. CSRF Tokens, Session Headers, etc can be extracted and used in requests.

Example of defining a dynamic extractor with name `api_key` which will capture a regex based pattern from the request.

```yaml
    extractors:
      - type: regex
        name: api_key
        part: body
        internal: true
        regex:
          - "(?m)[0-9]{3,10}\\.[0-9]+"
```

Here we used extractor name as variable `api_key` which holds the extracted value and can be used in any part of the next requests. 

This feature is supported in RAW request format only. 

Note:- You can use `internal: true` when you only want to use extractor as dynamic variable as this will avoid printing extracted values in the terminal. 

An optional regex **match-group** can also be specified for the regex for more complex matches.

```yaml
# A list of extractors for text extraction
extractors:
  # type of extractor
  - type: regex
    # Let's reuse the extracted CSRF token
    name: csrf_token
    part: body
    # group defines the matching group being used. 
    # In GO the "match" is the full array of all matches and submatches 
    # match[0] is the full match
    # match[n] is the submatches. Most often we'd want match[1] as depicted below
    group: 1
    regex:
      - '<input\sname="csrf_token"\stype="hidden"\svalue="([[:alnum:]]{16})"\s/>'
```

The above extractor with name `csrf_token` will hold the value extracted (by `([[:alnum:]]{16}))` as `abcdefgh12345678`. 

If no group option is provided with this regex, the above extractor with name `csrf_html_tag` will hold the full match (by `<input name="csrf_token"\stype="hidden"\svalue="([[:alnum:]]{16})" />`) as `<input name="csrf_token" type="hidden" value="abcdefgh12345678" />`.

