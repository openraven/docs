#  GCP Client dependencies
Required maven dependencies are as follows:
```
<dependencyManagement>
  <dependencies>
    <dependency>
      <groupId>com.google.cloud</groupId>
      <artifactId>libraries-bom</artifactId>
      <version>20.2.0</version>
      <type>pom</type>
      <scope>import</scope>
    </dependency>
.
.
.
  </dependencies>

</dependencyManagement>

<dependencies>
  <dependency>
    <groupId>com.google.cloud</groupId>
    <artifactId>google-cloud-bigtable</artifactId>
  </dependency>
<dependencies>
```

Similar to AWS each service have different dependency. In above example it's bigtable

Example usage:
```
// Lists bigdata tables in the current instance.
try {
  List<String> tableIds = adminClient.listTables();
  for (String tableId : tableIds) {
    System.out.println(tableId);
  }
} catch (NotFoundException e) {
  System.err.println("Failed to list tables from a non-existent instance: " + e.getMessage());
}
```

More detailed information available here https://cloud.google.com/java/docs/setup

#  Authentication methods
If no credentails are specified in code. GCP will look for credentials in environment varialbes such as GOOGLE_APPLICATION_CREDENTIALS

To use this authenitication method use save .json file with authentication key to file then set:
```
export GOOGLE_APPLICATION_CREDENTIALS=PATH_TO_CREDENTIALS_JSON_FILE
```
# Top services to support
Some popular services we should start with:
- Google Kubernates Engine - listClusters()
- BigQuerry - listDatasets()
- Cloud Functions - listFunctions()
- Secret Manager - listSecrets()
- Memorystore for Redis - listInstances()
- Memorystore for Memcache - listInstances()
- IoT Core - listDevices()
- Data Catalog - listEntries()
- Cloud Tasks - listTask(), listQueues()
- Cloud Key Management Service - listCryptoKeys()
- BigTable - listInstances(), listClusters()
- AutoML - listModels(), listDatasets()

Api references here https://cloud.google.com/java/docs/reference
# Persistence
For persistance the logical solution is to create class similar to AWSResource flow, but with more general member and class names to fit GCP and other cloud services in future

```
public class CloudResource {
  public String documentId;
  public String uniqueId;
  public String resourceName;
  public String resourceId;
  public String resourceType;
  public String region;
  public String accountId;
  public String createdIso;
  public String updatedIso;
  public String discoverySessionId;
  public Long maxSizeInBytes = null;
  public Long sizeInBytes = null;

  public JsonNode configuration;
  public JsonNode supplementaryConfiguration;
  public JsonNode tags;
  public JsonNode discoveryMeta;
}
```
