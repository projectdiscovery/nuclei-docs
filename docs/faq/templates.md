??? info "What are nuclei templates?"

	Nuclei [templates](http://github.com/projectdiscovery/nuclei-templates) are the core of the nuclei project. The templates contain the actual logic that is executed in order to detect various vulnerabilities. The project consists of **several thousand** ready-to-use **[community-contributed](https://github.com/projectdiscovery/nuclei-templates/graphs/contributors)** vulnerability templates.

??? info "How can I write nuclei templates?"

	We maintain a [documentation guide](https://nuclei.projectdiscovery.io/templating-guide/) for writing new and custom nuclei templates. We also have [sample templates](https://nuclei.projectdiscovery.io/template-examples/http/) for various modules nuclei supports.


??? tip "Is writing nuclei templates useful?"

	Performing security assessment of an application is time-consuming. It's always better and time-saving to automate steps whenever possible. Once you've found a security vulnerability, you can prepare a nuclei template by defining the required HTTP request to reproduce the issue, and test the same vulnerability across multiple hosts with ease. It's worth mentioning ==you write the template once and use it forever==, as you don't need to manually test that specific vulnerability any longer.

	Here are few examples from the community making use of templates to automate the security findings.



	??? Reference
		- https://dhiyaneshgeek.github.io/web/security/2021/02/19/exploiting-out-of-band-xxe/
		- https://blog.melbadry9.xyz/fuzzing/nuclei-cache-poisoning
		- https://blog.melbadry9.xyz/dangling-dns/xyz-services/ddns-worksites
		- https://blog.melbadry9.xyz/dangling-dns/aws/ddns-ec2-current-state
		- https://blog.projectdiscovery.io/writing-nuclei-templates-for-wordpress-cves/

??? info "How do I run nuclei templates?"

	Nuclei templates can be executed using a template name or with tags, using `-templates` (`-t`) and `-tags` flag, respectively.

	```
	nuclei -tags cve -list target_urls.txt
	```

??? info "I want to contribute nuclei templates! 😁"

	You are always welcome to share your nuclei templates with the community. You can either open a [GitHub issue](https://github.com/projectdiscovery/nuclei-templates/issues/new?assignees=&labels=nuclei-template&template=submit-template.md&title=%5Bnuclei-template%5D+template-name) with the template details or open a GitHub [pull request](https://github.com/projectdiscovery/nuclei-templates/pulls) with your nuclei templates. If you don't have a GitHub account, you can also make use of the [discord server](https://discord.gg/projectdiscovery) to share the template with us.


??? warning "I'm getting false-positive results!"
	The nuclei template project is a **community-contributed project**. The ProjectDiscovery team manually reviews templates before merging them into the project. Still, there is a possibility that some templates with weak matchers will slip through the verification. This could produce false-positive results. **Templates are only as good as their matchers.**

	If you identified templates producing false positive/negative results, here are few steps that you can follow to fix them quickly.

	??? info "I found a template producing false positive or negative results, but I'm not sure if this is accurate."

		Direct message us on [Twitter](https://twitter.com/pdnuclei) or [Discord](https://discord.gg/projectdiscovery) to confirm the validity of the template.

	??? info "I found a template producing false positive or negative result and I don't know how to fix it."

		Please open a GitHub [issue](https://github.com/projectdiscovery/nuclei-templates/issues/new?assignees=&labels=false-positive&template=false-positive.md&title=%5Bfalse-positive%5D+template-name+) with details, and we will work to address the problem and update the template.

	??? info "I found a template producing a false positive or negative result and I know how to fix it."

		Please open a GitHub [pull request](https://github.com/projectdiscovery/nuclei-templates/pulls) with fix.

??? warning "I'm not able to run all templates!"

	The nuclei templates project houses a variety of templates which perform fuzzing and other actions which may result in a DoS against the target system (see [the list here](https://github.com/projectdiscovery/nuclei-templates/blob/master/.nuclei-ignore)). To ensure  these templates are not accidentally run, they are tagged and excluded them from the default scan. These templates can be only executed when explicitly invoked using the `-itags` option.

??? warning "Templates exist on GitHub but are not running with nuclei?"

	When you download or update nuclei templates using the nuclei binary, it downloads all the templates from the latest **release**. All templates added after the release exist in the [master branch](https://github.com/projectdiscovery/nuclei-templates) and are added to nuclei when a new template release is created.
