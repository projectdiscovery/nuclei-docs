??? info "What is nuclei templates?"

	Nuclei [templates](http://github.com/projectdiscovery/nuclei-templates) are core of the nuclei project, template contains the actual logic to execute for detecting various vulnerabilities and checks, template projects consist ==880==+ ready-to-use **[community-contributed](https://github.com/projectdiscovery/nuclei-templates/graphs/contributors)** vulnerability templates.


??? info "How to write nuclei templates?"
	We maintain [documentation guide](https://nuclei.projectdiscovery.io/templating-guide/) for writing new and custom nuclei templates, and [example templates](https://nuclei.projectdiscovery.io/template-examples/http/) for various module nuclei supports.

??? tip "Writing nuclei templates is useful?"

	Performing security assessment of the application is time-consuming, it's always better and time saver to automate the steps when possible. Once you found a security vulnerability, you can prepare a nuclei template by defining the required HTTP request to reproduce the issue and test the same vulnerability across multiple hosts with ease. Worth mentioning ==you write the template once and use it forever==, as you don't need to manually test that specific vulnerability anymore.

	Here are few examples from the community making use of templates to automate the security findings.



	??? Reference
		- https://dhiyaneshgeek.github.io/web/security/2021/02/19/exploiting-out-of-band-xxe/
		- https://blog.melbadry9.xyz/fuzzing/nuclei-cache-poisoning
		- https://blog.melbadry9.xyz/dangling-dns/xyz-services/ddns-worksites
		- https://blog.melbadry9.xyz/dangling-dns/aws/ddns-ec2-current-state
		- https://blog.projectdiscovery.io/writing-nuclei-templates-for-wordpress-cves/

??? info "How to run nuclei templates?"

	Nuclei templates can be executed using template name or tags, using `-t`, `-tags` flag, respectively.

	```
	nuclei -tags cve -list target_urls.txt
	```

??? info "I want to contribute nuclei templates 😁"

	You are always welcome to share your nuclei template with the community you can either open [GitHub issue](https://github.com/projectdiscovery/nuclei-templates/issues/new?assignees=&labels=nuclei-template&template=submit-template.md&title=%5Bnuclei-template%5D+template-name) with template details or open a Github [pull request](https://github.com/projectdiscovery/nuclei-templates/pulls) with your nuclei templates. If you don't have a GitHub account, you can also make use of [discord server](https://discord.gg/projectdiscovery) to share the template with us and we will make sure to add them into the project asap.


??? warning "I'm getting false-positive results!"
	Nuclei templates is **community-contributed** project, and we try our best in our capacity to manually review them before merging them into template project, still, it's possible that some templates with weak matcher slipped through manual verification and becomes part of the project producing false-positive results. **Templates are as good as its matchers.**

	If you identified templates producing false positive/negative results, here are few steps that you can follow to fix the templates quickly.

	??? info "Found template producing false +ve/-ve result but not sure about it?"

		DM us on [twitter](https://twitter.com/pdnuclei) or [discord server](https://discord.gg/projectdiscovery) to confirm the validity of the template.

	??? info "Found template producing false +ve/-ve result but don't know how to fix it?"

		Please open a GitHub [issue](https://github.com/projectdiscovery/nuclei-templates/issues/new?assignees=&labels=false-positive&template=false-positive.md&title=%5Bfalse-positive%5D+template-name+) with details, and we will quickly address the problem and update the template.

	??? info "Found template producing a false +ve/-ve result and know how to fix it?"

		Please open a GitHub [pull request](https://github.com/projectdiscovery/nuclei-templates/pulls) with fix.

??? warning "I'm not able to run all templates!"

	Nuclei follow the default list of templates that are excluded from default run and are listed here https://github.com/projectdiscovery/nuclei-templates/blob/master/.nuclei-ignore, this is to ensure someone not running few templates that are not meant to run along with all templates or excluded to run safe scans. We do not suggest running them unless you are running them for the specific use case.

??? warning "Templates exist on GitHub but not running with nuclei?"

	When you download or update nuclei templates using nuclei binary, it downloads all the templates from the latest release. All templates added after release exist in the master branch and added to nuclei when a new template release would be created.