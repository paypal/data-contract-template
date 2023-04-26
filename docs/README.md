# Data Contract Template

## Table of content
* [Demographics](#Demographics)

## Demographics

### Example

```
# this a sample YAML file.  it contains a few columns from a single table in a dataset 
# to serve as an example of what the file contains.  if this were an actual file, it 
# would have every column of every table within the dataset
# for more info on rosewall datasets and yaml files refer to this link:
# https://engineering.paypalcorp.com/confluence/x/9VH7LQ

# What's this DC identification?
datasetDomain: seller_uds   # Domain
quantumName: my quantum     # Data product name
userConsumptionMode: Analytical
version: 1.1    # Together on one line with status
status: current # Together
uuid: 53581432-6c55-4ba2-a65f-72344a91553a

# Lots of information
description:
  purpose: Views built on top of pp_tables
  limitations: null
  usage: null
tenant: paypal

# Getting support
productDl:
productSlackChannel:
productFeedbackUrl:

# Physical parts / GCP / BigQuery specific
sourcePlatform: googleCloudPlatform
sourceSystem: bigQuery
datasetProject: pypl-edw     # BQ dataset
datasetName: pp_access_views # BQ dataset

kind: virtualDataset
type: tables

# Physical 
driver: null
driverVersion: null
server: null
database: pypl-edw.pp_access_views
username: '${env.username}'
password: '${env.password}'
schedulerAppName: name_coming_from_scheduler # NEW 2.1.0 Required if you want to schedule stuff, comes from DataALM.

# Data Quality
quality: null

# Tags
tags: null
```

