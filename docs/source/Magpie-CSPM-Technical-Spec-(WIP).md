# Magpie CSPM Tech Spec
This document describes the the technical specifications for Magpie CSPM. It's based on the earlier [Magpie RFC](https://github.com/openraven/magpie/wiki/Magpie-RFC---Security-Rules-and-Policies). Where this doc differs from the RFC, this one takes precedence.

This document dives into the `magpie-policy` implementation, as the existing `magpie` executable will be renamed `magpie-discovery` and its behavior is already implemented.


![Magpie CSPM Flow](https://github.com/openraven/magpie/raw/main/.github/magpie-cspm-flow_v2.png)

The Magpie Policy flow can be divided into three stages that are executed sequentially:

1. Policy Acquisition
2. Policy Analysis
3. Reporting

The Policy Acquisition Service is responsible for retrieving and deserializing policy and rule files from disk.  The Policy Analysis Service takes those policies and applies them against the persisted discovery data and outputs findings to the Reporting Service.  In the initial release the Reporting Service will output a text-based report.


## Policy/Rule Acquisition
When `magpie-policy` runs it must have a source of Policies and Rules to execute. These must reside on the local system in order for the Policy Engine to make use of them. This concern falls to the Policy Acquisition service, which is responsible for making sure all local policies/rules have been downloaded locally and loaded into the Magpie runtime.

Polcies and rules are packaged up in Policy Repositories. A repository may be a literal Git repository or simply a folder in the expected repository format.  A Policy Repository takes the following format:

```
(repo folder)
  |-> policies
      |- <policy title a>.yaml
      |- <policy title b>.yaml
      ...
      \- <policy title n>.yaml
  \-> rules
      |- <rule title a>.yaml
      |- <rule title b>.yaml
      ...
      \- <rule title n>.yaml
```

All policy repositories should be stored in a common location (default to ~/.magpie/).  The folder will have one of two forms 1) Remote and 
2) Local.  It is expected that most users will use Remote repositories.  All repos are specified in `config.yaml` but remote repos will contain a git URL such as `github.com/openraven/cis-policy-1.2` or similar. Local repos will contain only a folder name.

`config.yaml` Example:
```yaml
policies:
  root: ~/.magpie/policies
  repositories:
   - github.com/openraven/cis-policy-1.2
   - github.com/openpolicies/aws-iam
   - custom-rds
```
In the above example, the first two policies are from Github while the final policy is a custom-made one. All 3 reside in the default policy folder `~/.magpie/policies`.  These values can be overridden via environmental variable.


### Remote Repository
Before each run, the Policy Acquisition Service should either do a `git clone` if the folder does not exist or a `git pull` if it does. The `magpie-policy` CLI should provides options for *not* updating policies to save on time.   

*Use the System Git executable, not a Java library. If `git` is not present, throw an error*

### Local Repository
These are considered static folders. If they are missing an error should be thrown but otherwise no updates are attempted (there is no git source to pull from).

Magpie's `config.yaml` specifies the policies that should be loaded, while the policy files specify the rules.

## Build Assembly
`magpie-parent/magpie-cli` currently packages up a single fat jar, while the assembly builds a distribution zip with the following format:
```
10:05 $ unzip magpie-0.1.0-SNAPSHOT.zip 
Archive:  magpie-0.1.0-SNAPSHOT.zip
   creating: magpie-0.1.0-SNAPSHOT/
   creating: magpie-0.1.0-SNAPSHOT/plugins/
  inflating: magpie-0.1.0-SNAPSHOT/logging.xml  
  inflating: magpie-0.1.0-SNAPSHOT/magpie  
  inflating: magpie-0.1.0-SNAPSHOT/config.yaml  
  inflating: magpie-0.1.0-SNAPSHOT/plugins/magpie-aws-0.1.0-SNAPSHOT.jar  
  inflating: magpie-0.1.0-SNAPSHOT/plugins/magpie-json-0.1.0-SNAPSHOT.jar  
  inflating: magpie-0.1.0-SNAPSHOT/plugins/magpie-persist-0.1.0-SNAPSHOT.jar  
  inflating: magpie-0.1.0-SNAPSHOT/plugins/plugins.txt  
  inflating: magpie-0.1.0-SNAPSHOT/magpie-cli.jar  
```

This setup used a single executable (shell) script `magpie`.  The new setup should instead have two shell scripts that each execute a different class (one for discovery, another for policy).  Instead it should look like:
```
10:05 $ unzip magpie-0.1.0-SNAPSHOT.zip 
Archive:  magpie-0.1.0-SNAPSHOT.zip
   creating: magpie-0.1.0-SNAPSHOT/
   creating: magpie-0.1.0-SNAPSHOT/plugins/
  inflating: magpie-0.1.0-SNAPSHOT/logging.xml  
  inflating: magpie-0.1.0-SNAPSHOT/magpie-discovery  <-- Former 'magpie' with a new name
  inflating: magpie-0.1.0-SNAPSHOT/magpie-policy     <-- New script to execute magpie policy
  inflating: magpie-0.1.0-SNAPSHOT/config.yaml  
  inflating: magpie-0.1.0-SNAPSHOT/plugins/magpie-aws-0.1.0-SNAPSHOT.jar  
  inflating: magpie-0.1.0-SNAPSHOT/plugins/magpie-json-0.1.0-SNAPSHOT.jar  
  inflating: magpie-0.1.0-SNAPSHOT/plugins/magpie-persist-0.1.0-SNAPSHOT.jar  
  inflating: magpie-0.1.0-SNAPSHOT/plugins/plugins.txt  
  inflating: magpie-0.1.0-SNAPSHOT/magpie-cli.jar  <-- Contains two main classes (discovery, policy)
```
