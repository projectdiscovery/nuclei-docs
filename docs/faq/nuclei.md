??? info "What is nuclei?"

	Nuclei is a fast and customizable vulnerability scanner based on simple **YAML-based templates**.
	
	It has two components, [Nuclei](http://github.com/projectdiscovery/nuclei) engine - the core of the project allows scripting HTTP / DNS / Network / Headless / File protocols based checks in a very simple to read-and-write YAML-based format. Nuclei [templates](http://github.com/projectdiscovery/nuclei-templates) - ready-to-use **community-contributed** vulnerability templates.


??? info "What was the genesis behind nuclei?"
	Traditional scanners always lacked the features to allow easy-to-write custom checks on top of their engine. And this is how we started developing Nuclei with a core focus on simplicity, modularity, and the ability to scan on many assets.

	We wanted something simple enough to be used by ==**everyone**== while complex enough to integrate into the modern web with its intricacies. The features implemented in nuclei are tailored to allow very rapid prototyping of complex security checks.

??? info "Type of modules nuclei engine supports?"

	Nuclei engine supports the following type of modules.

	- [HTTP](https://nuclei.projectdiscovery.io/templating-guide/protocols/http/)
	- [DNS](https://nuclei.projectdiscovery.io/templating-guide/protocols/dns/)
	- [TCP](https://nuclei.projectdiscovery.io/templating-guide/protocols/network/)
	- [FILE](https://nuclei.projectdiscovery.io/templating-guide/protocols/file/)

??? info "What kind of scans can I perform with nuclei?"

	Nuclei can be used to detect security vulnerabilities in **web applications**, **networks**, **DNS** based misconfiguration, and **secret scanning** in source code or files on the local file system.

??? info "How well maintained is this project?"

	Nuclei project is developed and maintained by [ProjectDiscovery](https://projectdiscovery.io/#/) team and is in active development (we tend to push release every other week).

??? tip "How to support/contribute to this project? 💙"

	To keep us motivated to work on this project, we request you to write/share new nuclei templates with the community at [template project](https://github.com/projectdiscovery/nuclei-templates) and help us to maintain this public and ready to use / up-to-date nuclei templates.

	If you found something interesting/unique security issue using nuclei and wanted to share the process walk-through with everyone in the form of a blog, we are happy to publish your guest blog at https://blog.projectdiscovery.io.

??? warning "Found results with nuclei, time to report it?"

	**Wait for a moment**, after nuclei detected security issue, it's always advised to have a second look before reporting it. Here is a tip to confirm/validate the found matches.

	??? tip "How to validate nuclei results!"

		Once nuclei found the results, you have vulnerable ==URL== and ==template==, rerun the template with ==`-debug`== flag to inspect vulnerable response against expected matcher defined in the template. In this way, you can confirm the identified vulnerability.

??? warning "How much traffic nuclei generate?"
	
	As default nuclei makes **1234** HTTP requests in total against single target upon running **all nuclei-templates** directory, this includes 801 nuclei templates from [v8.1.9](https://github.com/projectdiscovery/nuclei-templates/releases/tag/v8.1.9) release.

	!!! info ""
		As default, few templates listed [here](https://github.com/projectdiscovery/nuclei-templates/blob/master/.nuclei-ignore) are excluded from default scans.


??? warning "Is it safe to run nuclei?"

	We consider two factors to say =="safe"== in context of nuclei -

	1. The **traffic** nuclei make against the target website.
	2. The **impact** templates have on the target website.

	!!! check "HTTP Traffic"

		Out of the box, nuclei make around **1200 - 1300** HTTP requests in total for a given target when ==all== available public templates are executed, which is considered relatively low in numbers and will grow as the template number grows.

	!!! check "Safe Templates"

		Nuclei templates project houses various kind of template which also includes fuzzing and templates resulting DOS on the target system, to ensure no one accidentally run these templates, we tagged, and [excluded](https://github.com/projectdiscovery/nuclei-templates/blob/master/.nuclei-ignore) these templates from default nuclei scan, these templates can be only executed when the user explicitly instructs nuclei to run them. 

??? info "What is the license of nuclei?"

	Nuclei is an open-source project distributed under [MIT License](https://github.com/projectdiscovery/nuclei/blob/master/LICENSE.md).


??? info "I've more questions. 🙋"
	
	You are welcome to join our [Discord server](https://discord.gg/projectdiscovery), and we are active on [Twitter](http://twitter.com/pdnuclei) as well.