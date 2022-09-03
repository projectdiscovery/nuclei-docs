??? info "Nuclei는 무엇인가요?"

    Nuclei는 간단한 YAML 템플릿을 기초로 한 빠른 맞춤형 취약점 스캐너입니다.

    2개의 요소로 구성됩니다. 1) [Nuclei](http://github.com/projectdiscovery/nuclei) engine - 쉽게 읽고 쓸 수 있는 YAML 기반 형식으로 HTTP/DNS/네트워크/헤드리스/파일 프로토콜 기반 검사를 스크립팅할 수 있게 합니다. 2) Nuclei [templates](http://github.com/projectdiscovery/nuclei-templates) - 즉시 사용할 수 있는 **커뮤니티에서 기여해준** 취약점 템플릿입니다.

??? info "어떻게 시작하게 되었나요?"
	기존의 스캐너는 엔진 위에 쓰기 쉬운 맞춤식 검사를 허용하는 기능이 항상 부족했습니다. 이것이 단순성, 모듈성, 그리고 많은 에셋들을 스캔할 수 있는 능력에 중점을 두고 Nuclei를 개발하기 시작한 이유입니다.
	
	누구나 사용하기에 간편하기도 하고 현대 웹에 통합될 만큼 충분히 정교하길 원했습니다. Nuclei의 기능들은 복잡한 보안 검사를 매우 빠르게 프로토타입화 할 수 있도록 조정했습니다.

??? info "어떤 모듈을 지원하나요?"

	Nuclei는 다음과 같은 모듈을 지원합니다.

	- [HTTP](https://nuclei.projectdiscovery.io/templating-guide/protocols/http/)
	- [DNS](https://nuclei.projectdiscovery.io/templating-guide/protocols/dns/)
	- [TCP](https://nuclei.projectdiscovery.io/templating-guide/protocols/network/)
	- [FILE](https://nuclei.projectdiscovery.io/templating-guide/protocols/file/)

??? info "Nuclei를 통해 어떤 종류의 스캔을 할 수 있나요?"

	Nuclei는 로컬 파일 시스템의 소스 코드나 파일들에 있는 **웹 어플리케이션**, **네트워크**, **잘못된 구성 기반의 DNS**, **비밀스러운 스캐닝**등에서 생기는 보안 취약점을 탐지 하는데 사용할 수 있습니다. 

??? info "어떻게 이 프로젝트는 어떻게 유지되고 있나요?"

	Nuclei 프로제트는 현재 [ProjectDiscovery](https://projectdiscovery.io/#/)팀에 의해서 개발 및 유지되고 있습니다. 그리고 일반적으로 매 2주마다 릴리즈를 진행하고 있습니다.

??? tip "어떻게 이 프로젝트에 지원/기여 할 수 있나요? 💙"

	프로젝트의 모멘텀을 유지하기 위해서, 모두가 [template project](https://github.com/projectdiscovery/nuclei-templates)에서 새로운 템플릿을 생성하고 커뮤니티에 공유해주길 원합니다. Nuclei 템플릿 저장소가 공개적이고, 즉시 사용가능 하고, 최신을 유지할 수 있도록 도와주세요.

	Nuclei를 사용하며 찾은 흥미롭거나 특별한 보안 문제를 찾았고, 이 과정 전체를 블로그의 형태로 공유하고자 한다면 [ProjectDiscovery blog](https://blog.projectdiscovery.io)에 게스트 게시물로 올려주시면 감사하겠습니다.

??? warning "Nuclei에서 결과를 얻었습니다. 언제 이를 신고(Report)해야하나요?"

	**잠시만요** -- Nuclei에서 보안 문제를 찾은 이후에 항상 이를 신고하기 이전에 다시 살펴 보는 것이 좋습니다. 다음은 문제를 확인/검증하기 위한 팁입니다.

	??? tip "어떻게 Nuclei의 결과를 검증하나요?"

		Nuclei를 통해 결과를 획득하고, 취약한 대상과 템플릿을 알게 되면, ==`-debug`== 플래그와 함께 템플릿을 다시 실행하여 템플릿에 정의된 예상 결과와 비교하여 출력을 검사합니다. 이러한 방식을 통해서, 식별된 취약점을 확정할 수 있습니다.

??? warning "Nuclei가 생성하는 트래픽은 얼마나 많은가요?"
	
	기본적으로 **모든 nuclei-templates**을 실행할 때 한 대상에게 (HTTP 프로토콜 과 다른 서비스 모두) 수 천개의 요청을 생성합니다. 매일 추가되는 3500개의 Nuclei 템플릿에서 비롯됩니다.

	!!! info ""
		기본적으로 [이 곳](https://github.com/projectdiscovery/nuclei-templates/blob/master/.nuclei-ignore)에 있는 일부 템플릿은 기본 검색에서 제외됩니다.

??? warning "Nuclei를 실행하는 것은 안전한가요?"

	Nuclei에서 =="안전"== 하다고 말하기 위해서 두 가지를 고려합니다.

	1. 핵이 대상 사이트에 생성한 **트래픽**.
	2. 템플릿이 사이트에 미치는 **영향**

	!!! check "HTTP 트래픽"

		Nuclei는 지능적인 요청 감소 때문에 항상 선택된 템플릿 수보다 적은 수의 HTTP 요청을 생성합니다. 일부 템플릿에는 다수의 요청이 포함되어 있지만, 이 룰은 일반적으로 대부분의 스캔 설정에 적용됩니다.
		
	!!! check "안전한 템플릿"

		Nuclei 템플릿에는 퍼지 및 대상 시스템에 DoS를 초래할 수 있는 다양한 템플릿이 포함되어 있습니다([목록](https://github.com/projectdiscovery/nuclei-templates/blob/master/.nuclei-ignore) 참조). 이러한 템플릿들이 실수로 실행되지 않도록 템플릿에 태그를 달고 기본 스캔에서 제외합니다. 이런 템플릿들은 `-itags` 옵션을 사용한 명시적인 경우에만 실행됩니다.

??? info "Nuclei 라이선스는 무엇인가요?"

	Nuclei [MIT License](https://github.com/projectdiscovery/nuclei/blob/master/LICENSE.md)를 가진 오픈소스 프로젝트입니다.


??? info "더 많은 질문을 하고 싶습니다! 🙋"
	
	[Discord server](https://discord.gg/projectdiscovery)에 참여하시거나 [Twitter](http://twitter.com/pdnuclei)로 연락을 부탁드립니다.
