### DMAP
Runing `magpie-dmap` requires that DMAP-Predictions be running locally.  The easiest way is through Docker:
```shell
docker run -p 10234:10234 -d quay.io/openraven/dmap-predictions
```

#### DMAP Requirements
- IAM roles for creating lambda roles and managing lambda functions
- EC2 assets persisted into PostgreSQL (via the Magpie peristence plugin)
- Locally running DMAP-Predictions service

