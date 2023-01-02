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

	The nuclei project is actively developed and maintained by the [ProjectDiscovery](https://projectdiscovery.io/#/) team, and generally releases every 2 weeks.

??? tip "How can I support/contribute to this project? 💙"

	To help keep project momentum, we request everyone to write and share new templates with the community in the [template project](https://github.com/projectdiscovery/nuclei-templates). Please help us maintain this public, ready to use, and up-to-date nuclei template repository.

	If you found an interesting/unique security issue using nuclei and want to share the process walk-through in the form of a blog, we are happy to publish your guest post on the [ProjectDiscovery blog](https://blog.projectdiscovery.io).

??? warning "I found results with nuclei. When should I report it?"

	**Wait a minute** -- after nuclei detected a security issue, it's always advised to have a second look before reporting it. Here's a tip to confirm/validate the issues.

	??? tip "How do I validate nuclei results?"

		Once nuclei finds a result, and you have vulnerable ==target== and ==template==, rerun the template with ==`-debug`== flag to inspect the output against the expected matcher defined in the template. In this way, you can confirm the identified vulnerability.

??? warning "How much traffic does nuclei generate?"
	
	By default nuclei will make several thousand requests (both HTTP protocol and other services) against a single target when running **all nuclei-templates**. This stems from over 3500 nuclei templates in the [[template releases](https://github.com/projectdiscovery/nuclei-templates/releases/), with more added daily.

	!!! info ""
		As default, few templates listed [here](https://github.com/projectdiscovery/nuclei-templates/blob/master/.nuclei-ignore) are excluded from default scans.


??? warning "Is it safe to run nuclei?"

	We consider two factors to say =="safe"== in context of nuclei -

	1. The **traffic** nuclei makes against the target website.
	2. The **impact** templates have on the target website.

	!!! check "HTTP Traffic"

		Nuclei usually makes fewer HTTP requests than the number of templates selected for a scan due to its intelligent request reduction. While some templates contain multiple requests, this rule generally holds true across most scan configurations.

	!!! check "Safe Templates"

		The nuclei templates project houses a variety of templates which perform fuzzing and other actions which may result in a DoS against the target system (see [the list here](https://github.com/projectdiscovery/nuclei-templates/blob/master/.nuclei-ignore)). To ensure  these templates are not accidentally run, they are tagged and excluded them from the default scan. These templates can be only executed when explicitly invoked using the `-itags` option.

??? info "What is nuclei's license?"

	Nuclei is an open-source project distributed under the [MIT License](https://github.com/projectdiscovery/nuclei/blob/master/LICENSE.md).


??? info "I have more questions! 🙋"
	
	Please join our [Discord server](https://discord.gg/projectdiscovery), or contact us via [Twitter](http://twitter.com/pdnuclei).


??? warning "Problem related to dependencies for using nuclei on Ubuntu AMD64 and Docker"

	If you are using nuclei on a machine running Ubuntu AMD64 or in a Docker environment, you may encounter issues with headless browsers not being able to run due to missing dependencies related to specific OS-shared libraries. To fix these errors, you can pre-install the browser for the distribution or install the necessary dependencies by following these steps:

	```sh
	sudo apt update
	sudo snap refresh
	sudo apt install zip curl wget git
	sudo snap install golang --classic
	wget -q -O - https://dl-ssl.google.com/linux/linux_signing_key.pub | sudo apt-key add - 
	sudo sh -c 'echo "deb http://dl.google.com/linux/chrome/deb/ stable main" >> /etc/apt/sources.list.d/google.list'
	sudo apt update 
	sudo apt install google-chrome-stable
	```

	Run the following command to install the necessary dependencies:

	```
	sudo apt-get install libnss3 libgconf-2-4
	```

	If you encounter an error similar to "libnss3.so: cannot open shared object file: No such file or directory," try running the following command:

	```
	sudo apt-get install libnss3-dev
	```

	Error type examples:
	```
	Error:      	Expected nil, but got: &errors.errorString{s:"[launcher] Failed to launch the browser, the doc might help https://go-rod.github.io/#/compatibility?id=os: /root/.cache/rod/browser/chromium-1018003/chrome-linux/chrome: error while loading shared libraries: libnss3.so: cannot open shared object file: No such file or directory\n"}
	```
	```
	could not create browser
	```
	```
	Command '/usr/bin/chromium-browser' requires the chromium snap to be installed.
	Please install it with:
	snap install chromium
	```
