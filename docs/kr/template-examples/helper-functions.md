### 함수 도우미 예시

Nuclei에는 요청 블록에서 다양한 런타임 작업을 수행하는 데 사용할 수 있는 여러 도우미 기능이 있습니다. 다음은 사용 가능한 모든 도우미 기능을 사용하는 방법을 보여주는 예제 템플릿입니다.

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
        05: {{compare_versions('v1.0.0', '>v0.0.1', '<v1.0.1')}}
        06: {{concat("Hello", "world")}}
        07: {{contains("Hello", "lo")}}
        08: {{date_time("%Y-%M-%D")}}
        09: {{date_time("%Y-%M-%D", unix_time())}}
        10: {{date_time("%H-%m")}}
        11: {{date_time("02-01-2006 15:04")}}
        12: {{date_time("02-01-2006 15:04", unix_time())}}
        13: {{dec_to_hex(11111)}}
        14: {{generate_java_gadget("commons-collections3.1", "wget http://{{interactsh-url}}", "base64")}}
        15: {{gzip("Hello")}}
        16: {{gzip_decode(hex_decode("1f8b08000000000000fff248cdc9c907040000ffff8289d1f705000000"))}}
        17: {{hex_decode("6161")}}
        18: {{hex_encode("aa")}}
        19: {{hmac("sha1", "test", "scrt")}} 
        20: {{hmac("sha256", "test", "scrt")}}
        21: {{html_escape("<body>test</body>")}}
        22: {{html_unescape("&lt;body&gt;test&lt;/body&gt;")}}
        23: {{join("_", "hello", "world")}}
        24: {{len("Hello")}}
        25: {{len(5555)}}
        26: {{md5("Hello")}}
        27: {{md5(1234)}}
        28: {{mmh3("Hello")}}
        29: {{print_debug(1+2, "Hello")}}
        30: {{rand_base(5, "abc")}}
        31: {{rand_base(5, "")}}
        32: {{rand_base(5)}}
        33: {{rand_char("abc")}}
        34: {{rand_char("")}}
        35: {{rand_char()}}
        36: {{rand_int(1, 10)}}
        37: {{rand_int(10)}}
        38: {{rand_int()}}
        39: {{rand_ip("192.168.0.0/24")}}
        40: {{rand_ip("2002:c0a8::/24")}}
        41: {{rand_ip("192.168.0.0/24","10.0.100.0/24")}}
        42: {{rand_text_alpha(10, "abc")}}
        43: {{rand_text_alpha(10, "")}}
        44: {{rand_text_alpha(10)}}
        45: {{rand_text_alphanumeric(10, "ab12")}}
        46: {{rand_text_alphanumeric(10)}}
        47: {{rand_text_numeric(10, 123)}}
        48: {{rand_text_numeric(10)}}
        49: {{regex("H([a-z]+)o", "Hello")}}
        50: {{remove_bad_chars("abcd", "bc")}}
        51: {{repeat("a", 5)}}
        52: {{replace("Hello", "He", "Ha")}}
        53: {{replace_regex("He123llo", "(\\d+)", "")}}
        54: {{reverse("abc")}}
        55: {{sha1("Hello")}}
        56: {{sha256("Hello")}}
        57: {{to_lower("HELLO")}}
        58: {{to_upper("hello")}}
        59: {{trim("aaaHelloddd", "ad")}}
        60: {{trim_left("aaaHelloddd", "ad")}}
        61: {{trim_prefix("aaHelloaa", "aa")}}
        62: {{trim_right("aaaHelloddd", "ad")}}
        63: {{trim_space("  Hello  ")}}
        64: {{trim_suffix("aaHelloaa", "aa")}}
        65: {{unix_time(10)}}
        66: {{url_decode("https:%2F%2Fprojectdiscovery.io%3Ftest=1")}}
        67: {{url_encode("https://projectdiscovery.io/test?a=1")}}
        68: {{wait_for(1)}}
        69: {{zlib("Hello")}}
        70: {{zlib_decode(hex_decode("789cf248cdc9c907040000ffff058c01f5"))}}
        71: {{hex_encode(aes_gcm("AES256Key-32Characters1234567890", "exampleplaintext"))}}
        72: {{starts_with("Hello", "He")}}
        73: {{ends_with("Hello", "lo")}}
        74: {{line_starts_with("Hi\nHello", "He")}}
        75: {{line_ends_with("Hello\nHi", "lo")}}
```
