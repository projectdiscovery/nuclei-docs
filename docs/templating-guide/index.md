# Templating Guide

**Nuclei** is based on the concepts of `YAML` based template files that define how the requests will be sent and processed. This allows easy extensibility capabilities to nuclei.

The templates are written in `YAML` which specifies a simple human readable format to quickly define the execution process.

**Guide to write your own nuclei template -**

----------

Let's start with the basics and define our own workflow file for detecting the presence of a `.git/config` file on a webserver and take it from there.

## Template Details

Each template has a unique ID which is used during output writing to specify the template name for an output line.

The template file ends with **yaml** extension. The template files can be created any text editor of your choice.

```yaml
# id contains the unique identifier for the template.
id: git-config
```

ID must not contain spaces. This is done to allow easier output parsing.

### Information

Next important piece of information about a template is the **info** block. Info block provides more context on the purpose of the template and the **author**. It also contain **severity** field which indicates the severity of the template, **info** block supports dynamic fields, so one can define N number of `key: value` formatted fields to make template information more relevant for the checks you are writing.

Let's add an **info** block to our template as well.

```yaml
info:
  # Name is the name of the template
  name: Git Config File Detection Template
  # Author is the name of the author for the template
  author: Ice3man
  # Severity is the severity for the check.
  severity: medium
  # Description optionally describes the template.
  description: Searches for the pattern /.git/config on passed URLs.
  # CVSS is impact score of the check.
  cvss: 4.6 
```

Actual requests and corresponding matchers are placed below the info block and they perform the task of making requests to target servers and finding if the template request was successful.

Each template file can contain multiple requests to be made. The template is iterated and one by one the desired HTTP/DNS requests are made to the target sites.

## HTTP Requests

Requests start with a request block which specifies the start of the requests for the template.

```yaml
# Start the requests for the template right here
requests:
```

#### Method

First thing in the request is **method**. Request method can be **GET**, **POST**, **PUT**, **DELETE**, etc depending on the needs.

```yaml
# Method is the method for the request
method: GET
```

#### Redirects

Redirection conditions can be specified per each template. By default, redirects are not followed. However, if desired, they can be enabled with `redirects: true` in request details. 10 redirects are followed at maximum by default which should be good enough for most use cases. More fine grained control can be exercised over number of redirects followed by using `max-redirects` field.

An example of the usage:

```yaml
requests:
  - method: GET
    path:
      - "{{BaseURL}}/login.php"
    redirects: true
    max-redirects: 3
```

#### Path

The next part of the requests is the **path** of the request path. Dynamic variables can be placed in the path to modify its behavior on runtime. Variables start with `{{` and end with `}}` and are case-sensitive.

1. **BaseURL** - Placing BaseURL as a variable in the path will lead to it being replaced on runtime in the request by the original URL as specified in the target file.
2. **Hostname** - Hostname variable is replaced by the hostname of the target on runtime.

Some sample dynamic variable replacement examples:

```yaml
path: "{{BaseURL}}/.git/config"
# This path will be replaced on execution with BaseURL
# If BaseURL is set to  https://abc.com then the
# path will get replaced to the following: https://abc.com/.git/config
```

Multiple paths can also be specified in one request which will be requested for the target.

#### Headers

Headers can also be specified to be sent along with the requests. Headers are placed in form of key/value pairs. An example header configuration looks like this:

```yaml
# headers contains the headers for the request
headers:
  # Custom user-agent header
  User-Agent: Some-Random-User-Agent
  # Custom request origin
  Origin: https://google.com
```

#### Body

Body specifies a body to be sent along with the request. For instance:

```yaml
# Body is a string sent along with the request
body: "{\"some random JSON\"}"

# Body is a string sent along with the request
body: "admin=test"
```

#### Session

To maintain cookie based browser like session between multiple requests, you can simply use `cookie-reuse: true` in your template, Useful in cases where you want to maintain session between series of request to complete the exploit chain and to perform authenticated scans. 

```yaml
# cookie-reuse accepts boolean input and false as default
cookie-reuse: true
```

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



