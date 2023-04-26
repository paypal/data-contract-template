This document tracks the history and evolution of the data contract template. It precedes 

# v2.1.1 - 2023-04-26 - APPROVED

Open source version.

# v2.1.0 - 2023-03-23 - APPROVED

## Data Quality
The data contract adds elements specifically for interfacing with the Data Quality tooling. 

Additions:
* quality (table level & column level check):
* templateName
* dimension
* type
* severity
* businessImpact
* scheduleCronExpression 
* customProperties
* columns
* isPrimaryKey

## Physical names
The data contract is a logical construct; we add more specific links to the physical world.

## Service-level agreement
The service-level agreements not previously used are more detailed to follow the DP QoS pattern. See SLA.

## Other
Removed the weight for system ratings from the data contract. Their default values remain.

# v2.0.0 - SUPERSEED BY V2.1.0

## Guidelines & Evolution
* [Type case](https://google.github.io/styleguide/jsoncstyleguide.xml?showone=Property_Name_Format#Property_Name_Format)
* Support for SemVer versioning.
* Tags can have values.

## Additions
* Version of contract definition: v2.0.0. A breaking change with v1.
* Description:
  * Purpose (text field).
  * Limitations (text field).
  * Usage (text field).
* Domain.
* Dictionary section:
  * Identification of masked column (encryptedColumnName property), example: the email_decrypted column would be masked by email_encrypted.
  * Flag for critical data element.
  * Added keys for transformation data (sources, logic, description).
  * Sample values.
  * Ability to specify links to authoritative sources at the column level (authoritativeDefinitions).
  * Business name.
* List of stakeholders:
  * Username (user account).
  * Role.
  * Date in.
  * Date out.
  * Replaced by.
* Service levels: agreements & objective [orginal inspiration](https://medium.com/@jgperrin/meet-cactar-the-mongolian-warlord-of-data-quality-d7bdbd6a5398).
* Price / cost.
* Name changes to match PPaaS type case.
* Product data:
  * productDl.
  * productSlackChannel.
  * productFeedbackUrl.
* Renamed `tables` key to `dataset`.
* Removed `owner` key.  Owner is now a stakeholder role.
* Additional quality keys:
  * description.
  * toolName.
  * toolRuleName.
* Custom Properties.
* Product dates:
  * generalAvailabilityDate.
  * endOfSupportDate.
  * endOfLifeDate.

# v1 - DEPRECATED
* Description of the data quantum/data artifact.
* Roles.
* Schema:
  * Tables, columns.
  * Data quality.
* System rating weightage.
* Ratings:
  * System, user, etc.