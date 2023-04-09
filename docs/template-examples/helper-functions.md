### Helper Functions Examples

Nuclei has a number of helper functions that may be used to conduct various run-time operations on the request block. Here's an example template that shows how to use all the available helper functions.

```yaml
id: helper-functions-examples

info:
  name: RAW Template with Helper Functions
  author: pdteam
  severity: info

http:
  - raw:
      - |
        GET / HTTP/1.1
        Host: {{Hostname}}
        1: {{base64("Hello")}}
        2: {{base64(1234)}}
        3: {{base64_decode("SGVsbG8=")}}
        4: {{base64_py("Hello")}}
        5: {{compare_versions('v1.0.0', '>v0.0.1', '<v1.0.1')}}
        6: {{concat("Hello", "world")}}
        7: {{contains("Hello", "lo")}}
        8: {{contains_all("Hello everyone", "lo", "every")}}
        9: {{contains_any("Hello everyone", "abc", "llo")}}
        10: {{date_time("%Y-%M-%D")}}
        11: {{date_time("%Y-%M-%D", unix_time())}}
        12: {{date_time("%H-%m")}}
        13: {{date_time("02-01-2006 15:04")}}
        14: {{date_time("02-01-2006 15:04", unix_time())}}
        15: {{dec_to_hex(11111)}}
        16: {{generate_java_gadget("commons-collections3.1", "wget http://{{interactsh-url}}", "base64")}}
        17: {{gzip("Hello")}}
        18: {{gzip_decode(hex_decode("1f8b08000000000000fff248cdc9c907040000ffff8289d1f705000000"))}}
        19: {{hex_decode("6161")}}
        20: {{hex_encode("aa")}}
        21: {{hmac("sha1", "test", "scrt")}} 
        22: {{hmac("sha256", "test", "scrt")}}
        23: {{html_escape("<body>test</body>")}}
        24: {{html_unescape("&lt;body&gt;test&lt;/body&gt;")}}
        25: {{join("_", "hello", "world")}}
        26: {{len("Hello")}}
        27: {{len(5555)}}
        28: {{md5("Hello")}}
        29: {{md5(1234)}}
        30: {{mmh3("Hello")}}
        31: {{print_debug(1+2, "Hello")}}
        32: {{rand_base(5, "abc")}}
        33: {{rand_base(5, "")}}
        34: {{rand_base(5)}}
        35: {{rand_char("abc")}}
        36: {{rand_char("")}}
        37: {{rand_char()}}
        38: {{rand_int(1, 10)}}
        39: {{rand_int(10)}}
        40: {{rand_int()}}
        41: {{rand_ip("192.168.0.0/24")}}
        42: {{rand_ip("2002:c0a8::/24")}}
        43: {{rand_ip("192.168.0.0/24","10.0.100.0/24")}}
        44: {{rand_text_alpha(10, "abc")}}
        45: {{rand_text_alpha(10, "")}}
        46: {{rand_text_alpha(10)}}
        47: {{rand_text_alphanumeric(10, "ab12")}}
        48: {{rand_text_alphanumeric(10)}}
        49: {{rand_text_numeric(10, 123)}}
        50: {{rand_text_numeric(10)}}
        51: {{regex("H([a-z]+)o", "Hello")}}
        52: {{remove_bad_chars("abcd", "bc")}}
        53: {{repeat("a", 5)}}
        54: {{replace("Hello", "He", "Ha")}}
        55: {{replace_regex("He123llo", "(\\d+)", "")}}
        56: {{reverse("abc")}}
        57: {{sha1("Hello")}}
        58: {{sha256("Hello")}}
        59: {{to_lower("HELLO")}}
        60: {{to_upper("hello")}}
        61: {{trim("aaaHelloddd", "ad")}}
        62: {{trim_left("aaaHelloddd", "ad")}}
        63: {{trim_prefix("aaHelloaa", "aa")}}
        64: {{trim_right("aaaHelloddd", "ad")}}
        65: {{trim_space("  Hello  ")}}
        66: {{trim_suffix("aaHelloaa", "aa")}}
        67: {{unix_time(10)}}
        68: {{url_decode("https:%2F%2Fprojectdiscovery.io%3Ftest=1")}}
        69: {{url_encode("https://projectdiscovery.io/test?a=1")}}
        70: {{wait_for(1)}}
        71: {{zlib("Hello")}}
        72: {{zlib_decode(hex_decode("789cf248cdc9c907040000ffff058c01f5"))}}
        73: {{hex_encode(aes_gcm("AES256Key-32Characters1234567890", "exampleplaintext"))}}
        74: {{starts_with("Hello", "He")}}
        75: {{ends_with("Hello", "lo")}}
        76: {{line_starts_with("Hi\nHello", "He")}}
        77: {{line_ends_with("Hello\nHi", "lo")}}
        78: {{ip_format("169.254.169.254", 4)}}
```
