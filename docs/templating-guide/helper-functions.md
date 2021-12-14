#### Helper functions

Here is the list of all supported helper functions can be used in the RAW requests / Network requests.

| Helper function         | Description                                                                                                                                | Example                                                |
| ----------------------- | ------------------------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------ |
| len                     | Length of a string                                                                                                                         | len("Hello")                                           |
| toupper                 | String to uppercase                                                                                                                        | toupper("Hello")                                       |
| tolower                 | String to lowercase                                                                                                                        | tolower("Hello")                                       |
| replace                 | Replace string parts                                                                                                                       | replace("Hello", "He", "Ha")                           |
| replace_regex           | Replace string parts with regex                                                                                                            | replace_regex("test", "regextomach", "replacewith")    |
| trim                    | Remove trailing unicode chars                                                                                                              | trim("aaaHelloddd", "ad")                              |
| trimleft                | Remove unicode chars from left                                                                                                             | trimleft("aaaHelloddd", "ad")                          |
| trimright               | Remove unicode chars from right                                                                                                            | trimleft("aaaHelloddd", "ad")                          |
| trimspace               | Remove trailing spaces                                                                                                                     | trimspace("  Hello  ")                                 |
| trimprefix              | Trim specified prefix                                                                                                                      | trimprefix("aaHelloaa", "aa")                          |
| trimsuffix              | Trim specified suffix                                                                                                                      | trimsuffix("aaHelloaa", "aa")                          |
| reverse                 | Reverse the string                                                                                                                         | reverse("ab")                                          |
| base64                  | Encode string to base64                                                                                                                    | base64("Hello")                                        |
| base64_py               | Encode string to base64 like python (with new lines)                                                                                       | base64_py("Hello")                                     |
| base64_decode           | Decode string from base64                                                                                                                  | base64_decode("SGVsbG8=")                              |
| url_encode              | URL encode a string                                                                                                                        | url_encode("hxxps://projectdiscovery.io/test?a=1")     |
| url_decode              | URL decode a string                                                                                                                        | url_decode("https:%2F%2Fprojectdiscovery.io%3Ftest=1") |
| hex_encode              | Hex encode a string                                                                                                                        | hex_encode("aa")                                       |
| hex_decode              | Hex decode a string                                                                                                                        | hex_decode("6161")                                     |
| html_escape             | HTML escape a string                                                                                                                       | html_escape("<body>test</body>")                       |
| html_unescape           | HTML unescape a string                                                                                                                     | html_unescape("&lt;body&gt;test&lt;/body&gt;")         |
| md5                     | Calculate md5 of string                                                                                                                    | md5("Hello")                                           |
| sha256                  | Calculate sha256 of string                                                                                                                 | sha256("Hello")                                        |
| sha1                    | Calculate sha1 of string                                                                                                                   | sha1("Hello")                                          |
| mmh3                    | Calculate mmh3 of string                                                                                                                   | mmh3("Hello")                                          |
| contains                | Verify if a string contains another one                                                                                                    | contains("Hello", "lo")                                |
| regex                   | Verify a regex versus a string                                                                                                             | regex("H([a-z]+)o", "Hello")                           |
| rand_char               | Pick a random char among charset (optional, default letters and numbers) avoiding badchars (optional, default empty)                       | rand_char("charset", "badchars")                       |   
| rand_char               | Pick a random sequence of length l among charset (optional, default to letters and numbers) avoiding badchars (optional, default empty)    | rand_base(l, "charset", "badchars")                    |
| rand_text_alphanumeric  | Pick a random sequence of length l among letters and numbers avoiding badchars (optional)                                                  | rand_text_alphanumeric(l, "badchars")                  |
| rand_text_alpha         | Pick a random sequence of length l among letters avoiding badchars                                                                         | rand_text_alpha(l, "charset")                          |
| rand_text_numeric       | Pick a random sequence of length l among numbers avoiding badchars                                                                         | rand_text_numeric(l, "charset")                        |
| rand_int                | Pick a random integer between min and max                                                                                                  | rand_int(min, max)                                     |
| waitfor                 | block the logic execution for x seconds                                                                                                    | waitfor(10)                                            |


#### Deserialization helper functions

Nuclei allows payload generation for a few common gadget from [ysoserial](https://github.com/frohoff/ysoserial).

**Supported Payload:**

- dns (URLDNS)
- commons-collections3.1
- commons-collections4.0
- jdk7u21
- jdk8u20
- groovy1

**Supported encodings:**

- base64 (default)
- gzip-base64
- gzip
- hex
- raw

**Deserialization helper function format:**

```sh
{{generate_java_gadget(payload, cmd, encoding}}
```

**Deserialization helper function example:**

```sh
{{generate_java_gadget("commons-collections3.1", "wget http://{{interactsh-url}}", "base64")}}
```





