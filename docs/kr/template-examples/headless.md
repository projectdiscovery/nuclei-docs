### 기본 헤드리스 탐색 예시

이 템플릿은 헤드리스 브라우저의 URL을 방문하여 로드될 때까지 기다립니다.

```yaml
id: basic-headless-request

info:
  name: Basic Headless Request
  author: pdteam
  severity: info

headless:
  - steps: 
    - action: navigate
      args:
        url: "{{BaseURL}}" 
    - action: waitload
```

### 헤드리스 프로토타입 오염 감지

아래 템플릿은 Nuclei 헤드리스 기능이 있는 페이지에서 프로토타입 오염을 감지합니다. 탐지를 위한 코드는 [https://github.com/msrkp/PPScan](https://github.com/msrkp/PPScan)에서 가져왔습니다. 우리는 핵의 스크립트 주입 기능을 사용하여 프로토타입 오염에 대한 신뢰할 수 있는 탐지를 제공합니다.

```yaml
id: prototype-pollution-check

info:
  name: Prototype Pollution Check
  author: pd-team
  severity: medium
  reference: https://github.com/msrkp/PPScan

headless:
  - steps:
      - action: setheader
        args:
          part: response
          key: Content-Security-Policy
          value: "default-src * 'unsafe-inline' 'unsafe-eval' data: blob:;"
      - action: setheader
        args:
          part: response
          key: X-Frame-Options
          value: foo
      - action: setheader
        args:
          part: response
          key: If-None-Match
          value: foo
      # Set the hook to override window.data for xss detection
      - action: script
        args:
          hook: true
          code: |
            // Hooking code adapted from https://github.com/msrkp/PPScan/blob/main/scripts/content_script.js
            (function() {window.alerts = [];

            function logger(found) {
            	window.alerts.push(found);
            }

            function check() {
                loc = location.href;

                if (loc.indexOf("e32a5ec9c99") >= 0 && loc.search("a0def12bce") == -1) {
                    setTimeout(function() {
                        if (Object.prototype.e32a5ec9c99 == "ddcb362f1d60") {
                            logger(location.href);
                        }
                        var url = new URL(location.origin + location.pathname);
                        url.hash = "__proto__[a0def12bce]=ddcb362f1d60&__proto__.a0def12bce=ddcb362f1d60&dummy";
                        location = url.href;
                    }, 5 * 1000);
                } else if (loc.search("a0def12bce") != -1) {
                    setTimeout(function() {
                        if (Object.prototype.a0def12bce == "ddcb362f1d60") {
                            logger(location.href);
                        }
                        window.close();
                    }, 5 * 1000);
                } else {
                    var url = new URL(loc);
                    url.searchParams.append("__proto__[e32a5ec9c99]", "ddcb362f1d60");
                    url.searchParams.append("__proto__.e32a5ec9c99", "ddcb362f1d60");
                    location = url.href;
                }
            }

            window.onload = function() {
                if (Object.prototype.e32a5ec9c99 == "ddcb362f1d60" ||  Object.prototype.a0def12bce == "ddcb362f1d60") {
                    logger(location.href);
                } else {
                    check();
                }
            };

            var timerID = setInterval(function() {
                if (Object.prototype.e32a5ec9c99 == "ddcb362f1d60" || Object.prototype.a0def12bce == "ddcb362f1d60") {
                    logger(location.href);
                    clearInterval(timerID);
                }
            }, 5 * 1000)})();
      - args:
          url: "{{BaseURL}}"
        action: navigate
      - action: waitload
      - action: script
        name: alerts
        args:
          code: "window.alerts"
    matchers:
      - type: word
        part: alerts
        words:
          - "__proto__"
    extractors:
      - type: kval
        part: alerts
        kval:
          - alerts
```

### 헤드리스 모드로 DVWA XSS 재현

이 템플릿은 DVWA(Damn Vulnerable Web App)에 로그인하고 Reflected XSS를 자동으로 재현하려고 시도하며 페이로드가 성공적으로 실행된 경우 일치 항목을 반환합니다.

```yaml
id: dvwa-xss-verification

info:
  name: DVWA Reflected XSS Verification
  author: pd-team
  severity: info

headless:
  - steps:
      - args:
          url: "{{BaseURL}}"
        action: navigate
      - action: waitload

      # xss 감지를 위해 window.data를 재정의하도록 후크 설정
      - action: script
        args:
          hook: true
          code: "(function() { window.alert = function() { window.data = 'found' } })()"
      - args:
          by: x
          value: admin
          xpath: /html/body/div/div[2]/form/fieldset/input
        action: text
      - args:
          by: x
          value: password
          xpath: /html/body/div/div[2]/form/fieldset/input[2]
        action: text
      - args:
          by: x
          xpath: /html/body/div/div[2]/form/fieldset/p/input
        action: click
      - action: waitload
      - args:
          by: x
          xpath: /html/body/div/div[2]/div/ul[2]/li[11]/a
        action: click
      - action: waitload
      - args:
          by: x
          value: '"><svg/onload=alert(1)>'
          xpath: /html/body/div/div[3]/div/div/form/p/input
        action: text
      - args:
          keys: "\r" # 키보드의 Enter 키를 누릅니다.
        action: keyboard
      - action: waitload
      - action: script
        name: alert
        args:
          code: "window.data"
    matchers:
      - part: alert
        type: word
        words:
          - "found"
```

### DOM XSS 감지

이 템플릿은 `eval`, `innerHTML` 및 `document.write`와 같은 공통 싱크를 후킹하여 `window.name` 소스에 대한 DOM-XSS 감지를 수행합니다.

```yaml
id: window-name-domxss

info:
  name: window.name DOM XSS
  author: pd-team
  severity: medium

headless:
  - steps:
      - action: setheader
        args:
          part: response
          key: Content-Security-Policy
          value: "default-src * 'unsafe-inline' 'unsafe-eval' data: blob:;"
      - action: script
        args:
          hook: true
          code: |
            (function() {window.alerts = [];

            function logger(found) {
            	window.alerts.push(found);
            }

            function getStackTrace () {
              var stack;
              try {
                throw new Error('');
              }
              catch (error) {
                stack = error.stack || '';
              }
              stack = stack.split('\n').map(function (line) { return line.trim(); });
              return stack.splice(stack[0] == 'Error' ? 2 : 1);
            }
            window.name = "{{randstr_1}}'\"<>";

            var oldEval = eval;
            var oldDocumentWrite = document.write;
            var setter = Object.getOwnPropertyDescriptor(Element.prototype, 'innerHTML').set;
            Object.defineProperty(Element.prototype, 'innerHTML', {
              set: function innerHTML_Setter(val) {
                if (val.includes("{{randstr_1}}'\"<>")) {
                  logger({sink: 'innerHTML', source: 'window.name', code: val, stack: getStackTrace()});
                }
                return setter.call(this, val)
              }
            });
            eval = function(data) {
              if (data.includes("{{randstr_1}}'\"<>")) {
                logger({sink: 'eval' ,source: 'window.name', code: data, stack: getStackTrace()});
              }
              return oldEval.apply(this, arguments);
            };
            document.write = function(data) {
              if (data.includes("{{randstr_1}}'\"<>")) {
                logger({sink: 'document.write' ,source: 'window.name', code: data, stack: getStackTrace()});
              }
              return oldEval.apply(this, arguments);
            };
            })();
      - args:
          url: "{{BaseURL}}"
        action: navigate
      - action: waitload
      - action: script
        name: alerts
        args:
          code: "window.alerts"
    matchers:
      - type: word
        part: alerts
        words:
          - "sink:"
    extractors:
      - type: kval
        part: alerts
        kval:
          - alerts
```