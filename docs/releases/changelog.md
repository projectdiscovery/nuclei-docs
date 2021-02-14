# Changelog

## Nuclei v2.2.0 - 20 Nov 2020

!!! example ""

	+ Added **Turbo intruder** support for fuzzing
	+ Added **HTTP pipeline** support for fuzzing
	+ Added **Connection pooling** support for fuzzing
	+ Added **Raw HTTP** support for malformed HTTP requests.
	+ Added support for **Race condition** testing
	+ Added `§variable§` marker for fuzzing
	+ Added `stats` in multithreaded requests (fuzzing)
	+ Added **YAML syntax support for workflows**
	+ Added **Project file** support for request reuse
	+ Added global rate limit support
	+ Added **burp collaborator** support
	+ Added [hmap](https://github.com/projectdiscovery/hmap) for reducing memory uses
	+ Added [clistats](https://github.com/projectdiscovery/clistats) in place of a progress bar
	+ Added support to **DSL matcher to match each unique request**
	+ Added trace log (`-trace-log`) support
	+ Added `-templates-version` flag to list template version
	+ Added `-no-meta` flag to ignore meta information
	+ Added dynamic field support in the template info block
	+ Added `bulk-size` flag
	+ Added **response time support to DSL**
	+ Added type to specify type of request
	+ Added **mmh3** hashing support in helper functions
	+ Added shared resolver cache among various HTTP clients
	+ Added fuzzing payloads output values to json output
	+ Added `.nuclei-ignore` file from current working directory
	+ Added comments support in `.nuclei-ignore` file
	+ Added `stop-at-first-match` flag
	+ Added flag to disable host header and content length
	+ Added host information in the JSON response by @savushkin-yauheni
	+ Updated flag `nC` to `no-color` 
	+ Updated flag `json-requests` to `include-rr`
	+ Updated flag `pbar` to `stats`
	+ Fixed a bug with ignoring paths in the input file by @vzamanillo
	+ Fixed a bug with raw requests redirect
	+ Fixed a bug with debug flag to display post body
	+ Fixed a panic with trace log

## Nuclei v2.1.1

!!! example ""

	+ Added **negative matcher support**
	+ Added **support for severity based filtering** with `severity` flag  by @manuelbua
	+ Added **support for template exclusions** with `exclude` flag by @manuelbua
	+ Added `.nuclei-ignore` file support
	+ Added **rate limit per host** with `rl`flag by @CasperGN 
	+ Added template list support with `tl` flag by @vzamanillo
	+ Added template name field in JSON output by @vzamanillo
	+ Added color support for severity by @vzamanillo
	+ Added Progress bar support with the silent flag by @vzamanillo
	+ **Added support for using local templates along with nuclei-templates**
	+ Added template preloading at the start of the scan
	+ Added support for  golangci lint by @vzamanillo
	+ Added JSON output support for DNS templates by @Marmelatze
	+ Added centralize template loaded info message, add output coloring by @manuelbua
	+ Added template ID on HTTP request error message @vzamanillo
	+ Added severity information in the output by @vzamanillo
	+ Added **match groups support in regex extractor**
	+ **Fixed a failed request error on multiple URLs**
	+ Fixed a bug with helper function by @organiccrap
	+ **Fixed a bug with port conflict input with URLs and templates** by @vzamanillo
	+ **Fixed a bug with Workflow detecting `No Results`** by @vzamanillo
	+ Fixed inconsistent output printing in the terminal
	+ Fixed No JSON output with workflows
	+ Fixed a bug with matches when multiple headers with the same name by @CasperGN 
	+ Fixed `No result` found problem with and condition by @Marmelatze
	+ Fixed an issue with `all` matched part by @rykkard
	+ Updated nuclei-templates current and outdated messages
	+ Updated template loading UI message by @vzamanillo

## Nuclei v2.1.0

!!! example ""

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

!!! example ""

	+ Better error handling for templates by @manuelbua
	+ Fixed an issue with release binary.

## Nuclei v2.0.3

!!! example ""

	+ Fixed bugs in raw-requests.
	+ Fixed update template issue.

## Nuclei v2.0.2

!!! example ""

	+ Fixed an issue with failed requests.

## Nuclei v2.0.1

!!! example ""

	+ Fixed an issue with DSL helper function.
	+ Fixed error with auto-updates Github rate limit.
	+ Fixed defaults to OR condition when no condition is specified.

## Nuclei v2.0.0

!!! example ""

	+ Fixed raw requests newlines and allow blank request path.
	+ Added relative path and auto-template fetching support from installed directory.
	+ Added single target support.
	+ Added json output support.
	+ Chained workflow support with conditions etc.
	+ Added template updates feature with auto-updates, etc.
	+ Fixed blank output file bug.

## Nuclei v1.1.7

!!! example ""

	+ Added better verbose and debug modes.
	+ Inform user and no output file in case of 0 results.

## Nuclei v1.1.6

!!! example ""

	+ Updated default user-agent to include project details

## Nuclei v1.1.5

!!! example ""

	+ Added intruder like support (sniper/pitchfork/clusterbomb)
	+ Fuzzing with DSL helpers
	+ Fixed bug in body decompression
	+ Added global headers via CLI

## Nuclei v1.1.4

!!! example ""

	+ Small improvements.
	+ General Fixes.

## Nuclei v1.1.3

!!! example ""

	+ Complex DSL queries
	+ Raw requests
	+ Proxy (http/socks5)

## Nuclei v1.1.2

!!! example ""

	+ Fixed a bug with DNS requests and output file.

## Nuclei v1.1.1

!!! example ""

	+ Massive code refactor, conditions support + stdin input bug fix.
	+ DNS requests support
	+ Binary Matcher support
	+ Conditional redirects support within templates.

## Nuclei v1.1.0

!!! example ""

	+ Fixed go.mod issue
	+ Added extractors for custom text extraction from templates
	+ Fixed a bug with default headers

## Nuclei v1.0.1

!!! example ""

	+ Fixed go.mod file issue

## Nuclei v1.0.0

!!! example ""

	+ Initial Release
