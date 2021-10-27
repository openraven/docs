## Magpie Architecture
Magpie relies on plugins for all its integration capabilities.  They are the core of the framework and key to integration
with both cloud providers and downstream processing and storage.

*Magpie is essentially a series of layers separated by FIFOs.*

Depending on the configuration, these FIFOs are either **1) Java queues** (in the default configuration) or
**2) Kafka queues**.  Using Kafka queues allows Magpie to run in a distributed and highly scalable fashion where
each layer may exist on separate compute instances.

![Magpie Architecture](https://raw.githubusercontent.com/openraven/magpie-api/main/media/magpie_architecture.png?token=AAHX2PKUJYSKWMDS333MPSTALXTGC)

## Pre Reqs
*Java 11 is a prerequisite and must be installed to run Magpie.*

Out of the box Magpie supports AWS for the cloud provider and outputs discovery data to `stdout` in JSON format. The
AWS plugin utilizes the AWS Java SDK and will search for credentials as described in [Using Credentials](https://docs.aws.amazon.com/sdk-for-java/latest/developer-guide/credentials.html).

Assuming you have read credentials set up, you can start discovery by running:
```shell
./Projects
```

## Building Magpie from Source
```shell
git clone git@github.com:openraven/Projects.git
cd Projects
mvn clean package install && mvn --projects Projects-cli assembly:single
```

The distribution zip file will be located in `magpie-cli/target/magpie-<version>.zip`

Alternatively you can download the latest snapshot build by going to Action->(choose latest) and click the `magpie-cli` artifact,
which will download a zip distribution.  