This is the list for a DNS response supported by DSL matcher:

| Response Part | Description                | Example            |
| ------------- | -------------------------- | ------------------ |
| rcode         | Response status            | rcode == "NXDOMAIN |
| question      | Response question section  | len(question)      |
| extra         | Response extra section     | len(extra)         |
| answer        | Response answers section   | len(answer)        |
| ns            | Response authority section | len(ns)            |
| raw           | Full response              | len(raw)           |


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


#### **Example HTTP Template**

The final template file for the `.git/config` file mentioned above is as follows:

```yaml
id: git-config

info:
  name: Git Config File
  author: Ice3man
  severity: medium
  description: Searches for the pattern /.git/config on passed URLs.

requests:
  - method: GET
    path:
      - "{{BaseURL}}/.git/config"
    matchers:
      - type: word
        words:
          - "[core]"
```

### HTTP Raw requests

Another way to create request is using raw requests which comes with more flexibility and support of DSL helper functions, like the following ones (as of now it's suggested to leave the `Host` header as in the example with the variable `{{Hostname}}`), All the Matcher, Extractor capabilities can be used with RAW requests in same the way described above. 

```yaml
requests:
  - raw:
    - |
        POST /path2/ HTTP/1.1
        Host: {{Hostname}}
        Content-Length: 1
        Origin: https://www.google.com
        Content-Type: application/x-www-form-urlencoded
        User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_5) AppleWebKit/537.36 (KHTML, like Gecko)
        Accept-Language: en-US,en;q=0.9

        a=test&b=pd
```

Requests can be fine tuned to perform the exact tasks as desired. Nuclei requests are fully configurable meaning you can configure and define each and every single thing about the requests that will be sent to the target servers.  Here follows an example:


#### Intruder payloads

It's possible to define placeholders with simple keywords (or using brackets {{helper_function(keyword)}} in case mutator functions are needed), and perform **Sniper**, **Pitchfork** and **ClusterBomb** attacks. The wordlist for these attacks needs to be defined during the request definition under the Payload field, with a name matching the keyword, Nuclei supports both file based and in template wordlist support and Finally all DSL functionalities are fully available and supported, and can be used to manipulate the final values.

Payloads are defined using variable name and can be referenced in the request between `§` marker, for example **§variable_name§**.

An example of the using payloads with local wordlist:

```yaml
requests:

# HTTP Intruder fuzzing using local wordlist.

  - payloads:
      parameter: params.txt
      header: local.txt

```

An example of the using payloads with in template wordlist support:

```yaml
requests:

# HTTP Intruder fuzzing using in template wordlist.

requests:

  - payloads:

      password:
        - admin
        - guest
        - password
        - test
        - 12345
        - 123456
```

**Note:-** be careful while selecting attack type, as unexpected input will break the template. 

For example, if you used `clusterbomb` or `pitchfork` as attack type and defined only one variable in the payload section, template will fail to compile, as `clusterbomb` or `pitchfork` expect more then one variable to use in the template. 

#### Intruder attack 

For using intruder, we support multiple attack types, including `sniper` which generally used to fuzz single parameter, `clusterbomb` and `pitchfork` for fuzzing multiple parameters which works same as classical burp intruder on CLI. 

An example of the using using `clusterbomb` attack to fuzz. 

```yaml
# Defining HTTP Intruder attack type

    attack: clusterbomb

    # Available attack types: sniper, pitchfork and clusterbomb
```

#### RAW HTTP Support

Nuclei also supports [rawhttp](https://github.com/projectdiscovery/rawhttp) for complete request control and customization allowing any kind of malformed requests checks for issues like HTTP request smuggling, Host header injection, CRLF with malformed characters and more, **rawhttp** library is disabled as default and can be used by including `unsafe: true` in the request block. 

Here is an example for HTTP request smuggling detection using `rawhttp`. 

```yaml
requests:
  - raw:
    - |
        POST / HTTP/1.1
        Host: {{Hostname}}
        Content-Type: application/x-www-form-urlencoded
        Content-Length: 150
        Transfer-Encoding: chunked

        0

        GET /post?postId=5 HTTP/1.1
        User-Agent: a"/><script>alert(1)</script>
        Content-Type: application/x-www-form-urlencoded
        Content-Length: 5

        x=1
    - |
        GET /post?postId=5 HTTP/1.1
        Host: {{Hostname}}

    unsafe: true  # enables rawhttp client
    matchers:
      - type: dsl
        dsl:
          - 'contains(body, "<script>alert(1)</script>")'
```

Using `rawhttp` also enable automatic handling of `host` header and `content-length` header, to disable this behavior, `disable-automatic-content-length-header` and  `disable-automatic-host-header` can be used in following manner. 

```
requests:
  - raw:
    - |
        POST / HTTP/1.1
        Host: {{Hostname}}
        Content-Type: application/x-www-form-urlencoded
        Content-Length: 150
        Transfer-Encoding: chunked

        0

    unsafe: true  # enables rawhttp client
    disable-automatic-content-length-header: true
    disable-automatic-host-header: true
    matchers:
      - type: dsl
        dsl:
          - 'contains(body, "<script>alert(1)</script>")'
```


#### Helper functions

Here is the list of all supported helper functions can be used in the RAW requests:

| Helper function | Description                             | Example                                                |
| --------------- | --------------------------------------- | ------------------------------------------------------ |
| len             | Length of a string                      | len("Hello")                                           |
| toupper         | String to uppercase                     | toupper("Hello")                                       |
| tolower         | String to lowercase                     | tolower("Hello")                                       |
| replace         | Replace string parts                    | replace("Hello", "He", "Ha")                           |
| trim            | Remove trailing unicode chars           | trim("aaaHelloddd", "ad")                              |
| trimleft        | Remove unicode chars from left          | trimleft("aaaHelloddd", "ad")                          |
| trimright       | Remove unicode chars from right         | trimleft("aaaHelloddd", "ad")                          |
| trimspace       | Remove trailing spaces                  | trimspace("  Hello  ")                                 |
| trimprefix      | Trim specified prefix                   | trimprefix("aaHelloaa", "aa")                          |
| trimsuffix      | Trim specified suffix                   | trimsuffix("aaHelloaa", "aa")                          |
| base64          | Encode string to base64                 | base64("Hello")                                        |
| base64_decode   | Decode string from base64               | base64_decode("SGVsbG8=")                              |
| url_encode      | URL encode a string                     | url_encode("hxxps://projectdiscovery.io/test?a=1")     |
| url_decode      | URL decode a string                     | url_decode("https:%2F%2Fprojectdiscovery.io%3Ftest=1") |
| hex_encode      | Hex encode a string                     | hex_encode("aa")                                       |
| hex_decode      | Hex decode a string                     | hex_decode("6161")                                     |
| html_escape     | Hex encode a string                     | html_escape("<body>test</body>")                       |
| html_unescape   | Hex decode a string                     | html_unescape("&lt;body&gt;test&lt;/body&gt;")         |
| md5             | Calculate md5 of string                 | md5("Hello")                                           |
| sha256          | Calculate sha256 of string              | sha256("Hello")                                        |
| sha1            | Calculate sha1 of string                | sha1("Hello")                                          |
| contains        | Verify if a string contains another one | contains("Hello", "lo")                                |
| regex           | Verify a regex versus a string          | regex("H([a-z]+)o", "Hello")                           |


An example of the using helper function in the header. 

```yaml
    raw:
      # Request with simple header manipulation with DSL functions
      - |
        GET /manager/html HTTP/1.1
        Host: {{Hostname}}
        Authorization: Basic {{base64('username:password')}}
        User-Agent: Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/55.0
        Accept-Language: en-US,en;q=0.9
        Connection: close
```

#### **Example RAW Template**

```yaml
id: http-raw-request
info:
  name: Example-Fuzzing

requests:

  - payloads:
      username:
        - admin

      password:
        - admin
        - guest
        - password
        - test
        - 12345
        - 123456

    attack: clusterbomb

    # Supported attack types: sniper, pitchfork and clusterbomb

    raw:
      # Request with simple header manipulation with DSL functions
      - |
        GET /manager/html HTTP/1.1
        Host: {{Hostname}}
        Authorization: Basic {{base64('username:password')}}
        User-Agent: Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/55.0
        Accept-Language: en-US,en;q=0.9
        Connection: close

    matchers:
      - type: status
        status:
          - 200
```

### DNS Requests

Requests start with a dns block which specifies the start of the requests for the template.

```yaml
# Start the requests for the template right here
dns:
```

DNS requests can be fine tuned to perform the exact tasks as desired. Nuclei requests are fully configurable meaning you can configure and define each and every single thing about the requests that will be sent to the target servers.

#### Type

First thing in the request is **type**. Request type can be **A**, **NS**, **CNAME**, **SOA**, **PTR**, **MX**, **TXT**, **AAAA**.

```yaml
# type is the type for the dns request
type: A
```

#### Name

The next part of the requests is the **name** of the request path. Dynamic variables can be placed in the path to modify its value on runtime. Variables start with `{{` and end with `}}` and are case-sensitive.

1. **FQDN** - variable is replaced by the hostname/FQDN of the target on runtime.

Some sample dynamic variable replacement examples:

```yaml
name: {{FQDN}}.com
# This value will be replaced on execution with the FQDN.
# If FQDN is https://this.is.an.example then the
# name will get replaced to the following: this.is.an.example.com
```

As of now the tool supports only one question per request.

#### Class

Class type can be **INET**, **CSNET**, **CHAOS**, **HESIOD**, **NONE** and **ANY**. Usually it's enough to just leave it as **INET**.

```yaml
# method is the class for the dns request
class: inet
```

#### Recursion

Recursion is a boolean value, and determines if the resolver should only return cached results, or traverse the whole dns root tree to retrieve fresh results. Generally it's better to leave it as **true**.

```yaml
# Recursion is a boolean determining if the request is recursive
recursion: true
```

#### Retries

Retries is the number of attempts a dns query is retried before giving up among different resolvers. It's recommended a reasonable value, like **3**.

```yaml
# Retries is a number of retries before giving up on dns resolution
retries: 3
```

#### Matchers

Matchers are just equal to HTTP, but the search is performed on the whole dns response, therefore it's not necessary to specify the **part**. Multiple type of combinations and checks can be added to ensure that the results you get are free from false positives. The complex **dsl** matcher type allows to build complex queries as described in the HTTP section.

##### Types

Multiple matchers can be specified in a request. There are basically 3 types of matchers:

| Matcher Type | Part Matched |
| ------------ | ------------ |
| word         | DNS Response |
| regex        | DNS Response |
| dsl          | DNS Response |

#### **Example DNS Template**

The final example template file for performing `A` query, and check if CNAME and A records are in the response is as follows:

```yaml
id: dummy-cname-a

info:
  name: Dummy A dns request
  author: mzack9999
  severity: none
  description: Checks if CNAME and A record is returned.

dns:
  - name: "{{FQDN}}"
    type: A
    class: inet
    recursion: true
    retries: 3
    matchers:
      - type: word
        words:
          # The response must contains a CNAME record
          - "IN\tCNAME"
          # and also at least 1 A record
          - "IN\tA"
        condition: and
```

### Workflows

Workflows are powerful utility of nuclei and most efficient way to use nuclei templates where all the templates can be configured based on specific needs of users, one can configure a workflow in a way that ensures / control complete nuclei templates checks from a single workflow, the use cases varies depending upon the users needs.

[Tengo](https://github.com/d5/tengo), a scripting engine is used as a base for nuclei workflows which provides a very powerful and highly customizable engine for users to automate all their needs.

Workflows can be defined with `workflows` attribute, following the `template` or `subtemplates` to executes, and we can write two type of workflows. 

```yaml
# Workflows starts form this block.
workflows:
  - template: technologies/template-to-execute.yaml
```



**Type of workflows**

1. Generic workflows
2. Conditional workflows

#### Generic Workflows

In generic workflow one can define a single or multiple template executions from a single workflow, it can be list of specific templates or list of directory containing multiple templates.

For example:- 

```yaml
# A workflow that runs all config related templates on the list of give URLs.

workflows:
  - template: files/git-config.yaml
  - template: files/svn-config.yaml
  - template: files/env-file.yaml
  - template: files/backup-files.yaml
```

```yaml
# A workflow that runs specific list of checks defined for your project.

workflows:
  - template: cves/
  - template: files/
  - template: toknes/
  - template: mis-configs/
```



#### Conditional Workflows

You can also create conditional templates which executes after matching the condition from the previous templates, mostly useful for vulnerability detection and exploitation and tech based detection and exploitation, single, multiple along with directory based templates can be executed in chained workflow template. 


**Templates based conditional check**

For example:- 

```yaml
# A workflow that executes subtemplates when base template gets matched.

workflows:
  - template: technologies/jira-detect.yaml
    subtemplates:
      - template: exploits/jira-exploit-1.yaml
      - template: exploits/jira-exploit-1.yaml
```

**Templates and matcher name based conditional check**

For example:- 


```yaml
# A workflow that executes subtemplates when a matcher of base template gets matched.

workflows:
  - template: technologies/tech-detect.yaml
    matchers:
      - name: vbulletin
        subtemplates:
          - template: exploits/vbulletin-exp1.yaml
          - template: exploits/vbulletin-exp2.yaml
      - name: jboss
        subtemplates:
          - template: exploits/jboss-exp1.yaml
          - template: exploits/jboss-exp2.yaml
```

In similar manner, one can create as much conditions you want to execute specific templates based on certain conditions.

**Templates and matcher name based multi level conditional check**

For example:- 

```yaml
# A workflow showcasing chain of template executions that runs only if the previous templates get matched.

workflows:
  - template: technologies/tech-detect.yaml
    matchers:
      - name: lotus-domino
        subtemplates:
          - template: technologies/lotus-domino-version.yaml
            subtemplates:
              - template: cves/xx-yy-zz.yaml
                subtemplates:
                  - template: cves/xx-xx-xx.yaml
```

Conditional workflows are great examples of performing checks and vulnerability detection in most efficient manner instead of spraying all the templates on your all the targets, and generally comes of with good ROI of your time and good for the target projects and websites as well.


### Advance Fuzzing

We’ve enriched nuclei to allow advanced fuzzing of web servers. Users can now use multiple options to tune HTTP fuzzing workflows.

#### HTTP Pipelining

HTTP Pipelining support has been added which allows multiple HTTP requests to be sent on the same connection inspired from [http-desync-attacks-request-smuggling-reborn](https://portswigger.net/research/http-desync-attacks-request-smuggling-reborn).

Before running HTTP pipelining based templates, make sure the running target supports HTTP Pipeline connection, otherwise nuclei engine fallback to stranded HTTP request. 

If you wanted to confirm the given domain or list of subdomains supports HTTP Pipelining? [httpx](https://github.com/projectdiscovery/) has a flag `-pipeline` to probe it.

An example configuring pipelining attributes of nuclei template.

```yaml
    unsafe: true
    pipeline: true
    pipeline-max-connections: 40
    pipeline-max-workers: 25000
```


An example template demonstrating pipelining capabilities of nuclei has been provided below-

```yaml
id: pipeline-testing
info:
  name: pipeline testing
  author: pdteam
  severity: info

requests:

  - payloads:
      path: path_wordlist.txt

    attack: sniper
    unsafe: true
    pipeline: true
    pipeline-max-connections: 40
    pipeline-max-workers: 25000

    raw:
      - |
        GET /§path§ HTTP/1.1
        Host: {{Hostname}}
        User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10.15; rv:79.0) Gecko/20100101 Firefox/79.0
        Accept: application/json, text/plain, */*
        Accept-Language: en-US,en;q=0.5
        Referer: {{BaseURL}}
        Connection: keep-alive        

    matchers:
      - type: status
        part: header
        status:
          - 200
```

#### HTTP Connection pooling

While the earlier versions of nuclei did not do connection pooling, users can now configure templates to either use HTTP connection pooling or not. This allows for faster scanning based on requirement.

To enable connection pooling in the template, `threads` attribute can be defined with respective number of threads you wanted to use in the payloads sections.

`Connection: Close` header can not be used in HTTP connection pooling template, otherwise engine will fail and fallback to standard HTTP requests.

An example template using HTTP connection pooling-

```yaml
id: fuzzing-example
info:
  name: Connection pooling example
  author: pdteam
  severity: info

requests:
  - payloads:
      password: password.txt

    threads: 40
    attack: sniper

    raw:
      - |
        GET /protected HTTP/1.1
        Host: {{Hostname}}
        Authorization: Basic {{base64('admin:§password§')}}
        User-Agent: Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/55.0
        Accept-Language: en-US,en;q=0.9

    matchers-condition: and
    matchers:
      - type: status
        status:
          - 200

      - type: word
        words:
          - "Unique string"
        part: body    
```

#### HTTP Smuggling

HTTP Smuggling is a class of Web-Attacks recently made popular by [Portswigger’s Research](https://portswigger.net/research/http-desync-attacks-request-smuggling-reborn) into the topic. For an in-depth overview, please visit the article linked above.

In the open source space, detecting http smuggling is difficult particularly due to the requests for detection being malformed by nature. Nuclei is able to reliably detect HTTP Smuggling vulnerabilities utilising the [rawhttp](https://github.com/projectdiscovery/rawhttp) engine.

The most basic example of a HTTP Smuggling vulnerability is CL.TE Smuggling. An example template to detect a CE.TL HTTP Smuggling vulnerability is provided below using the `unsafe: true` attribute for rawhttp based requests.

```yaml
id: CL.TE-http-smuggling

info:
  name: HTTP request smuggling, basic CL.TE vulnerability
  author: pdteam
  severity: info
  lab: https://portswigger.net/web-security/request-smuggling/lab-basic-cl-te

requests:
  - raw:
    - |
      POST / HTTP/1.1
      Host: {{Hostname}}
      Connection: keep-alive
      Content-Type: application/x-www-form-urlencoded
      Content-Length: 6
      Transfer-Encoding: chunked
      
      0
      
      G      
    - |
      POST / HTTP/1.1
      Host: {{Hostname}}
      Connection: keep-alive
      Content-Type: application/x-www-form-urlencoded
      Content-Length: 6
      Transfer-Encoding: chunked
      
      0
      
      G
            
    unsafe: true
    matchers:
      - type: word
        words:
          - 'Unrecognized method GPOST'
```

More examples are available in [template-examples](https://nuclei.projectdiscovery.io/template-examples/smuggling/
) section for smuggling templates.


#### Race conditions

Race Conditions are another class of bugs not easily automated via traditional tooling. Burp Suite introduced a Gate mechanism to Turbo Intruder where all the bytes for all the requests are sent expect the last one at once which is only sent together for all requests synchronizing the send event.

We have implemented **Gate** mechanism in nuclei engine and allow them run via templates which makes the testing for this specfic bug class simple and portable.

To enable race condition check within template, `race` attribute can be set to `true` and `race_count` defines the number of simultaneous request you want to initiate.

Below is an example template where the same request is repeated for 10 times using the gate logic.

```yaml
id: race-condition-testing

info:
  name: Race condition testing
  author: pdteam
  severity: info

requests:
  - raw:
      - |
        POST /coupons HTTP/1.1
        Host: {{Hostname}}
        Pragma: no-cache
        Cache-Control: no-cache, no-transform
        User-Agent: Mozilla/5.0 (Windows NT 6.1; Win64; x64; rv:47.0) Gecko/20100101 Firefox/47.0

        promo_code=20OFF        

    race: true
    race_count: 10

    matchers:
      - type: status
        part: header
        status:
          - 200
```

You can simply replace the `POST` request with any suspected vulnerable request and change the `race_count` as per your need and it's ready to run.

```bash
nuclei -t race.yaml -target https://api.target.com
```

The best part of this is you can simply share your crafted template with your team mates, triage/security team to replicate the issue on the other side with ease.


