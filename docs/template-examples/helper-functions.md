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
        01: {{base64("Hello")}}
        02: {{base64(1234)}}
        03: {{base64_decode("SGVsbG8=")}}
        04: {{base64_py("Hello")}}
        05: {{concat("Hello", "world")}}
        06: {{contains("Hello", "lo")}}
        07: {{generate_java_gadget("commons-collections3.1", "wget http://{{interactsh-url}}", "base64")}}
        08: {{gzip("Hello")}}
        09: {{hex_decode("6161")}}
        10: {{hex_encode("aa")}}
        11: {{html_escape("<body>test</body>")}}
        12: {{html_unescape("&lt;body&gt;test&lt;/body&gt;")}}
        13: {{len("Hello")}}
        14: {{len(5555)}}
        15: {{md5("Hello")}}
        16: {{md5(1234)}}
        17: {{mmh3("Hello")}}
        18: {{print_debug(1+2, "Hello")}}
        19: {{rand_base(5, "abc")}}
        20: {{rand_base(5, "")}}
        21: {{rand_base(5)}}
        22: {{rand_char("abc")}}
        23: {{rand_char("")}}
        24: {{rand_char()}}
        25: {{rand_int(1, 10)}}
        26: {{rand_int(10)}}
        27: {{rand_int()}}
        28: {{rand_text_alpha(10, "abc")}}
        29: {{rand_text_alpha(10, "")}}
        30: {{rand_text_alpha(10)}}
        31: {{rand_text_alphanumeric(10, "ab12")}}
        32: {{rand_text_alphanumeric(10)}}
        33: {{rand_text_numeric(10, 123)}}
        34: {{rand_text_numeric(10)}}
        35: {{regex("H([a-z]+)o", "Hello")}}
        36: {{remove_bad_chars("abcd", "bc")}}
        37: {{repeat("a", 5)}}
        38: {{replace("Hello", "He", "Ha")}}
        39: {{replace_regex("He123llo", "(\\d+)", "")}}
        40: {{reverse("abc")}}
        41: {{sha1("Hello")}}
        42: {{sha256("Hello")}}
        43: {{to_lower("HELLO")}}
        44: {{to_upper("hello")}}
        45: {{trim("aaaHelloddd", "ad")}}
        46: {{trim_left("aaaHelloddd", "ad")}}
        47: {{trim_prefix("aaHelloaa", "aa")}}
        48: {{trim_right("aaaHelloddd", "ad")}}
        49: {{trim_space("  Hello  ")}}
        50: {{trim_suffix("aaHelloaa", "aa")}}
        51: {{unix_time(10)}}
        52: {{url_decode("https:%2F%2Fprojectdiscovery.io%3Ftest=1")}}
        53: {{url_encode("https://projectdiscovery.io/test?a=1")}}
        54: {{wait_for(1)}}
        55: {{gzip_decode(hex_decode("1f8b08000000000000fff248cdc9c907040000ffff8289d1f705000000"))}}
        56: {{zlib("Hello")}}
        57: {{zlib_decode(hex_decode("789cf248cdc9c907040000ffff058c01f5"))}}
        58: {{compare_versions('v1.0.0', '>v0.0.1', '<v1.0.1')}}
        59: {{dec_to_hex(11111)}}
        60: {{join("_", 123, "hello", "world")}}
        61: {{hmac("sha1", "test", "scrt")}}
        62: {{hmac("sha256", "test", "scrt")}}
        63: {{date_time("%Y-%M-%D")}}
        64: {{date_time("%Y-%M-%D", unix_time())}}
        65: {{date_time("%H:%m")}}
```
