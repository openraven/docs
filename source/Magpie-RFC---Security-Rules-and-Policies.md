# Magpie RFC - Security Rules and Policies

`Version 1.0`
`Last Updated: 2021-05-05`

## Overview
### Magpie
Today, Open Raven’s Magpie framework (https://github.com/openraven/magpie) can  discover AWS resources and either persist them to PostgreSQL or output JSON. The new Magpie Policy Engine will also be able to apply policies against the persisted discovery data. This data forms the basis of a modern Cloud Security Posture Manager (CSPM)--Magpie Security Rules and Security Policies. 

This document describes the planned approach and is published for community comments,  which can be made on the [Open Raven Research Slack channel](https://join.slack.com/t/open-raven-research/shared_invite/zt-np27xiev-N5rL4AcTmrQt8YkE81BIaw).

## Components
### Policies
Policies are containers of multiple rules that test for compliance. The CIS AWS security benchmark, which contains various rules (or controls - i.e., “Avoid the use of the "root account”), is an example of a policy. Policies will be independent and stored in YAML format as below. 
```yaml
id: 7a7b9dac-9cf1-4876-89b0-be228636c6c9
refId: CIS
name:  CIS Amazon Web Services Foundations
description: >
  The CIS Security Benchmarks program provides well-defined, unbiased, consensus-based
  industry best practices to help organizations assess and improve their security
enabled: true
rules:
  - 2e443d91-c3c4-a96d-677e-7cb0da4ae64e
version: 0.1.3
```

### Rules
Policies reference one or more “rules,” which contain basic information, like name, description, and remediation steps, but also contain the actual logic used to evaluate the rule (more on this later). To enable reuse, rules can be shared and referenced in multiple policies.

```yaml
id: 2e443d91-c3c4-a96d-677e-7cb0da4ae64e
refId: CIS-1.2
type: asset
name: > 
  Ensure multi-factor authentication (MFA) is enabled for all IAM users 
  that have a console password
description: >
  Multi-Factor Authentication (MFA) adds an extra layer of protection on top of a 
  username and password. With MFA enabled, when a user signs in to an AWS website, 
  they will be prompted for their username and password as well as for an 
  authentication code from their AWS MFA device. It is recommended that MFA be 
  enabled for all accounts that have a console password.
severity: high,
enabled: true,
sql: >
  SELECT configuration->"arn" as arn 
  from awsusercredentialreport
  where configuration->"password_enabled" = true AND
  configuration->"mfa_active" = false
remediation: >
  Perform the following to enable MFA:
   1. Sign in to the AWS Management Console and open the IAM console at https://console.aws.amazon.com/iam/.
   2. In the navigation pane, choose Users.
   3. In the User Name list, choose the name of the intended MFA user.
   4. Choose the Security Credentials tab, and then choose Manage MFA Device.
   5. In the Manage MFA Device wizard, choose A virtual MFA device, and then choose Next Step.
remediationDocURLs: 
  - https://docs.openraven.com/remediations/enable_mfa
version: 0.1.3
```

The main part of the rule outlined above is the “SQL” statement that queries discovered assets and MUST return AT LEAST a field named “arn” for each asset that doesn't meet the policy rule (i.e., is “in violation”).
In addition to the SQL statement, there may also be an OPTIONAL “script” statement (written in Python). See “Rule evaluation” below for how it operates.

```
...
sql: >
  SELECT awsec2instances.arn as arn, sg.configuration as sg_config
  FROM awsec2instances ec2 INNER JOIN awsec2securitygroup sg
  ON ec2.configuration->securityGroups->groupId = sg.configuration->groupId
  WHERE sg.configuration->ipPermissions->ipRanges->cidrIp = "0.0.0.0/0" 
...
```

NOTE: the SQL JOIN query above is likely incorrect (as there may be multiple security groups on an EC2 instance and multiple ipRanges in a security group). The SQL JOIN query will therefore need to use either @> (contains) or Postgres' json_* functions - the above is a simplified example for illustration only.
If necessary, additional logic can be provided via an ‘evaluate’ function. This function is passed through a list of JSON objects, being the resultset of the SQL query above. The Python function can then iterate through that list, creating a final resultset of assets (ARNs - see below) that are in violation (i.e., port 22 open to the internet).

```yaml
...
evaluator: >
  def evaluate (resultset):
    arns = []
    for asset in resultset:
      for IPPerms in asset["config"]["ipPermissions"]:
      if (IPPerms["fromPort"] == 22):
        arns.append(asset["arn"])
    return arns    
...
```

### Violations
Where assets do not meet policy rules (i.e., are “in violation”), Magpie will emit violation messages such as the one below.

```yaml
policyId: 7a7b9dac-9cf1-4876-89b0-be228636c6c9
ruleId: 2e443d91-c3c4-a96d-677e-7cb0da4ae64e
assetId: arn:aws:s3:::some-bucket-name
info: >
  optional data from evaluation above for evidence, etc [ie. policy doc]
evaluatedAt: 2021-03-08T17:14:45.340437Z
```

If, however, the evaluation output results do not contain an “ARN” field, the evaluation engine will consider this to be an error and post a message like the error message below to its error queue. Similarly, if a Python error is encountered on a rule with the (optional) second-stage processing, it will also post an error message such as the one below.

```yaml
policyId: 7a7b9dac-9cf1-4876-89b0-be228636c6c9
policyRefId: CIS
ruleId: 2e443d91-c3c4-a96d-677e-7cb0da4ae64e
ruleRefId: CIS-1.2
error: >
 Required field 'ARN' was not found in evaluation response
 evaluatedAt: "2021-03-08T17:14:45.340437Z
```

OR…

```yaml
policyId: 7a7b9dac-9cf1-4876-89b0-be228636c6c9
policyRefId: CIS
ruleId: 2e443d91-c3c4-a96d-677e-7cb0da4ae64e
ruleRefId: CIS-1.2
error" : >
 File "<string>", line 24, in <module>
 KeyError: 'ipPermisions'
 evaluatedAt: 2021-03-08T17:14:45.340437Z
```



## Application Flow
### Rule Evaluation
When a policy is evaluated, this means that each of its associated rules is evaluated against currently discovered assets (stored in the database). To help simplify defining the logic used to evaluate discovered assets and enable flexibility beyond SQL queries, there will be a “two-stage” evaluation.The second stage will be an OPTIONAL Python script that may perform additional validation logic over the returned SQL dataset.
Thus, in the first stage, an SQL statement will select from the database assets that meet basic criteria. The SQL query approach means that we can join related assets together, as the example below shows.

## Usage
Magpie is invoked via two commands: magpie-discovery and magpie-policy. 

### Discovery Only
Runs Magpie but only performs discovery (and may persist the assets, depending on the configuration). No policy is applied.

```
$> magpie-discovery
```


### Policy Only
Runs Magpie and enables the policy engine but does not perform a discovery first.  This action is most useful for testing or applying new rules against an existing set of persisted assets.

```
$> magpie-policy
```

This command requires a previously successful magpie-discovery run.

### Discovery + Policy
When run, Magpie will perform a complete discovery (based on its configuration) followed immediately by a policy evaluation and a JSON-based report.

Example Usage:

```
$> magpie-discovery && magpie-policy
```


### Create Policy Template
Creates a policy template file already pre-filled with basic data and a randomly generated GUID.  This action simplifies the process of creating a new policy by preventing copy and paste errors (GUID and ID duplication). The policy file is created in the policy folder (unless overridden).

```
$> magpie-policy --create-policy --refId=<policy ID> --name=<name>
```

Where refID is a unique human-readable identifier (for example, CIS), and the name is a title for the policy (for example, CIS Amazon Web Services Foundations).

### Create Rule Template
Creates a rule template file pre-filled with basic data and a randomly generated GUID.  This action simplifies the process of creating a new rule by preventing copy and paste errors (GUID and ID duplication). The rule file is created in the rule folder (unless overridden).

```
$> magpie-policy --create-rule --id=<rule ID> --name=<name>
```

Where refID is a unique human-readable identifier (for example, CIS-1.2) and the name is a title for the policy (for example, Ensure multi-factor authentication (MFA) is enabled for all IAM users that have a console password).


## Policy and Rule Sources
Policies are always sourced from local folders on the filesystem.  Folders may be n-levels deep, with each file containing a single policy. Multiple folders may be specified to Magpie.

Policy sources may also be git repositories specified as a URL to the root of the repository. For example, github.com/openraven/policies. In this case, Magpie will invoke the local system’s git executable and clone it locally before using it as a local source for policies.  Magpie will keep a policy cache in a local folder to save policy sources between invocations.

Filesystem paths to policy sources will be noted in all findings as metadata.

## Violations
When a policy rule matches against infrastructure data, a violation is created.  Initially violations will be output to stdout in the form of a JSON report.  Other methods of output will be added in the future.

## Configuration
All Policy Engine configuration will take place within the existing Magpie configuration file (config.yaml).  Each configuration element is overridable with environment variables (https://github.com/openraven/magpie#overriding-configyaml).

A default configuration will be provided with the Magpie distribution.

### Stay tuned for future updates and come join us for real discussions in the [Open Raven Research Slack channel](https://join.slack.com/t/open-raven-research/shared_invite/zt-np27xiev-N5rL4AcTmrQt8YkE81BIaw).
