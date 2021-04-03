??? info "What is nuclei?"

	Nuclei is a fast and customizable vulnerability scanner based on simple YAML-based vulnerability templates.
	
	It has two components, [Nuclei](http://github.com/projectdiscovery/nuclei) engine - the core of the project allows scripting HTTP / DNS / Network / Headless / File protocols based checks in a very simple to read-and-write YAML-based format. Nuclei [templates](http://github.com/projectdiscovery/nuclei-templates) - ready-to-use **community-contributed** vulnerability templates.


??? info "What was genesis behind nuclei?"
	Traditional scanners always lacked the features to allow easy-to-write custom checks on top of their engine, this is how we started developing Nuclei with a core focus on simplicity, modularity, and the ability to scan on a large number of assets.

	We wanted something that was simple enough to be used by ==**everyone**== while complex enough to integrate into the modern web with all its intricacies. The features implemented in nuclei are tailored to allow very rapid prototyping of complex security checks.

??? info "Type of modules nuclei engine supports?"

	Nuclei engine supports following type of modules.

	- [HTTP](https://nuclei.projectdiscovery.io/templating-guide/protocols/http/)
	- [DNS](https://nuclei.projectdiscovery.io/templating-guide/protocols/dns/)
	- [TCP](https://nuclei.projectdiscovery.io/templating-guide/protocols/network/)
	- [FILE](https://nuclei.projectdiscovery.io/templating-guide/protocols/file/)

??? info "How well maintained is this project?"

	Nuclei project is developed and maintained by [ProjectDiscovery](https://projectdiscovery.io/#/) team and is in active development (we tend to push release every other week).

??? warning "Found results with nuclei, time to report it?"

	Wait for a moment, even you detected valid security issue using nuclei, it's always good idea to have a second look before reporting it.

	??? info "How to validate the results again!"

		Once you found the results, you have vulnerable ==URL== and ==template==, run the template again with `-debug` flag to inspect vulnerable response against expected matcher defined in the template, in this way, you will be sure about identified vulnerability.

??? warning "How much traffic nuclei generates?"
	
	As default Nuclei makes **1234** HTTP requests in total against single target upon running **all nuclei-templates** directory, this includes 801 nuclei templates from [v8.1.9](https://github.com/projectdiscovery/nuclei-templates/releases/tag/v8.1.9) release.

	!!! info ""
		As default, few templates listed [here](https://github.com/projectdiscovery/nuclei-templates/blob/master/.nuclei-ignore) are excluded from default scans.

??? info "I've more questions?"
	
	You are welcome to join our discord server at https://discord.gg/projectdiscovery, and we are active on [pdnuclei](http://twitter.com/pdnuclei) as well.