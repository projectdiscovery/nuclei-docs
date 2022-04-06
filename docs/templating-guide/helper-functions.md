#### Helper functions

Here is the list of all supported helper functions can be used in the RAW requests / Network requests.

| Helper function                                                       | Description                                                                                                               | Example                                                                                                  |
| --------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------| -------------------------------------------------------------------------------------------------------- |
| base64(src interface{}) string                                        | Base64 encodes a string                                                                                                   | `base64("Hello")` // SGVsbG8=                                                                            |
| base64_decode(src interface{}) []byte                                 | Base64 decodes a string                                                                                                   | `base64_decode("SGVsbG8=")` // [72 101 108 108 111]                                                      |
| base64_py(src interface{}) string                                     | Encodes string to base64 like python (with new lines)                                                                     | `base64_py("Hello")` // SGVsbG8=\n                                                                       |
| concat(arguments ...interface{}) string                               | Concatenates the given number of arguments to form a string                                                               | `concat("Hello", 123, "world)` // Hello123world                                                          |
| compare_versions(versionToCheck string, constraints ...string) bool                               | Compares the first version argument with the provided constraints                                                              | `compare_versions('v1.0.0', '>v0.0.1,<v1.0.1')` // true                                                          |

| contains(input, substring interface{}) bool                           | Verifies if a string contains a substring                                                                                 | `contains("Hello", "lo")` // true                                                                        |
| generate_java_gadget(gadget, cmd, encoding interface{}) string        | Generates a Java Deserialization Gadget                                                                                   | `generate_java_gadget("commons-collections3.1", "wget http://{{interactsh-url}}", "base64")`             |
| gzip(input string) string                                             | Compresses the input using GZip                                                                                           | `gzip("Hello")`                                                                                          |
| gzip_decode(input string) string | Decompresses the input using GZip | `gzip_decode(hex_decode("1f8b08000000000000fff248cdc9c907040000ffff8289d1f705000000"))` // Hello | 
| zlib(input string) string | Compresses the input using Zlib | `zlib("Hello")` | 
| zlib_decode(input string) string | Decompresses the input using Zlib | `zlib_decode(hex_decode("789cf248cdc9c907040000ffff058c01f5"))` // Hello | 
| date(input string) string | Returns a formatted date string | `date("%Y-%M-%D")` // 2022-05-01 |
| time(input string) string | Returns a formatted time string | `time("%H-%M")` // 22-12 |
| timetostring(input int) string | Returns a formatted unix time string | `timetostring(1647861438)` // 2022-03-21 16:47:18 +0530 IST | 
| hex_decode(input interface{}) []byte                                  | Hex decodes the given input                                                                                               | `hex_decode("6161")` // aa                                                                               |
| hex_encode(input interface{}) string                                  | Hex encodes the given input                                                                                               | `hex_encode("aa")` // 6161                                                                               |
| html_escape(input interface{}) string                                 | HTML escapes the given input                                                                                              | `html_escape("<body>test</body>")` // &lt;body&gt;test&lt;/body&gt;                                      |
| html_unescape(input interface{}) string                               | HTML un-escapes the given input                                                                                           | `html_unescape("&lt;body&gt;test&lt;/body&gt;")` // <body>test</body>                                    |
| len(arg interface{}) int                                              | Returns the length of the input                                                                                           | `len("Hello")` // 5                                                                                      |
| md5(input interface{}) string                                         | Calculates the MD5 (Message Digest) hash of the input                                                                     | `md5("Hello")` // 8b1a9953c4611296a827abf8c47804d7                                                       |
| mmh3(input interface{}) string                                        | Calculates the MMH3 (MurmurHash3) hash of an input                                                                        | `mmh3("Hello")` // 316307400                                                                             |
| print_debug(args ...interface{})                                      | Prints the value of a given input or expression. Used for debugging.                                                      | `print_debug(1+2, "Hello")` // [INF] print_debug value: [3 Hello]                                        |
| rand_base(length uint, optionalCharSet string) string                 | Generates a random sequence of given length string from an optional charset (defaults to letters and numbers)             | `rand_base(5, "abc")` // caccb                                                                           |
| rand_char(optionalCharSet string) string                              | Generates a random character from an optional character set (defaults to letters and numbers)                             | `rand_char("abc")` // a                                                                                  |
| rand_int(optionalMin, optionalMax uint) int                           | Generates a random integer between the given optional limits (defaults to 0 - MaxInt32)                                   | `rand_int(1, 10)`  // 6                                                                                  |
| rand_text_alpha(length uint, optionalBadChars string) string          | Generates a random string of letters, of given length, excluding the optional cutset characters                           | `rand_text_alpha(10, "abc")` // WKozhjJWlJ                                                               |
| rand_text_alphanumeric(length uint, optionalBadChars string) string   | Generates a random alphanumeric string, of given length without the optional cutset characters                            | `rand_text_alphanumeric(10, "ab12")` // NthI0IiY8r                                                       |
| rand_text_numeric(length uint, optionalBadNumbers string) string      | Generates a random numeric string of given length without the optional set of undesired numbers                           | `rand_text_numeric(10, 123)` // 0654087985                                                               |   
| regex(pattern, input string) bool                                     | Tests the given regular expression against the input string                                                               | `regex("H([a-z]+)o", "Hello")` // true                                                                   |
| remove_bad_chars(input, cutset interface{}) string                    | Removes the desired characters from the input                                                                             | `remove_bad_chars("abcd", "bc")` // ad                                                                   |
| repeat(str string, count uint) string                                 | Repeats the input string the given amount of times                                                                        | `repeat("../", 5)` // ../../../../../                                                                    |
| replace(str, old, new string) string                                  | Replaces a given substring in the given input                                                                             | `replace("Hello", "He", "Ha")` // Hallo                                                                  |
| replace_regex(source, regex, replacement string) string               | Replaces substrings matching the given regular expression in the input                                                    | `replace_regex("He123llo", "(\\d+)", "")` // Hello                                                       |
| reverse(input string) string                                          | Reverses the given input                                                                                                  | `reverse("abc")` // cba                                                                                  |
| sha1(input interface{}) string                                        | Calculates the SHA1 (Secure Hash 1) hash of the input                                                                     | `sha1("Hello")` // f7ff9e8b7bb2e09b70935a5d785e0cc5d9d0abf0                                              |
| sha256(input interface{}) string                                      | Calculates the SHA256 (Secure Hash 256) hash of the input                                                                 | `sha256("Hello")` // 185f8db32271fe25f561a6fc938b2e264306ec304eda518007d1764826381969                    |
| to_lower(input string) string                                         | Transforms the input into lowercase characters                                                                            | `to_lower("HELLO")` // hello                                                                             |
| to_upper(input string) string                                         | Transforms the input into uppercase characters                                                                            | `to_upper("hello")` // HELLO                                                                             |
| trim(input, cutset string) string                                     | Returns a slice of the input with all leading and trailing Unicode code points contained in cutset removed                | `trim("aaaHelloddd", "ad")` // Hello                                                                     |
| trim_left(input, cutset string) string                                | Returns a slice of the input with all leading Unicode code points contained in cutset removed                             | `trim_left("aaaHelloddd", "ad")` // Helloddd                                                             |
| trim_prefix(input, prefix string) string                              | Returns the input without the provided leading prefix string                                                              | `trim_prefix("aaHelloaa", "aa")` // Helloaa                                                              |
| trim_right(input, cutset string) string                               | Returns a string, with all trailing Unicode code points contained in cutset removed                                       | `trim_right("aaaHelloddd", "ad")` // aaaHello                                                            |
| trim_space(input string) string                                       | Returns a string, with all leading and trailing white space removed, as defined by Unicode                                | `trim_space("  Hello  ")` // "Hello"                                                                     |
| trim_suffix(input, suffix string) string                              | Returns input without the provided trailing suffix string                                                                 | `trim_suffix("aaHelloaa", "aa")` // aaHello                                                              |
| unix_time(optionalSeconds uint) float64                               | Returns the current Unix time (number of seconds elapsed since January 1, 1970 UTC) with the added optional seconds       | `unix_time(10)` // 1639568278                                                                            |
| url_decode(input string) string                                       | URL decodes the input string                                                                                              | `url_decode("https:%2F%2Fprojectdiscovery.io%3Ftest=1")` // https://projectdiscovery.io?test=1           |
| url_encode(input string) string                                       | URL encodes the input string                                                                                              | `url_encode("https://projectdiscovery.io/test?a=1")` // https%3A%2F%2Fprojectdiscovery.io%2Ftest%3Fa%3D1 |
| wait_for(seconds uint)                                                | Pauses the execution for the given amount of seconds                                                                      | `wait_for(10)` // true                                                                                   |

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
