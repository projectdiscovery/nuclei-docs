??? info "Nuclei 템플릿이 뭔가요?"

    Nuclei [templates](http://github.com/projectdiscovery/nuclei-templates) 는 Nuclei 프로젝트의 핵심입니다. 템플릿들은 다양한 취약점을 찾아내기 위해 실행되는 핵심 로직을 포함합니다. 프로젝트는 **수천 개**의 즉시 사용가능한 **[커뮤니티에서 기여해준](https://github.com/projectdiscovery/nuclei-templates/graphs/contributors)** 취약점 템플릿들로 구성되어 있습니다.

??? info "어떻게 Nuclei 템플릿을 장성할 수 있나요?"

    Nuclei templates 을 새로 작성하고 커스텀 하기 위한 [가이드](https://nuclei.projectdiscovery.io/templating-guide/)를 유지하고 있습니다. 또한, Nuclei가 지원하는 다양한 모듈들에 대한 [샘플 탬플릿](https://nuclei.projectdiscovery.io/template-examples/http/)도 있습니다.

??? tip "Nuclei 템플릿을 작성하는 것이 유용한가요?"

    한 어플리케이션의 보안을 평가하는 일을 많은 시간을 소모합니다. 가능할 때마다 단계를 자동화 하는 것은 언제나 시간이 절약되고 좋은 것입니다. 보안 취약점을 발견하면, 그 이슈를 재현하기 위하여 필요한 HTTP 요청을 정의하여 Nuclei 템플릿을 준비하고, 이를 통해 쉽게 여러 호스트에서 동일한 취약점을 테스트합니다. **템플릿을 한 번 작성하여 영원히 사용하는 것**은 더 이상 취약점 테스트를 수동으로 할 필요가 없기 때문에 충분히 가치있는 일입니다.

    다음은 보안관련 결과들을 자동화하기 위해 커뮤니티가 템플릿을 사용한 예시입니다.

	??? Reference
		- https://dhiyaneshgeek.github.io/web/security/2021/02/19/exploiting-out-of-band-xxe/
		- https://blog.melbadry9.xyz/fuzzing/nuclei-cache-poisoning
		- https://blog.melbadry9.xyz/dangling-dns/xyz-services/ddns-worksites
		- https://blog.melbadry9.xyz/dangling-dns/aws/ddns-ec2-current-state
		- https://blog.projectdiscovery.io/writing-nuclei-templates-for-wordpress-cves/

??? info "Nuclei 템플릿을 어떻게 실행시킬 수 있나요?"

    Nuclei 템플릿은 이름과 태그를 통해 (각각 `-templates` (`-t`)나 `-tags`플래그) 실행시킬 수 있습니다.

	```
	nuclei -tags cve -list target_urls.txt
	```

??? info "Nuclei 템플릿에 기여하고 싶어요! 😁"

    Nuclei 템플릿을 커뮤니티에 공유해주시는 것은 언제나 환영합니다. 템플릿의 세부사항과 함께 [GitHub issue](https://github.com/projectdiscovery/nuclei-templates/issues/new?assignees=&labels=nuclei-template&template=submit-template.md&title=%5Bnuclei-template%5D+template-name)를 열거나 본인만의 Nuclei 템플릿을 포함하여 GitHub [pull request](https://github.com/projectdiscovery/nuclei-templates/pulls)을 보내주세요. 만약 Github 계정이 없다면, [discord server](https://discord.gg/projectdiscovery)를 이용하셔도 됩니다.

??? warning "잘못된 결과를 얻었어요!"
    
    Nuclei 템플릿 프로젝트는 **커뮤니티에서 기여하는 프로젝트**입니다. ProjectDiscovery 팀은 템플릿이 병합되기 전에 수동적으로 템플릿들을 리뷰합니다. 그럼에도, 약한 matchers를 가진 일부 템플릿들은 통과할 가능성이 있습니다. 이는 잘못된 결과를 생성할 수 있습니다. **템플릿은 그 matchers만큼의 성능을 냅니다.**

    만약 템플릿이 거짓된 결과를 생상하는지 확인하고 싶다면, 빠르게 고치기 위한 다음의 몇 가지 따라할 수 있는 단계들이 있습니다.

	??? info "잘못된 결과를 찾았는데, 정확한지 모르겠어요."

        [Twitter](https://twitter.com/pdnuclei) 혹은 [Discord](https://discord.gg/projectdiscovery)의 DM을 통해 템플릿의 타당성을 확인합니다.

	??? info "잘못된 결과를 찾았는데, 어떻게 고칠 지 모르겠어요."

        GitHub [issue](https://github.com/projectdiscovery/nuclei-templates/issues/new?assignees=&labels=false-positive&template=false-positive.md&title=%5Bfalse-positive%5D+template-name+)를 세부사항과 함께 남겨주세요. 모두가 문제를 해결하고 업데이트 하기 위해 일할 것입니다.

	??? info "잘못된 결과를 찾는 템플릿을 찾고, 어떻게 고치는 지 알아요."

		수정한 내역을 GitHub [pull request](https://github.com/projectdiscovery/nuclei-templates/pulls)를 통해 전달해주세요.

??? warning "모든 템플릿을 실행시킬 수 없어요!"

    Nuclei 템플릿에는 퍼지 및 대상 시스템에 DoS를 초래할 수 있는 다양한 템플릿이 포함되어 있습니다([목록](https://github.com/projectdiscovery/nuclei-templates/blob/master/.nuclei-ignore) 참조). 이러한 템플릿들이 실수로 실행되지 않도록 템플릿에 태그를 달고 기본 스캔에서 제외합니다. 이런 템플릿들은 `-itags` 옵션을 사용한 명시적인 경우에만 실행됩니다.

??? warning "Github에 있는 템플릿이지만 Nuclei로 실행되지 않아요."

    Nuclei 실행파일로 템플릿을 다운받거나 업데이트할 때, 항상 최신 버전의 릴리즈에서 모든 다운로드를 받습니다. 릴리즈 이후에 추가된 템플릿들은 [master branch](https://github.com/projectdiscovery/nuclei-templates)에 존재하고 새 릴리즈가 생성될 때 추가됩니다.
