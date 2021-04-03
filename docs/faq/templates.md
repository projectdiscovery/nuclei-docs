??? info "What is nuclei templates?"

	Nuclei [templates](http://github.com/projectdiscovery/nuclei-templates) are core of the nuclei project, template contains the actual logic to execute for detecting various vulnerabilities and checks, template projects consist ==850==+ ready-to-use **[community-contributed](https://github.com/projectdiscovery/nuclei-templates/graphs/contributors)** vulnerability templates.


??? info "How to write nuclei templates?"
	We maintain [documentation guide](https://nuclei.projectdiscovery.io/templating-guide/) for writing new and custom nuclei templates, and [example templates](https://nuclei.projectdiscovery.io/template-examples/http/) for various module nuclei supports.

??? tip "Writing nuclei templates is useful?"

	Performing security assessment of the application is time-consuming, it's always better and time saver to automate the steps when possible. Once you found a security vulnerability, you can prepare a nuclei template by defining the required HTTP request to reproduce the issue and simply test the same vulnerability across multiple hosts with ease, worth mentioning ==you write the template for once and use forever==, as you don't need to manually test that specific vulnerability anymore.

	Here are few examples from the community making use of templates to automate the security findings.

	??? Reference
		- https://dhiyaneshgeek.github.io/web/security/2021/02/19/exploiting-out-of-band-xxe/
		- https://blog.melbadry9.xyz/fuzzing/nuclei-cache-poisoning
		- https://blog.melbadry9.xyz/dangling-dns/xyz-services/ddns-worksites
		- https://blog.melbadry9.xyz/dangling-dns/aws/ddns-ec2-current-state
		- https://blog.projectdiscovery.io/writing-nuclei-templates-for-wordpress-cves/

??? warning "I'm getting false-positive results!"
	Nuclei templates is community-contributed project and we try our best in our capacity to manually review them before merging them into template project, still it's possible that some templates with weak matcher slipped through manual verification and becomes the part of the project.

	If you identified a templates producing false positive/negative results, here are few steps that you can follow to quickly fix the templates.

	??? info "Identified template producing invalid result but not sure about it?"

		DM us on [twitter](https://twitter.com/pdnuclei) or [discord server](https://discord.gg/projectdiscovery) to confirm the same.

	??? info "Identified template producing invalid result but don't know how to fix it?"

		Just open a GitHub [issue](https://github.com/projectdiscovery/nuclei-templates/issues/new?assignees=&labels=false-positive&template=false-positive.md&title=%5Bfalse-positive%5D+template-name+) with details, and we will quickly address the problem and update the template.

	??? info "Identified template producing invalid result and know how to fix it?"

		Open a GitHub [pull request](https://github.com/projectdiscovery/nuclei-templates/pulls) with fix.

??? info "I want to contribute nuclei templates 😁"
	You are always welcome, to share your nuclei template with community you can either open [GitHub issue](https://github.com/projectdiscovery/nuclei-templates/issues/new?assignees=&labels=nuclei-template&template=submit-template.md&title=%5Bnuclei-template%5D+template-name) with template details or open a Github [pull request](https://github.com/projectdiscovery/nuclei-templates/pulls) with your nuclei templates. If you don't have a GitHub account, you can also make use of [discord server](https://discord.gg/projectdiscovery) to share the template with us and we will make sure to add them into project asap.
