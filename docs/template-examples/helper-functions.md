### Helper Functions Examples

Nuclei has a number of helper functions that may be used to conduct various run-time operations on the request block. Here's an example template that shows how to use all the available helper functions.

```yaml
id: helper-functions-examples

info:
  name: RAW Template with Helper Functions
  author: pdteam
  severity: info

requests:
  - raw:
      - |
        GET / HTTP/1.1
        Host: {{Hostname}}
        01: {{len("Hello")}}
        02: {{toupper("Hello")}}
        03: {{tolower("Hello")}}
        04: {{replace("Hello", "He", "Ha")}}
        05: {{replace_regex("test", "regextomach", "replacewith")}}
        06: {{trim("aaaHelloddd", "ad")}}
        07: {{trimleft("aaaHelloddd", "ad")}}
        08: {{trimright("aaaHelloddd", "ad")}}
        09: {{trimspace("  Hello  ")}}
        10: {{trimprefix("aaHelloaa", "aa")}}
        11: {{trimsuffix("aaHelloaa", "aa")}}
        12: {{reverse("ab")}}
        13: {{base64("Hello")}}
        14: {{base64_py("Hello")}}
        15: {{base64_decode("SGVsbG8=")}}
        16: {{url_encode("hxxp://projectdiscovery.io/test?a=1")}}
        17: {{url_decode("https:%2F%2Fprojectdiscovery.io%3Ftest=1")}}
        18: {{hex_encode("aa")}}
        19: {{hex_decode("6161")}}
        20: {{html_escape("<body>test</body>")}}
        21: {{html_unescape("&lt;body&gt;test&lt;/body&gt;")}}
        22: {{md5("Hello")}}
        23: {{sha256("Hello")}}
        24: {{sha1("Hello")}}
        25: {{mmh3("Hello")}}
        26: {{contains("Hello", "lo")}}
        27: {{regex("H([a-z]+)o", "Hello")}}
        28: {{rand_char("charset", "badchars")}}
        32: {{rand_text_numeric(10, "123")}}
        31: {{rand_text_alpha(10, "aaa")}}
        30: {{rand_text_alphanumeric(10, "aaa1")}}
        29: {{rand_base(10, "aaaa", "abcd")}}
        33: {{rand_int(100, 102)}}
```