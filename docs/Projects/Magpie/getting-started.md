## Magpie

Magpie is a free, open-source framework and a collection of community developed plugins that can be used to build complete end-to-end security tools such as a CSPM or Cloud Security Posture Manager. The project was originally created and is maintained by Open Raven. We build commercial cloud native data security tools and in doing so have learned a great deal about how to discover AWS assets and their security settings at scale.

We also heard that many people were frustrated with their existing security tools that couldn't be extended  and couldn't work well with their other systems, so decided to create this Magpie framework and refactor and sync our core AWS commercial discovery code as the first plugin.

We plan to actively contribute additional modules to make Magpie a credible free open source alternative to commercial CSPM’s and welcome the community to join us in adding to the framework and building plugins.

Magpie also contains Open Raven's DMAP technology, which allows users to enumerate and identify *non native* services running on EC2 instances using a combination of port fingerprinting (think Nmap's OS fingerprinting, but on the application layer instead of the transport layer) and a little machine learning (decision trees).

## Overview


## Prereqs

*Java 11 is a prerequisite and must be installed to run Magpie.*

Out of the box Magpie supports AWS for the cloud provider and outputs discovery data to `stdout` in JSON format. The
AWS plugin utilizes the AWS Java SDK and will search for credentials as described in [Using Credentials](https://docs.aws.amazon.com/sdk-for-java/latest/developer-guide/credentials.html).

Assuming you have read credentials set up, you can start discovery by running:
```shell
./Projects
```
### Running via Docker
Builds on the `main` branch are automatically uploaded to quay.io under the `latest` tag.  See https://quay.io/repository/openraven/magpie for
all available tags. Once a stable public release is made this will also be available there.  The Docker image uses the
default config and provides no AWS credentials.

- Credentials can be added at runtime via volume mapping or passing environmental variables into the container.
- Configuration overrides can be done via environmental variable.

For example: to pass environmental variables for both credentials and configuration:
```bash
docker run -a stdout -a stderr \
--env MAGPIE_CONFIG="{'/plugins/magpie.aws.discovery/config/services': ['s3']}" \
-e AWS_ACCESS_KEY_ID -e AWS_SECRET_ACCESS_KEY -e AWS_SESSION_TOKEN \
quay.io/openraven/Projects:latest
```

The two `-a` arguments map both `stdout` and `stderr` to your local terminals, so you can redirect output as you would
with a local Mapgie execution.

### Plugins

### Community Contributed Plugins

If you've written a plugin you'd like listed please create a Pull Request with it listed here.

##### Authentcation
To use this plugin save .json file with authentication key to file then set environment variable:
```
export GOOGLE_APPLICATION_CREDENTIALS=PATH_TO_CREDENTIALS_JSON_FILE
```


The Magpie repo is [https://github.com/openraven/magpie](https://github.com/openraven/magpie). 

The security rules repo is [https://github.com/openraven/security-rules](https://github.com/openraven/security-rules).

Remediation advice can be found in our [Cloud Security Configuration Guide](Cloud Configuration Guides/guides.md).
