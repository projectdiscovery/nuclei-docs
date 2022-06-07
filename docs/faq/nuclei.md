??? info "What is nuclei?"

	Nuclei is a fast and customizable vulnerability scanner based on simple **YAML-based templates**.
	
	It has two components, 1) [Nuclei](http://github.com/projectdiscovery/nuclei) engine - the core of the project allows scripting HTTP / DNS / Network / Headless / File protocols based checks in a very simple to read-and-write YAML-based format. 2) Nuclei [templates](http://github.com/projectdiscovery/nuclei-templates) - ready-to-use **community-contributed** vulnerability templates.


??? info "What was the genesis behind nuclei?"
	Traditional scanners always lacked the features to allow easy-to-write custom checks on top of their engine. And this is why we started developing Nuclei with a core focus on simplicity, modularity, and the ability to scan on many assets.

	We wanted something simple enough to be used by ==**everyone**== while complex enough to integrate into the modern web with its intricacies. The features implemented in nuclei are tailored to allow very rapid prototyping of complex security checks.

??? info "What modules does nuclei engine support?"

	Nuclei engine supports the following type of modules.

	- [HTTP](https://nuclei.projectdiscovery.io/templating-guide/protocols/http/)
	- [DNS](https://nuclei.projectdiscovery.io/templating-guide/protocols/dns/)
	- [TCP](https://nuclei.projectdiscovery.io/templating-guide/protocols/network/)
	- [FILE](https://nuclei.projectdiscovery.io/templating-guide/protocols/file/)

??? info "What kind of scans can I perform with nuclei?"

	Nuclei can be used to detect security vulnerabilities in **Web Applications**, **Networks**, **DNS** based misconfiguration, and **Secrets scanning** in source code or files on the local file system.

??? info "How well-maintained is this project?"

	The Nuclei project is developed and maintained by [ProjectDiscovery](https://projectdiscovery.io/#/) team and is in active development (we generally release every other week).

??? tip "How can I support/contribute to this project? 💙"

	To keep us motivated to work on this project, we request you to write/share new nuclei templates with the community at [template project](https://github.com/projectdiscovery/nuclei-templates) and help us to maintain this public and ready to use, up-to-date nuclei templates.

	If you found an interesting/unique security issue using nuclei and want to share the process walk-through with everyone in the form of a blog, we are happy to publish your guest blog at https://blog.projectdiscovery.io.

??? warning "I found results with nuclei. When should I report it?"

	**Wait a minute** -- after nuclei detected a security issue, it's always advised to have a second look before reporting it. Here's a tip to confirm/validate the found matches.

	??? tip "How do I validate nuclei results?"

		Once nuclei finds a result, and you have vulnerable ==URL== and ==template==, rerun the template with ==`-debug`== flag to inspect the vulnerable response against expected matcher defined in the template. In this way, you can confirm the identified vulnerability.

??? warning "How much traffic does nuclei generate?"
	
	As default nuclei makes **1234** HTTP requests in total against single target upon running **all nuclei-templates** directory. This includes over 3500 nuclei templates from the [v9.0.2](https://github.com/projectdiscovery/nuclei-templates/releases/tag/v9.0.2) release.

	!!! info ""
		As default, few templates listed [here](https://github.com/projectdiscovery/nuclei-templates/blob/master/.nuclei-ignore) are excluded from default scans.


??? warning "Is it safe to run nuclei?"

	We consider two factors to say =="safe"== in context of nuclei -

	1. The **traffic** nuclei makes against the target website.
	2. The **impact** templates have on the target website.

	!!! check "HTTP Traffic"

		Nuclei usally makes fewer HTTP requests than the number of templates selected for a scan due to its intelligent request reduction. While some templates contain multiple requests, this rule generally holds true across most scan configurations.

	!!! check "Safe Templates"

		The nuclei templates project houses a variety of templates which include fuzzing and templates which may result in a DOS against the target system. To ensure that no one accidentally runs these templates, they are tagged and nuclei excludes them from the default nuclei scan. These templates can be only executed when the user explicitly instructs nuclei to run them using the `itag` option.

??? info "What is nuclei's license?"

	Nuclei is an open-source project distributed under the [MIT License](https://github.com/projectdiscovery/nuclei/blob/master/LICENSE.md).


??? info "I have more questions! 🙋"
	
	Please join our [Discord server](https://discord.gg/projectdiscovery), or we are active on [Twitter](http://twitter.com/pdnuclei) as well.