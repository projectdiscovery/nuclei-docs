#### Extractors

Extractors are another important feature of nuclei. Extractors can be used to extract and display in results a match from the response body or headers based on available types.

##### Types

Multiple extractors can be specified in a request, as of now we support two type of extractors.

| Extractor Type | Part Matched               |
| -------------- | -------------------------- |
| regex          | Response body or headers   |
| kval           | Response headers or cookie |

Example extractor for response body using regex, you can use the following syntax.

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

To extract `key-value` formatted data from the header, you can use the following syntax.


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

To extract `key-value` formatted data from cookie, you can use the following syntax.


```yaml
# A list of extractors for text extraction
extractors:
  # type of the extractor
      - type: kval
        kval:
        # cookie value to extract from response
          - PHPSESSID
```

##### Matched Parts

Multiple parts of the response can also be extracted for the request, default matched part is `body` if not defined. 

| Part   | Matched Part                         |
| ------ | ------------------------------------ |
| body   | Body of the response                 |
| header | Header of the response               |
| all    | Both body and header of the response |

Note:- `kval` extractor only supported for header and cookies. 

##### Dynamic extractor

Extractor plays an important role while writing an template for chained request which requires dynamic value to use at run time, for example CSRF tokens, headers or any values requires to complete the chain.  

+ Example of defining extractor as dynamic variable:-

```yaml
    extractors:
      - type: regex
        name: api_key
        part: body
        internal: true
        regex:
          - "(?m)[0-9]{3,10}\\.[0-9]+"
```

Here we used extractor name as variable `api_key` which holds the value and can be reused in any part of the request dynamically, this feature is supported in RAW request format only. 

Note:- You can use `internal: true` when you only want to use extractor as dynamic variable, this will avoid printing extracted values in the terminal. 

Extraction of regex content can also be specific for **matchgroups**.

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

The above extractor with name `csrf_token` will hold the value extracted (by `([[:alnum:]]{16}))` as `abcdefgh12345678`. This is compared to not using the group variable.

```yaml
# A list of extractors for text extraction
extractors:
  # type of extractor
  - type: regex
    # Let's reuse the extracted CSRF token
    name: csrf_html_tag
    part: body
    # No group here
    regex:
      - '<input\sname="csrf_token"\stype="hidden"\svalue="([[:alnum:]]{16})"\s/>'
```

The above extractor with name `csrf_html_tag` will hold the full match (by `<input name="csrf_token"\stype="hidden"\svalue="([[:alnum:]]{16})" />`) as `<input name="csrf_token" type="hidden" value="abcdefgh12345678" />`.

