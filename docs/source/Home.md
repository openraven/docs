Welcome to the [Open Raven Research](https://www.openraven.com/research) wiki for all things related to the [security rules](https://github.com/openraven/security-rules) that power [Magpie](https://github.com/openraven/magpie) and are incorporated in the Open Raven commercial platform.

The wiki includes documentation about how to write policies and rules as well as a section of curated guides about how to fix problems that may have been identified after running an analysis.

We also maintain a current directory page listing all the rules hosted in this repo and a section listing other public repos that share rules.

## Policies and rules directory (@TODO)

## Writing policies and rules 

### Policy and Rules overview
Policies and rules consist of crafted YAML files within a Git repository, one file per policy or rule.

#### Policy Fields
Policies contain the following fields. All fields are required unless specifically marked optional.

|Field|Data Type|Description|Sample|Required?|
|---|---|---|---|---|
|policyId|string|A unique identifier provided by Open Raven|`opnrvn-p-1`|yes|
|policyName|string|Human readable title for the policy|`AWS Security Best Practices`|yes|
|cloudProvider|enum|The provider this policy should be applied against.|`aws`|yes|
|description|string|A sentence-long description of the policy||yes|
|enabled|boolean|Whether this policy should be applied when run in Magpie|`true`|optional, default to true
|rules|array of strings|Rule filenames (the `/rules` path is implied)|`- opnrvn-r-1.yaml`|yes
|version|string|Version number for the policy|`0.9`|yes

#### Rule Fields
Rules contain the following fields. All fields are required unless specifically marked optional.

|Field|Data Type|Description|Sample|Required?|
|---|---|---|---|---|
|ruleId|string|A numerically increasing serial following the defined naming format|`opnrvn-r-12`|yes|
|type|enum|Currently only one type is supported (asset)|`asset`|yes|
|ruleName|string|Human readable title for the rule|`AWS security group allows access to known command and control destinations`|yes|
|description|string|A sentence-long description of the rule||yes|
|severity|enum|One of `high`, `medium`, `low`|`high`|yes|
|enabled|boolean|Whether this rule should be applied when run in Magpie|`true`|optional, default to true
|sql|SQL string|A sql query that returns assets that violate this policy||yes|
|eval|Python code|Python code that performs additional processing on the returned SQL results||optional|
|version|string|Version number for the rule|`0.9`|yes

### Rule naming
TODO: [Rule naming authorities and the RuleID field](https://github.com/openraven/security-rules/wiki/policies-and-rules/policy-and-rule-ids.md)

### SQL Schema and Queries
Magpie rules require SQL queries (unless the rule is is set to be a manual control).  These SQL queries are always executed against the PostgreSQL service where assets are persisted during the discovery phase.  PostgreSQL is the only database supported by Magpie and as such rules can use PG specific query features (such as https://www.postgresql.org/docs/13/functions-json.html).

Rules should not modify the database, but checks are currently done to ensure this. As such only consume rules from trusted sources.

The current schema is as follows:

|Column|Type|Description|
|---|---|---|
|document_id|`varchar(59) primary key`||
|asset_id|`varchar(255)`||
|resource_name|`varchar(255)`||
|resource_id|`varchar(255)`||
|resource_type|`varchar(255)`||
|region|`varchar(50)`||
|project_id|`varchar(255)`||
|account_id|`varchar(255)`||
|created_iso|`timestamp with time zone`||
|updated_iso|`timestamp with time zone`||
|discovery_session_id|`varchar(255)`||
|max_size_in_bytes|`integer`||
|size_in_bytes|`integer`||
|configuration|`jsonb`||
|supplementary_configuration|`jsonb`||
|tags|`jsonb`||
|discovery_meta|`jsonb`||
### Python for Advanced Rules
TODO

## Testing security rules
### Structure
Security rules could be tested using prepared tests assets which reproduce scenario violation see the `security-rules/resources/tests`
Each tests consists of
* _ruleId_ : matching test assets to the specific rule, relation is always one-to-one
* _description_ : about how the test scenario prepared and why it violates the rule
* _violatedAssetId_ : expected violated assed being returned by specified rule
* _controlAssetId_ : asset which pass the rule
* _insecureAssetGroup_ : list of assets in JSON format which simulate _insecure_ environment from rule perspective
* _secureAssetGroup_ : list of assets in JSON format which simulate _secure_ environment from rule perspective
Rules logic mostly relies on relation between assets, hence specified rule will be executed against provided assets

### Creating test resource
1. grab the required assets type by magpie-discovery. You could use magpie-quickrun, enable json and postgres output
2. redirect JSON formatted assets from the error output to the file, ensure discovery assets are also persisted in DB
3. execute the rule SQL against the DB, discovered asset_id is the one which violated the rule, and should be filled under the field _violatedAssetId_ in related test resource
4. find the asset definition in the output file from the point 2, save it to the _insecureAssetGroup_ list. In case if there a graph of assets which takes part in violation save them all
5. check the DB for other assets related to the same rule which pass it. Them should be saved under the _secureAssetGroup_ list. 
6. put the safe assetId under the test resource field _controlAssetId_ . Which will point further to safe asset setup

### Execution
Test scenario for security rules resides in magpie. (Search in magpie repository for SecurityRuleValidator class).
We use Junit5 Parameterized tests, were testcase is executed for each provided test resource file
Shortly test perform the following:
* getting the security-rules repo, and build stream of testresource files
* load policies with rules
* foreach test resource file:
  - get the referenced rule from test resource file
  - insert insecure asset group to testcontainer DB
  - execute rule against assets, and ensure expected violated asset returned
  - cleanup DB
  - execute secure group of assets
  - execute rule once again, no assets should be returned violating the rule

Developer able to run the testcase in following modes:
* using local repo specifying the parameter `-Drepository=<absolute-path-to-local-git-repo>` - execute all tests in repository. Useful for the full scope testing on branch level and playing with assets definition.
* using local test-resource path specifying the parameter `-DtestResourcePath=<absolute-path-to-resource>` - execute all tests in folder or a single test if provided path is file. Useful for single rule testing or current list of rules in terms of their development to not waste time for full scope testing.
* default execution without any parameters - checkout the master branch of `security-rules` repo and execute it

### Build
Security-rules testing is triggered on each PR and merge to master for security-rules and magpie repo.

## How-to guides for fixing cloud security issues

- [AWS - Amazon Web Services](https://github.com/openraven/security-rules/wiki/aws-remediation)
- [GCP - Google Cloud Platform](https://github.com/openraven/security-rules/wiki/gcp-remediation)

Additional help and information is available on the [Open Raven research](https://www.openraven.com/research) site including screencasts and videos covering these topics and more.

We encourage people to join our public [Slack channel](https://join.slack.com/t/open-raven-research/shared_invite/zt-np27xiev-N5rL4AcTmrQt8YkE81BIaw) to follow development and ask questions.