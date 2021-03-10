### File Requests

Nuclei allows modelling templates that can match/extract on filesystem too.

```yaml
# Start the requests for the template right here
file:
```

#### Extensions

By default, certain extensions are not allowed in nuclei file module. A list of these is provided below - 

> ".3g2", ".3gp", ".7z", ".apk", ".arj", ".avi", ".axd", ".bmp", ".css", ".csv", ".deb", ".dll", ".doc", ".drv", ".eot", ".exe", ".flv", ".gif", ".gifv", ".gz", ".h264", ".ico", ".iso", ".jar", ".jpeg", ".jpg", ".lock", ".m4a", ".m4v", ".map", ".mkv", ".mov", ".mp3", ".mp4", ".mpeg", ".mpg", ".msi", ".ogg", ".ogm", ".ogv", ".otf", ".pdf", ".pkg", ".png", ".ppt", ".psd", ".rar", ".rm", ".rpm", ".svg", ".swf", ".sys", ".tar.gz", ".tar", ".tif", ".tiff", ".ttf", ".txt", ".vob", ".wav", ".webm", ".wmv", ".woff", ".woff2", ".xcf", ".xls", ".xlsx", ".zip"

To match on all extensions (except the ones in default denylist), use the following - 

```yaml
extensions:
  - all
```

You can also provide a list of custom extensions that should be matched upon.

```yaml
extensions:
  - .py
  - go
```

A denylist of extensions can also be provided. Files with these extensions will not be processed by nuclei.

```yaml
extensions:
  - all
denylist:
  - .go
  - .py
  - .txt
```

#### More Options

`max-size` parameter can be provided which limits the maximum size of files read by nuclei. Files larger than the max-size will not be processed.

`no-recursive` option disables recursive walking of directories / globs while input is being processed for file module of nuclei.

#### Matchers / Extractor Parts

Valid `part` values supported by **File** protocol for Matchers / Extractor are - 
    
| Value                   | Description             |
|-------------------------|-------------------------|
| raw / body / data / all | Data read from the file |


#### **Example File Template**

The final example template file for a Private Key detection is provided below.

```yaml
id: private-key-detect

info:
  name: Private Key Files Detect
  author: pd-team
  severity: high

file:
  - extensions:
      - all
    max-size: 1024 # read very small chunks
    matchers:
      - type: word
        words:
          - "BEGIN OPENSSH PRIVATE KEY"
          - "BEGIN PRIVATE KEY"
          - "BEGIN RSA PRIVATE KEY"
          - "BEGIN DSA PRIVATE KEY"
          - "BEGIN EC PRIVATE KEY"
          - "BEGIN PGP PRIVATE KEY BLOCK"
          - "ssh-rsa"
```

More complete examples are provided [here](../../template-examples/file.md)