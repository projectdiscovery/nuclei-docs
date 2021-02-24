#### Matchers

Matchers are the core of nuclei. They are what make the tool so powerful. Multiple type of combinations and checks can be added to ensure that the results you get are free from false-positives.

##### Types

Multiple matchers can be specified in a request. There are basically 6 types of matchers:

| Matcher Type | Part Matched               |
| ------------ | -------------------------- |
| status       | Status Code of Response    |
| size         | Content Length of Response |
| word         | Response body or headers   |
| regex        | Response body or headers   |
| binary       | Response body              |
| dsl          | All Response Parts         |

To match status codes for responses, you can use the following syntax.

```yaml
matchers:
  # Match the status codes
  - type: status
    # Some status codes we want to match
    status:
      - 200
      - 302
```

To match binary for hexadecimal responses, you can use the following syntax.

```yaml
matchers:
  - type: binary
    binary:
      - "504B0304" # zip archive
      - "526172211A070100" # rar RAR archive version 5.0
      - "FD377A585A0000" # xz tar.xz archive
    condition: or
    part: body
```

To match size, similar structure can be followed. If the status code of response from the site matches any single one specified in the matcher, the request is marked as successful.

**Word** and **Regex** matchers can be further configured depending on the needs of the users.

Complex matchers of type **dsl** allows to build more elaborated expressions with helper functions, this is an example of a complex DSL matcher:

```yaml
matchers:
  - type: dsl
    dsl:
      - "len(body)<1024 && status_code==200" # Body length less than 1024 and 200 status code
      - "contains(toupper(body), md5(cookie))" # Check if the MD5 sum of cookies is contained in the uppercase body
```

Every part of a HTTP response can be matched with DSL matcher:

| Response Part  | Description                                     | Example                |
| -------------- | ----------------------------------------------- | ---------------------- |
| content_length | Content-Length Header                           | content_length >= 1024 |
| status_code    | Response Status Code                            | status_code==200       |
| all_headers    | Unique string containing all headers            | len(all_headers)       |
| body           | Body as string                                  | len(body)              |
| header_name    | Lowercase header name with `-` converted to `_` | len(user_agent)        |
| raw            | Headers + Response                              | len(raw)               |


##### Conditions

Multiple words and regexes can be specified in a single matcher and can be configured with different conditions like **AND** and **OR**.

1. **AND** - Using AND conditions allows matching of all the words from the list of words for the matcher. Only then will the request be marked as successful when all the words have been matched.
2. **OR** - Using OR conditions allows matching of a single word from the list of matcher. The request will be marked as successful when even one of the word is matched for the matcher.

##### Matched Parts

Multiple parts of the response can also be matched for the request, default matched part is `body` if not defined. 

| Part   | Matched Part                         |
| ------ | ------------------------------------ |
| body   | Body of the response                 |
| header | Header of the response               |
| all    | Both body and header of the response |

Example matchers for response body using the AND condition:

```yaml
matchers:
  # Match the body word
  - type: word
   # Some words we want to match
   words:
     - "[core]"
     - "[config]"
   # Both words must be found in the response body
   condition: and
   #  We want to match request body (default)
   part: body
```

Similarly, matchers can be written to match anything that you want to find in the response body allowing unlimited creativity and extensibility.


##### Negative Matchers

All types of matchers also support negative/opposite conditions, mostly useful when you looking to match with exclusions, it can be used by adding `negative: true` in the **matchers** block.

Here is an example syntax using `negative` condition, this will return all the URLs not having `PHPSESSID` in the response header. 

```yaml
matchers:
  - type: word
    words:
      - "PHPSESSID"
    part: header
    negative: true
```

In same manner, `negative` condition can be used with single or multiple matchers.

##### Multiple Matchers

Multiple matchers can be used in a single template to fingerprint multiple conditions with a single request.

Here is an example of syntax for multiple matchers.

```yaml
matchers:
  - type: word
    name: php
    words:
      - "X-Powered-By: PHP"
      - "PHPSESSID"
    part: header
  - type: word
    name: node
    words:
      - "Server: NodeJS"
      - "X-Powered-By: nodejs"
    condition: or
    part: header
  - type: word
    name: python
    words:
      - "Python/2."
      - "Python/3."
    condition: or
    part: header
```

##### Matchers Condition

While using multiple matchers the default condition is to follow OR operation in between all the matchers, AND operation can be used to make sure return the result if all matchers returns true.

```yaml
    matchers-condition: and
    matchers:
      - type: word
        words:
          - "X-Powered-By: PHP"
          - "PHPSESSID"
        condition: or
        part: header

      - type: word
        words:
          - "PHP"
        part: body
```