Policies have a policyId field and rules have a ruleId field, a reference value which is the unique identifier for a 
policy and a rule.

To solve the problem of uniqueness in a human-readable manner we are inspired by a scheme and process that was developed
to [
manage CVE names and numbers](https://www.govinfo.gov/content/pkg/GOVPUB-C13-e1840672bcc4d823d5a2b11dcb45d280/pdf/GOVPUB-C13e1840672bcc4d823d5a2b11dcb45d280.pdf). By standardizing on a naming and numbering scheme the CVE developers were able
to create

* a comprehensive list of publicly known software flaws
* a globally unique name to identify each vulnerability
* a basis for discussing priorities and the risks of vulnerabilities
* a way for disparate products and services to integrate vulnerability information

Similarly, by standardizing on a naming and numbering scheme we can create

* a comprehensive list of known cloud security rules
* a globally unique name to identify each policy and each rule
* a basis for discussing policies, vulnerabilities, rules and remediation
* a way for a user of disparate products and services to integrate policies and rules into their products and services

A policyId is made up of two parts
```magpie naming authority code``` and ```type```and ```number``` seperated by a ```-``` (hyphen).

* example opnrvn-p-1


A ruleId is made up of two parts
```magpie naming authority code``` and ```type```and ```number``` seperated by a ```-``` (hyphen).

* example opnrvn-r-1

### Magpie Naming Authority Code
A Magpie naming authority is any individual or organization that wants develop and publish policies and or public 
rules. Open Raven, the  developers of Magpie are a Magpie naming authority using the policyId and ruleId opnrvn. While 
not 
required,
we recommend people that are developing private rules also register for a namespace to avoid potential local rule clashes.

If you would like to be a Magpie rule naming authority submit a PR against this page with your details and your 
preferred 
six character string.

The current Magpie Naming Authorities and their respective namespaces are 

[Open Raven](https://www.openraven.com) - opnrvn

### Type
* a policy type is specified with a p
* a rule type is specified with an r

### Numbers
Numbers should start with 1 and incrementally add with whole numbers.

### Additional Notes
* everything should be lowercase