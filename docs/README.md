# Data Contract Template

## Executive summary
This document describes the keys and values expected in a YAML data contract. It is divided in multiple sections: [demographics](#Demographics), [dataset & schema](#Dataset-&-schema), [data quality](#Data-quality), [pricing](#Pricing), [stakeholders](#Stakeholders), [roles](#Roles), [service-level agreement](#Service-level-agreement), and [other properties](#Other-properties). Each section starts with at least an example followed by definition of the each field/key.

## Table of content
* [Demographics](#Demographics)
* [Dataset & schema](#Dataset-&-schema)
* [Data quality](#Data-quality)
* [Pricing](#Pricing)
* [Stakeholders](#Stakeholders)
* [Roles](#Roles)
* [Service-level agreement](#Service-level-agreement)
* [Other properties](#Other-properties)

## Notes
* This contract is containing example values, we reviewed very carefully the consistency of those values, but we cannot guarantee that there are no errors. If you spot one, please raise an [issue](https://github.com/paypal/data-contract-template/issues).
* Some fields have `null` value: even if it is equivalent to not having the field in the contract, we wanted to have the field for illustration purpose.
* This contract leverages BigQuery but should be **platform agnostic**. If you think it is not the case, please raise an [issue](https://github.com/paypal/data-contract-template/issues).

## Demographics
Thia section contains general information about the contract.

### Example

```YAML
# What's this data  identification?
datasetDomain: seller # Domain
quantumName: my quantum # Data product name
userConsumptionMode: Analytical
version: 1.1.0 # Version follows semantic versioning
status: current
uuid: 53581432-6c55-4ba2-a65f-72344a91553a

# Lots of information
description:
  purpose: Views built on top of the seller tables.
  limitations: null
  usage: null
tenant: paypal

# Getting support
productDl: product-dl@paypal.com
productSlackChannel: '#product-help'
productFeedbackUrl: null

# Physical parts / GCP / BigQuery specific
sourcePlatform: googleCloudPlatform
sourceSystem: bigQuery
datasetProject: edw # BQ dataset
datasetName: access_views # BQ dataset

kind: virtualDataset
type: tables

# Physical access
driver: null
driverVersion: null
server: null
database: pypl-edw.pp_access_views
username: '${env.username}'
password: '${env.password}'
schedulerAppName: name_coming_from_scheduler # NEW 2.1.0 Required if you want to schedule stuff, comes from DataALM.

# Data Quality
quality: null # See more information below

# Tags
tags: null
```

|Key|UX label|Required|Description|
| --- | --- | --- | --- | 
| version|Version|Yes|Current version of the data contract.|
| uuid|Identifier|Yes| A unique identifier used to reduce the risk of dataset name collisions; initially the UUID will be created using a UUID generator tool ([example](https://www.uuidgenerator.net/)). However, we may want to develop a method that accepts a seed value using a combination of fields–such as name, kind and source–to create a repeatable value.|
|username|Username|Yes|User credentials for connecting to the dataset; how the credentials will be stored/passed is outside of the scope of the contract.|
|userConsumptionMode|Consumption mode|No|List of data modes for which the dataset may be used.  Expected sample values might be Analytical or Operational. <br/>Note: in the future, this will probably be replaced by ports.|
|type|Type|Yes|Identifies the types of objects in the dataset.  For BigQuery the expected value would be tables.
tenant|Tenant|No|Indicates the property the data is primarily associated with. Value is case insensitive. For PayPal, the expected sample values might be PayPal, Venmo, PPWC, etc.|
tags|Tags|No|a list of tags that may be assigned to the dataset, table or column; the `tags` keyword may appear at any level.
status|Status|Yes|Current status of the dataset.
sourceSystem|Source system|Yes|The system where the dataset resides.  Expected value is bigQuery
sourcePlatform|Source platform|Yes|The platform where the dataset resides. Expected value is googleCloudPlatform
server|Server|Yes|The server where the dataset resides.|
quantumName|Quantum name|Yes|The name of the data quantum or data product.
productSlackChannel|Support Slack channel|No|Slack channel of the team responsible for maintaining the dataset.
productFeedbackUrl|Feedback URL|No|The URL for submitting feedback to the team responsible for maintaining the dataset.|
productDl|E-mail distribution list|No|The email distribution list (DL) of the persons or team responsible for maintaining the dataset.
password|Password|Yes|User credentials for connecting to the dataset; how the credentials will be stored/passed is out of the scope of this contract.
kind|Kind|Yes|The kind of Rosewall dataset being cataloged; Expected values are `virtualDataset` or `managedDataset`.
driverVersion|Driver version|Yes|The version of the connection driver to be used to connect to the dataset.|
driver|Driver|Yes|The connection driver required to connect to the dataset.|
description.usage|Usage|No|intended usage of the dataset, table, or column (depending on the level); the key may appear at the dataset, table, or column level.|
description.purpose|Purpose|No|Purpose of the dataset, table or column (depending on the level); the key may appear at the dataset, table, or column level.|
description.limitations|Limitations|No|Limitations of the dataset, table or column (depending on the level); the key may appear at the dataset, table, or column level.|
description|N/A|No|Object.|
datasetProject|GCP project|Yes|GCP BigQuery dataset project name.|
datasetName|BigQuery dataset name|Yes|GCP BigQuery dataset name.|
|datasetDomain|Domain dataset|No|Name of the logical domain dataset the contract describes. This field is only required for output data contracts. Examples: `imdb_ds_aggregate`, `receiver_profile_out`,  `transaction_profile_out`.|
database|Database|Yes|The database where the dataset resides.|

## Dataset & schema
This section describes the dataset and the schema of the data contract. It is the support for data quality, which is detailed in the next section.

### Example

```YAML
dataset:
  - table: tbl
    physicalName: tbl_1 # NEW in v2.1.0, Optional, default value is table name + version separated by underscores, as table_1_2_0
    priorTableName: null # if needed
    description: Provides core payment metrics 
    tags: null
    dataGranularity: Aggregation on columns txn_ref_dt, pmt_txn_id
    columns:
      - column: txn_ref_dt
        isPrimary: false # NEW in v2.1.0, Optional, default value is false, indicates whether the column is primary key in the table.
        businessName: transaction reference date
        logicalType: date
        physicalType: date
        isNullable: false
        description: null
        partitionStatus: true
        clusterStatus: false
        criticalDataElementStatus: false
        tags: null
        classification: null
        encryptedColumnName: null
        transformSourceTables:
          - table_name_1
          - table_name_2
          - table_name_3
        transformLogic: sel t1.txn_dt as txn_ref_dt from table_name_1 as t1, table_name_2 as t2, table_name_3 as t3 where t1.txn_dt=date-3
        transformDescription: defines the logic in business terms; logic for dummies
        sampleValues:
          - 2022-10-03
          - 2020-01-28
      - column: rcvr_id
        isPrimary: true # NEW in v2.1.0, Optional, default value is false, indicates whether the column is primary key in the table.
        businessName: receiver id
        logicalType: string
        physicalType: varchar(18)
        isNullable: false
        description: A description for column rcvr_id.
        partitionStatus: false
        clusterStatus: true
        criticalDataElementStatus: false
        tags: null
        classification: null
        encryptedColumnName: null
      - column: rcvr_cntry_code
        isPrimary: false # NEW in v2.1.0, Optional, default value is false, indicates whether the column is primary key in the table.
        businessName: receiver country code
        logicalType: string
        physicalType: varchar(2)
        isNullable: false
        description: null
        partitionStatus: false
        clusterStatus: false
        criticalDataElementStatus: false
        tags: null
        classification: null
        authoritativeDefinitions:
          - url: https://collibra.com/asset/742b358f-71a5-4ab1-bda4-dcdba9418c25
            type: Business definition
          - url: https://github.com/myorg/myrepo
            type: Reference implementation
        encryptedColumnName: rcvr_cntry_code_encrypted
```

### Definitions

|Key|UX label|Required|Description|
| --- | --- | --- | --- | 
dataset||Yes|Array. A list of tables within the dataset to be cataloged
dataset.table||Yes|Name of the table being cataloged; the value should only contain the table name. Do not include the project or dataset name in the value.
dataset.physicalName||No|Physical name of the table, default value is table name + version separated by underscores, as `table_1_2_0`.|
dataset.priorTableName||Yes|Name of the previous version of the dataset.|
dataset.dataGranularity||No|Granular level of the data in the table. Example would be `pmt_txn_id`.|
dataset.columns||Yes|Array. A list of columns in the table.|
dataset.columns.column||Yes|the name of the column.|
dataset.columns.isPrimaryKey||No|Boolean value specifying whether the column is primary or not. Default is false.|
dataset.columns.businessName||Yes|the business name of the column.|
dataset.columns.logicalType||Yes|the logical column datatype.|
dataset.columns.physicalType||Yes|the physical column datatype.|
dataset.columns.isNullable||Yes|indicates if the column may contain Null values; possible values are true and false.|
dataset.columns.partitionStatus||Yes|indicates if the column is partitioned; possible values are true and false.|
dataset.columns.clusterStatus||Yes|indicates of the column is clustered; possible values are true and false.|
dataset.columns.classification||Yes|the PayPal data classification indicating the class of data in the column; expected values are 1, 2, 3, 4, or 5.|
|dataset.columns.authoritativeDefinitions||No|list of links to sources that provide more detail on column logic or values; examples would be URL to a GitHub repo, Collibra, on another tool.|
dataset.columns.encryptedColumnName||Yes|The column name within the table that contains the encrypted column value. For example, unencrypted column `email_address` might have an encryptedColumnName of `email_address_encrypt`.
dataset.columns.transformSourceTables||No|List of sources used in column transformation.|
dataset.columns.transformLogic||No|Logic used in the column transformation.|
dataset.columns.transformDescription||No|Describes the transform logic in very simple terms.|
dataset.columns.sampleValues||No|List of sample column values.|
dataset.columns.criticalDataElementStatus||No|True or false indicator; If element is considered a critical data element (CDE) then true else false.|
dataset.columns.tags||No|A list of tags that may be assigned to the dataset, table or column; the tags keyword may appear at any level.|

## Data quality 
This section describes data quality rules & parameters. They are tightly linked to the schema described in the previous section.

### Example of data quality at the dataset level

Note:
* This example relies on a data quality tool called Elevate. It should be easily transformed to any other tool. If you have questions, please raise an [issue](https://github.com/paypal/data-contract-template/issues).
* The data contract template has a provision for supporting multiple data quality tools.

```YAML
dataset:
  - table: tab1
# ...
    quality:
      - code: countCheck # Required, name of the rule
        templateName: CountCheck # NEW in v2.1.0 Required
        description: Ensure row count is within expected volume range       # Optional
        toolName: Elevate # Required
        toolRuleName: DQ.rw.tab1.CountCheck # NEW in v2.1.0 Optional (Available only to the users who can change in source code edition)
        dimension: completeness                                             # Optional
        type: reconciliation                                                # Optional NEW in v2.1.0 default value for column level check - dataQuality and for table level reconciliation
        severity: error                                                     # Optional NEW in v2.1.0, default value is error
        businessImpact: operational                                         # Optional NEW in v2.1.0
        scheduleCronExpression: 0 20 * * *                                  # Optional NEW in v2.1.0 default schedule - every day 10 a.m. PST
      - code: distinctCheck    
        description: enforce distinct values
        toolName: Elevate
        templateName: DistinctCheck
        dimension: completeness
        toolRuleName: DQ.rw.tab1.DistinctCheck
        columns:
          - txn_ref_dt
          - rcvr_id
        column: null
        type: reconciliation
        severity: error
        businessImpact: operational
        scheduleCronExpression: 0 20 * * *
        customProperties:                     
          - property: FIELD_NAME
            value: rcvr_id
          - property: FILTER_CONDITIONS
            value: 1>0
      - code: piiCheck
        description: null
        toolName: Elevate
        templateName: PIICheck
        toolRuleName: DQ.rw.tab1_2_0_0.piiCheck
        type: dataQuality
        severity: error
        businessImpact: operational
        scheduleCronExpression: 0 20 * * *
        customProperties:                     
          - property: FIELD_NAME
            value:
          - property: FILTER_CONDITIONS
            value:
```

### Example of data quality at the column level

```YAML
dataset:
  - table: tab1
      - column: rcvr_id
        isPrimary: true # NEW in v2.1.0, Optional, default value is false, indicates whether the column is primary key in the table.
        businessName: receiver id
# ...
        quality:
          - code: nullCheck
            templateName: NullCheck
            description: column should not contain null values
            toolName: Elevate
            toolRuleName: DQ.rw.tab1_2_0_0.rcvr_cntry_code.NullCheck
            dimension: completeness # dropdown 7 values
            type: dataQuality
            severity: error
            businessImpact: operational
            scheduleCronExpression: 0 20 * * *
            customProperties:
              - property: FIELD_NAME
                value:
              - property: COMPARE_TO
                value:
              - property: COMPARISON_TYPE
                value: Greater than
```

### Definitions

|Key|UX label|Required|Description|
| --- | --- | --- | --- | 
|quality||No|Quality tag with all the relevant information for rule setup and execution.|
|quality.code||No|The Rosewall data quality code(s) indicating which quality checks need to be performed at the dataset, table or column level; The quality keyword may appear at any level; Some quality checks require parameters such so the check can be completed (eg, list of fields used to identify a distinct row) therefore some quality checks may be followed by a single value or an array; See appendix for link to quality checks.
|quality.templateName||Yes|The template name which indicates what is the equivalent template from the tool. 
|quality.description||No|Describe the quality check to be completed.
|quality.toolName||Yes|Name of the tool used to complete the quality check; Most will be Elevate initially.|
|quality.toolRuleName||No|Name of the quality tool's rule created to complete the |quality check.|
|quality.dimension||No|The key performance indicator (KPI) or dimension for data quality.|
|quality.columns||No|List of columns to be used in the quality check
|quality.column||No|To be used in lieu of quality.columns when only a single column is required for the quality check.|
|quality.type||No|The type of quality check.|
|quality.severity||No|The severance of the quality rule.|
|quality.businessImpact||No|Consequences of the rule failure.|
|quality.scheduleCronExpression||No|Rule execution schedule details.|
|quality.customProperties||No|Additional properties required for rule execution. |

## Pricing
This section covers pricing when you bill your customer for using this data product. Pricing is experimental in v2.1.1 of the data contract.

### Example

```YAML
price:
  priceAmount: 9.95
  priceCurrency: USD
  priceUnit: megabyte
```

### Definitions

|Key|UX label|Required|Description|
| --- | --- | --- | --- | 
price||No|Object
price.priceAmount||No|Subscription price per unit of measure in `priceUnit`.|
price.priceCurrency||No|Currency of the subscription price in `price.priceAmount`.|
price.priceUnit||No|The unit of measure for calculating cost. Examples megabyte, gigabyte.|

## Stakeholders
This section lists stakeholders and the history of their relation with this data contract.

### Example
```YAML
stakeholders:
  - username: ceastwood
    role: Data Scientist
    dateIn: 2022-08-02
    dateOut: 2022-10-01
    replacedByUsername: mhopper
  - username: mhopper
    role: Data Scientist
    dateIn: 2022-10-01
    dateOut: null
    replacedByUsername: null
  - username: daustin
    role: Owner
    comment: Keeper of the grail
    dateIn: 2022-10-01
    dateOut: null
    replacedByUsername: null
```

### Definitions
The UX label is the label used in the UI and other user experiences. It is not limited to BlueRacket.

|Key|UX label|Required|Description|
| --- | --- | --- | --- |
stakeholders||No|Array
stakeholders.username||No|The stakeholder's username or email.|
stakeholders.role||No|The stakeholder's job role; Examples might be owner, data steward. There is no limit on the role.|
stakeholders.dateIn||No|The date when the user became a stakeholder.|
stakeholders.dateOut||No|The date when the user ceased to be a stakeholder|
stakeholders.replacedByUsername||No|The username of the user who replaced the stakeholder|

## Roles
This section lists the roles that a consumer may need to access the dataset depending on the type of access they require.

### Example

```YAML
roles:
  - role: microstrategy_user_opr
    access: read
    firstLevelApprovers: Reporting Manager
    secondLevelApprovers: 'mandolorian'
  - role: bq_queryman_user_opr
    access: read
    firstLevelApprovers: Reporting Manager
    secondLevelApprovers: na
  - role: risk_data_access_opr
    access: read
    firstLevelApprovers: Reporting Manager
    secondLevelApprovers: 'dathvador'
  - role: bq_unica_user_opr
    access: write
    firstLevelApprovers: Reporting Manager
    secondLevelApprovers: 'mickey'
```

### Definitions

|Key|UX label|Required|Description|
| --- | --- | --- | --- |
roles||Yes|Array. A list of roles that will provide user access to the dataset.|
roles.role||Yes|name of the IAM role that provides access to the dataset; the value will generally come directly from the "BQ dataset to IAM roles mapping" document.|
roles.access||Yes|the type of access provided by the IAM role; the value will generally come directly from the "BQ dataset to IAM roles mapping" document.|
roles.firstLevelApprovers||No|the name(s) of the first level approver(s) of the role; the value will generally come directly from the "BQ dataset to IAM roles mapping" document.|
roles.secondLevelApprovers||No|the name(s) of the second level approver(s) of the role; the value will generally come directly from the "BQ dataset to IAM roles mapping" document.|


## Service-level agreement
This section describes the service-level agreements (SLA). SLA have been extended in version v2.1.0.

* Use the `Table.Column` to indicate the number to do the checks on, as in `SELECT txn_ref_dt FROM tab1`.
* Separate multiple table.columns by a comma, as in `table1.col1`, `table2.col1`, `table1.col2`.
* If there is only one table in the contract, the table name is not required.

### Example

```YAML
slaDefaultColumn: tab1.txn_ref_dt # Optional, default value is partitionColumn.
slaProperties:
  - property: latency # Property, see list of values in DP QoS
    value: 4
    unit: d # d, day, days for days; y, yr, years for years
    column: tab1.txn_ref_dt # This would not be needed as it is the same table.column as the default one
  - property: generalAvailability
    value: 2022-05-12T09:30:10-08:00
  - property: endOfSupport
    value: 2032-05-12T09:30:10-08:00
  - property: endOfLife
    value: 2042-05-12T09:30:10-08:00
  - property: retention
    value: 3
    unit: y
    column: tab1.txn_ref_dt
  - property: frequency
    value: 1
    valueExt: 1
    unit: d
    column: tab1.txn_ref_dt
  - property: timeOfAvailability
    value: 09:00-08:00
    column: tab1.txn_ref_dt
    driver: regulatory # Describes the importance of the SLA: [regulatory|analytics|operational|...]
  - property: timeOfAvailability
    value: 08:00-08:00
    column: tab1.txn_ref_dt
    driver: analytics
```

### Definitions

|Key|UX label|Required|Description|
| --- | --- | --- | --- |
|slaDefaultColumn|Default SLA column(s)|No|Columns (using the Table.Column notation) to do the checks on. By default, it is the partition column.|
|slaProperties|SLA|No|A list of key/value pairs for SLA specific properties. There is no limit on the type of properties (more details to come).|
|slaProperties.property|Property|Yes|Specific property in SLA, check the periodic table. May requires units (more details to come).|
|slaProperties.value|Value|Yes|Agreement value. The label will change based on the property itself.|
|slaProperties.valueExt|Extended value|No - unless needed by property|Extended agreement value. The label will change based on the property itself.|
|slaProperties.unit|Unit|No - unless needed by property|**d**, day, days for days; **y**, yr, years for years, etc. Units use the ISO standard.|
|slaProperties.column|Column(s)|No|Column(s) to check on. Multiple columns should be extremely rare and, if so, separated by commas.|
|slaProperties.driver|Driver|No|Describes the importance of the SLA from the list of: regulatory, analytics, operational.|

## Other properties
This section covers other properties you may find in a data contract.

### Example

```YAML
customProperties:
  - property: refRulesetName
    value: gcsc.ruleset.name
  - property: somePropertyName
    value: property.value
  - property: dataprocClusterName # Used for specific applications like Elevate
    value: [cluster name]

systemInstance: someinstance.paypal.com
contractCreatedTs: 2022-11-15 02:59:43
```


### Definitions

|Key|UX label|Required|Description|
| --- | --- | --- | --- |
customProperties||No|A list of key/value pairs for custom properties. Initially created to support the REF ruleset property.
customProperties.property||No|The name of the key. Names should be in camel case–the same as if they were permanent properties in the contract.
customProperties.value||No|The value of the key.
systemInstance||No|System Instance name where the dataset resides.
contractCreatedTs||No|Timestamp in PST of when the data contract was created.
