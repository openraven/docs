#### AWS
Magpie supports AWS as a core plugin out of the box. Checked boxes are complete and available today, the unchecked are on the roadmap for completion. We have already built the code for all services in the list, but need to port them over from a previous framework.

- [x] EC2
- [x] S3
- [x] Athena
- [x] Batch
- [x] Backup
- [x] Cassandra
- [x] Cloudfront
- [x] Cloudsearch
- [x] Cloudtrail
- [x] CloudWatch
- [x] DynamoDB
- [x] EB
- [x] ECS
- [x] EFS
- [x] EKS
- [x] Elastic Cache
- [x] ELB
- [x] ELBv2
- [x] EMR
- [x] ESS
- [x] FSX
- [x] Glacier
- [x] GuardDuty  
- [x] IAM
- [x] KMS
- [x] Lakeformation
- [x] Lambda
- [x] Lightsail
- [x] QLDB
- [x] RDS
- [x] Redshift
- [x] Route 53
- [x] Secrets Manager
- [x] SecurityHub  
- [x] SNS
- [x] SSM
- [x] Storage Gateway
- [x] VPC

##### Per region discovery
By default the Magpie AWS Plugin will run discovery in all regions.  To narrow down discovery to a subset
of regions edit the plugins.magpie.aws.discovery.config.regions value to an array of desired region names, for example:

```yaml
plugins:
  magpie.aws.discovery:
    enabled: true
    config:
      regions:
        - us-east-2
        - us-east-1
```