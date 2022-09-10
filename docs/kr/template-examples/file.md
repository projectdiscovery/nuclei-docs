### 기본 파일 템플릿

제공된 파일에서 패턴 체크를 하는 템플릿입니다.

```yaml
id: ssh-public-key

info:
  name: SSH Public Key Detect
  author: pd-team
  severity: low

file:
  - extensions:
      - pub
    max-size: 1024 # read very small chunks

    matchers:
      - type: word
        words:
          - "ssh-rsa"
```

### 비재귀, 제외 목록 확장

아래의 템플릿은 위의 템플릿과 동일합니다. 하지만, 비재귀 옵션과 함께 제외 목록을 사용하고 있습니다.

```yaml
id: ssh-private-key

info:
  name: SSH Private Key Detect
  author: pd-team
  severity: high

file:
  - extensions:
      - all
    denylist:
      - pub
    no-recursive: true
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