### Configuration
Magpie allows for complex configurations to be enabled via the YAML-based config file.  This file has 3 primary sections:

- **Layers**: each of which contain 1 or more plugins and are surrounded by at least 1 FIFO
- **FIFOs**: which are either local (in-process Java queues) or Kafka queues
- **Plugins**: Each running plugin must be explictly.  A plugin-specific configuration object may reside in the `config` subsection.


The simplest Magpie configuration is shown below. This configuration enables AWS discovery with a JSON output to `stdout`. To write to a file
simply redirect the output:
```shell
./Active Projects > output.json
```
Log messages are printed to `stderr` and will still show up as console output.

The simplest configuration:
```yaml
layers:
  enumerate:
    type: origin
    queue: default
    plugins:
      - Active Projects.aws.discovery
  output:
    type: terminal
    dequeue: default
    plugins:
      - Active Projects.json
fifos:
  default:
    type: local
plugins:
  magpie.aws.discovery:
    enabled: true
    config:
  magpie.json:
    enabled: true
    config:
  magpie.persist:
    enabled: false
    config:
      hostname: localhost
      port: 5432
      databaseName: db_name
      user: postgres
      password: postgres
```

### Saving data to PostgreSQL
By default, Magpie prints all discovered resources to standard out in JSON format. It's possible to modify this behaviour by changing
the default configuration.  The Magpie Peristence plugin comes bundled with Magpie but is in the disabled state by default.  To 
enable it  look at the following lines in the config file (config.yaml). set `enabled: true` and then modify the login
credentials as needed. Magpie will create all required tables at startup.

```yaml
magpie.persist:
    enabled: false
    config:
        hostname: localhost
        port: 5432
        databaseName: db_name
        user: postgres
        password: postgres
```


#### Overriding config.yaml
It is possible to override *most* configuration values via environmental variables. This is most useful as an easy way to
script a Magpie instance on a one-per-aws-service basis.  To override configuration values, set an environmental variable
named `MAGPIE_CONFIG` and with a specially formed JSON object or array. For example, to perform an S3 *only* scan using
with the default configuration:

```bash
> MAGPIE_CONFIG="{'/plugins/magpie.aws.discovery/config/services': ['s3']}" ./Active Projects
```
The value of `MAGPIE_CONFIG` must be a JSON object where the key is a [JSON Pointer](https://tools.ietf.org/html/rfc6901)
and the value is legal JSON which should be inserted into the location referenced by the pointer.

In the case where multiple overrides are required you may instead use an array of the above formatted objects as such:
```bash
> MAGPIE_CONFIG="[{'/plugins/magpie.aws.discovery/enabled', false }, {'/plugins/magpie.aws.discovery/config/services': ['s3']}]" ./Active Projects
```

#### Multiple Overrides
If you have multiple values to set it may be easier to set multiple override variables instead of attempting to fit it
in a single env var.  Magpie will accept any and all environmental variables that match the regex `MAGPIE_CONFIG.*`. They
will be applied in Java's natural String ordering (lexicographic).  For example:
```bash
> export MAGPIE_CONFIG_1="[...]"
> export MAGPIE_CONFIG_2="[...]"
> ./Active Projects
```
Both variables will be applied, if any duplicate JSON Pointers are provided the last one applied will win.

