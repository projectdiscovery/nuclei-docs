#### Helper functions

Here is the list of all supported helper functions can be used in the RAW requests / Network requests.

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

