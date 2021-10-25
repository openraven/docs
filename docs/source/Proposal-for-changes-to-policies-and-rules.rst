This is a simple proposal to update the policies and rules system to make it easier for security engineers developing and using them. 

**Assumptions**
* We are multi-cloud and we will likely have rules that span cloud and native technologies i.e. for databases or K8.
* There is a many-to-many relationship for policies and rules that describe them. 

**Storage**
* The default repo for polices and rules will be renamed to cspm-kb.git.
* All policies and rules will be in single repo with a single /rules and a single /policies folders. Users can PR the main repo or fork / clone and point to theirs. Each policy and rule is in its own file.

**Policy Format**
* Policy files should follow a naming format using a short description in snake case ie aws_cis_benchamarks_1.
* Policies will have a flag with true and false options. Given you pass the cspm-kb repo to the scanner this makes it easy to enable or disable a specific policy and avoid duplicate rule evaluations. I can't see this being used much as evaluation should be cheap, more at a rule level (see below) but feels like sensible future proofing. 
* Policies will list rules using the file name. Given we have a rule naming format it should be obvious what it is. 
* Rename refID to policyID
* Rename name to policyName

**Rules**
* Rules files should follow a naming format using a short description in snake case i.e. no_root_access
* Rename refID to ruleID
* Rename name to ruleName 
* The ruleID should follow the naming format of the target and a sequential integer in snake case i.e. aws_001 or K8_009. The description should not reference a section of a policy).
* The ruleName should be a short human readable description in free form. 

**Question**
- Why do we need version numbers in the files if we are managing in git? _(Internally rules will likely be stored in PG instead of Git.  I'll defer to Andrews on the reasoning for version numbers - Jason )_












