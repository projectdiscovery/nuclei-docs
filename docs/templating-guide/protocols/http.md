
## HTTP Requests

Nuclei offers extensive support for various features related to HTTP protocol. Raw and Model based HTTP requests are supported, along with options Non-RFC client requests support too. Payloads can also be specified and raw requests can be transformed based on payload values along with many more capabilities that are shown later on this Page.

HTTP Requests start with a `request` block which specifies the start of the requests for the template.

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

### Raw requests

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

It's possible to define placeholders with simple keywords (or using brackets {{helper_function(variable)}} in case mutator functions are needed), and perform **Sniper**, **Pitchfork** and **ClusterBomb** attacks. The wordlist for these attacks needs to be defined during the request definition under the Payload field, with a name matching the keyword, Nuclei supports both file based and in template wordlist support and Finally all DSL functionalities are fully available and supported, and can be used to manipulate the final values.

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

When using intruder, we support multiple attack types, including `sniper` which generally used to fuzz single parameter, `clusterbomb` and `pitchfork` for fuzzing multiple parameters which works same as classical burp intruder on CLI. 

##### Types of attack

  - sniper
  - pitchfork
  - clusterbomb

**Sniper:-**

> The sniper attack uses only one payload set, and it replaces only one position at a time. It loops through the payload set, first replacing only the first marked position with the payload and leaving all other positions to their original value. After its done with the first position, it continues with the second position.

**Pitchfork:-**

> The pitchfork attack type uses one payload set for each position. It places the first payload in the first position, the second payload in the second position, and so on.

> It then loops through all payload sets at the same time. The first request uses the first payload from each payload set, the second request uses the second payload from each payload set, and so on.


**Clusterbomb:-** 

> The cluster bomb attack tries all different combinations of payloads. It still puts the first payload in the first position, and the second payload in the second position. But when it loops through the payload sets, it tries all combinations.

> This attack type is useful for a brute-force attack. Load a list of commonly used usernames in the first payload set, and a list of commonly used passwords in the second payload set. The cluster bomb attack will then try all combinations.

You can read more about attack types [here](https://www.sjoerdlangkemper.nl/2017/08/02/burp-intruder-attack-types/). 

An example of the using using `clusterbomb` attack to fuzz.

```yaml
# Defining HTTP Intruder attack type

    attack: clusterbomb

    # Available attack types: sniper, pitchfork and clusterbomb
```

#### RAW HTTP Support

Nuclei also supports [rawhttp](https://github.com/projectdiscovery/rawhttp) for complete request control and customization allowing any kind of malformed requests checks for issues like HTTP request smuggling, Host header injection, CRLF with malformed characters and more. **rawhttp** library is disabled by default and can be enabled by including `unsafe: true` in the request block. 

Here is an example of HTTP request smuggling detection template using `rawhttp`. 

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

An example of the using a helper function in the header. 

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
        Authorization: Basic {{base64(username + ':' + password)}}
        User-Agent: Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/55.0
        Accept-Language: en-US,en;q=0.9
        Connection: close

    matchers:
      - type: status
        status:
          - 200
```
### Advance Fuzzing

We’ve enriched nuclei to allow advanced fuzzing of web servers. Users can now use multiple options to tune HTTP fuzzing workflows.

#### Pipelining

HTTP Pipelining support has been added which allows multiple HTTP requests to be sent on the same connection inspired from [http-desync-attacks-request-smuggling-reborn](https://portswigger.net/research/http-desync-attacks-request-smuggling-reborn).

Before running HTTP pipelining based templates, make sure the running target supports HTTP Pipeline connection, otherwise nuclei engine fallbacks to standard HTTP request engine. 

If you want to confirm the given domain or list of subdomains supports HTTP Pipelining, [httpx](https://github.com/projectdiscovery/) has a flag `-pipeline` to do so.

An example configuring showing pipelining attributes of nuclei.

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

#### Connection pooling

While the earlier versions of nuclei did not do connection pooling, users can now configure templates to either use HTTP connection pooling or not. This allows for faster scanning based on requirement.

To enable connection pooling in the template, `threads` attribute can be defined with respective number of threads you wanted to use in the payloads sections.

`Connection: Close` header can not be used in HTTP connection pooling template, otherwise engine will fail and fallback to standard HTTP requests with pooling.

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

#### Smuggling

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

**Multi request race condition testing**

For the scenario when multiple requests needs to be sent in order to exploit the race condition, we can make use of threads.

```yaml
    threads: 5
    race: true
```

`threads` is a total number of request you wanted make with the template to perform race condition testing.


Below is an example template where multiple (5) unique request will be sent at the same time using the gate logic.

```yaml
id: multi-request-race

info:
  name: Race condition testing with multiple requests
  author: pd-team
  severity: info

requests:
  - raw:  
      - |
        POST / HTTP/1.1
        Pragma: no-cache
        Host: {{Hostname}}
        Cache-Control: no-cache, no-transform
        User-Agent: Mozilla/5.0 (Windows NT 6.1; Win64; x64; rv:47.0) Gecko/20100101 Firefox/47.0

        id=1
        
      - |
        POST / HTTP/1.1
        Pragma: no-cache
        Host: {{Hostname}}
        Cache-Control: no-cache, no-transform
        User-Agent: Mozilla/5.0 (Windows NT 6.1; Win64; x64; rv:47.0) Gecko/20100101 Firefox/47.0

        id=2

      - |
        POST / HTTP/1.1
        Pragma: no-cache
        Host: {{Hostname}}
        Cache-Control: no-cache, no-transform
        User-Agent: Mozilla/5.0 (Windows NT 6.1; Win64; x64; rv:47.0) Gecko/20100101 Firefox/47.0

        id=3

      - |
        POST / HTTP/1.1
        Pragma: no-cache
        Host: {{Hostname}}
        Cache-Control: no-cache, no-transform
        User-Agent: Mozilla/5.0 (Windows NT 6.1; Win64; x64; rv:47.0) Gecko/20100101 Firefox/47.0

        id=4

      - |
        POST / HTTP/1.1
        Pragma: no-cache
        Host: {{Hostname}}
        Cache-Control: no-cache, no-transform
        User-Agent: Mozilla/5.0 (Windows NT 6.1; Win64; x64; rv:47.0) Gecko/20100101 Firefox/47.0

        id=5

    threads: 5
    race: true
```

### Matchers / Extractor Parts

Valid `part` values supported by **HTTP** protocol for Matchers / Extractor are - 

| Value                | Description                               |
|----------------------|-------------------------------------------|
| request              | Raw HTTP Request                          |
| response             | Raw HTTP Response                         |
| content_length       | HTTP Content Length                       |
| status_code          | HTTP Status Code                          |
| body                 | HTTP Response Body                        |
| header / all_headers | HTTP Response Headers                     |
| all                  | HTTP Header + Body                        |
| duration             | Time Taken for the request in seconds     |
| `header_name`        | Name of a HTTP response header with value |
| `cookie_name`        | Name of a HTTP cookie with value          |