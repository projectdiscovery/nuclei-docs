# Changelog

## Nuclei v2.1.1

+ Added negative matcher support
+ Added support for severity filtering with `severity` flag  by @manuelbua
+ Added support for template exclusions with `exclude` flag by @manuelbua
+ Added `.nuclei-ignore` file support
+ Added Rate limit per host by @CasperGN 
+ Added template list support with `tl` flag by @vzamanillo
+ Added template name field in JSON output by @vzamanillo
+ Added color support for severity by @vzamanillo
+ Added Progress bar support with the silent flag by @vzamanillo
+ Added support for using local templates along with nuclei-templates
+ Added template preloading at the start of the scan
+ Added support for  golangci lint by @vzamanillo
+ Added JSON output support for DNS templates by @Marmelatze
+ Added centralize template loaded info message, add output coloring by @manuelbua
+ Added template ID on HTTP request error message @vzamanillo
+ Added severity information in the output by @vzamanillo
+ Added match groups support in regex extractor
+ Fixed a failed request error on multiple URLs
+ Fixed a bug with helper function by @organiccrap
+ Fixed a bug with port conflict input with URLs and templates by @vzamanillo
+ Fixed a bug with Workflow detecting "No Results" by @vzamanillo
+ Fixed inconsistent output printing in the terminal
+ Fixed No JSON output with workflows
+ Fixed a bug with matches when multiple headers with the same name by @CasperGN 
+ Fixed `No result` found problem with and condition by @Marmelatze
+ Fixed an issue with `all` matched part by @rykkard
+ Updated nuclei-templates current and outdated messages
+ Updated template loading UI message by @vzamanillo

## Nuclei v2.1.0

+ Nuclei engine rework. 
+ Added Progress bar with live results by @manuelbua
+ Added multiple input template support by @manuelbua
+ Added in-template cookie reuse
+ Added key-value supported extractor
+ Added dynamic extraction and reuse
+ Added coloring support in output results by @manuelbua
+ Added wild-card template input support by @wdahlenburg
+ Fixed relative path issue with payloads.
+ Fixed dockerfile go version.

## Nuclei v2.0.4

+ Better error handling for templates by @manuelbua
+ Fixed an issue with release binary.

## Nuclei v2.0.3

+ Fixed bugs in raw-requests.
+ Fixed update template issue.

## Nuclei v2.0.2

+ Fixed an issue with failed requests.

## Nuclei v2.0.1

+ Fixed an issue with DSL helper function.
+ Fixed error with auto-updates Github rate limit.
+ Fixed defaults to OR condition when no condition is specified.

## Nuclei v2.0.0

+ Fixed raw requests newlines and allow blank request path.
+ Added relative path and auto-template fetching support from installed directory.
+ Added single target support.
+ Added json output support.
+ Chained workflow support with conditions etc.
+ Added template updates feature with auto-updates, etc.
+ Fixed blank output file bug.

## Nuclei v1.1.7

+ Added better verbose and debug modes.
+ Inform user and no output file in case of 0 results.

## Nuclei v1.1.6

+ Updated default user-agent to include project details

## Nuclei v1.1.5

+ Added intruder like support (sniper/pitchfork/clusterbomb)
+ Fuzzing with DSL helpers
+ Fixed bug in body decompression
+ Added global headers via CLI

## Nuclei v1.1.4

+ Small improvements.
+ General Fixes.

## Nuclei v1.1.3

+ Complex DSL queries
+ Raw requests
+ Proxy (http/socks5)

## Nuclei v1.1.2

+ Fixed a bug with DNS requests and output file.

## Nuclei v1.1.1

+ Massive code refactor, conditions support + stdin input bug fix.
+ DNS requests support
+ Binary Matcher support
+ Conditional redirects support within templates.

## Nuclei v1.1.0

+ Fixed go.mod issue
+ Added extractors for custom text extraction from templates
+ Fixed a bug with default headers

## Nuclei v1.0.1

+ Fixed go.mod file issue

## Nuclei v1.0.0

+ Initial Release
