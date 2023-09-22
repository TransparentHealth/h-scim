# H-SCIM (DRAFT) is a Healthcare profile of SCIM for "Provider Directories"

This is a DRAFT and is published here for public comment.

H-SCIM is a JSON document format to represent organizations in healthcare. Its core design goal is to be easy to search.  Its total ecosystem is the following:

* Organizations
* Individual clinicians
* Office staff
* Billing organizations
* Other organizations (for example, transportation to a medical appointment)


SCIM stands for the "System for Cross-domain Identity Management". The SCIM protocol is published by IETF and can be found here: https://datatracker.ietf.org/doc/html/rfc7644  

SCIM uses a JSON as its data model. H-SCIM is a profile that adds a few additional fields to facilitate US-based healthcare commerce. It doesn't change the SCIM protocol or fields. It only add some data elements. Examples of additional information stored in H-SCIM include:

* Tax IDs/ Employer Identification Number (Optional)
* National Provider Identifiers (NPI) for Organizations, Subparts, Facilities and Individuals
* Domains (e.g. work email domains used for login at the company by its employees/agents). (Optional)


The core set of JSON Documents in H-SCIM consist of Organizations of the following `organizationType` type:

* Health Care Providers -`provider-org`
* Insurance or Billing Companies -`payer-org`
* Other Organizations (for example, non-ambulatory transportation to healthcare appointment) - `other-org`
* National Provider Identifiers (NPI) Tpye 2 for Organizations, Subparts, Facilities - `npi-2`


H-SCIM is an easy to understand data model that can power provider directory APIs that are easy to query and integrate into other systems ans services.


Organization Groups in H-SCIM
-----------------------------

In H-SCIM, an Organization is represented as an SCIM Group. Organization Groups can have subgroups under the `groups` field.  This is true when an organization has multiple facilities or subparts.


Fields already defined in the base SCIM specification and remain unchanged are not listed here for brevity. The following table outlines specific additions for **SCIM Groups**.

| Field Name         | Type      | Required | Notes                           |
|--------------------|-----------|----------|---------------------------------|
|`externalId`          | string    | Yes      | externalID is a unique "tag" for an Organization. SCIM does not require this field, but it is required in for Organizations in SCIM|
| `organizationType`   | string    | Yes      | This field identified the organization type. It must be one of the following values: `provider-org` `payer-org`, `other-org`, `sole-proprietor`, `npi_2` |
| `domain`             | string    | No       | A work domain used for staff login |
| `tin`                | string    | No      | A Tax Identification number such as a EIN |
| `tinType`            | string    | No      | A field to identify the type of tax ID. It must be one of the following values: `EIN`, `ITIN`, `SSN`|
| `npi2s`              | array of strings   | No      | An list of NPI Type 2 codes for the Organization. These represent facilities and/or subparts of the organization. NPIs are issued by HHS/CMS.  |
| `npi1s `             | array of strings   | No      | An list of Individual health providers affiliated with the Organization.   NPI Type 1 codes are for individual health providers such as doctors.  NPIs are issued by HHS/CMS.  |
| `npi`            | string    | No      | A "Type 2" National Provider Identifier for the facility/subpart. |
| `groups`             | array of SCIM Group objects | No      | Details of facilities and or subparts of the organization. There is always a 1:1 relationship with the `npi2s` list.  |
| `members`            | array of SCIM User objects | No      | Details of the individuals affiliated with the organization.  This includes ALL STAFF such as office/practice administrators.  Each individual health provider with an NPI will be contained herein.  |


Users in H-SCIM
---------------

In H-SCIM, a person is represented as an SCIM User. H-SCIM defines additional fields to facilitate healthcare-related commerce.

The following table outlines specific additions for **SCIM Users**.  Fields already defined in SCIM that remain unchanged are omitted for brevity.


| Field Name         | Type      | Required | Notes                           |
|--------------------|-----------|----------|---------------------------------|
| `userType`         | string    | Yes      | This field identifies the user type. It must be one of the following values: `NPI-1`, `Employee` |
| `ial`              | string    | No       | A value for the users' Identity Assurance Level (IAL) according to NIST SP 800-63-3. see https://pages.nist.gov/800-63-3/sp800-63-3.html |
| `npi`              | string    | No       | The user's individual "Type 1" National Provider Identifier number (if they have one). This value SHALL be present when `userType`=`NPI-1`. |



Example
-------


In the following hypothetical example, the fictional Organization "ACME Med" is a health provider organization that is comprised of a surgery center and an imaging center.  There are 5 clinicians with NPIs associated with the Organization. `Alice Admin` is the ACME's practice administrator.




          {
          "schemas": [
                "urn:ietf:params:scim:schemas:core:2.0:Group"
          ],
          "id": "some-id",
          "externalId": "acme-med",
          "displayName": "ACME MED",
          "organizationType": "provider-org",
          "domain": "example.com",
          "tin": "345678909",
          "tin_type": "EIN",
          "npi2s": [
                "1355498457",
                "1012444340",
                "1547538558"
          ],
          "npi1s": [
                "1133167036",
                "1597743884",
                "1385669978",
                "1365431142"
          ],
          "groups": [
                {
                      "schemas": [
                            "urn:ietf:params:scim:schemas:core:2.0:Group"
                      ],
                      "id": "4c22289e-d666-49d1-8f66-a3744329789e",
                      "externalId": "acme-med",
                      "displayName": "ACME SURGERY CENTER, L.L.C",
                      "tin": "345678909",
                      "tin_type": "EIN",
                      "npi": "1355498457",
                      "organizationType": "NPI-2",
                      "meta": {
                            "resourceType": "Group",
                            "created": "2023-08-23 18:09:12.335009+00:00",
                            "lastModified": "2023-08-23 18:09:56.413945+00:00",
                            "location": "https://api.scim.example.com/scim/v2/Group/4c22289e-d666-49d1-8f66-a3744329789e"
                      }
                },
                {
                      "schemas": [
                            "urn:ietf:params:scim:schemas:core:2.0:Group"
                      ],
                      "id": "0a679400-349a-427f-ada2-0bc74dfb57f9",
                      "externalId": "acme-med",
                      "displayName": "ACME SURGERY CENTER",
                      "tin": "345678909",
                      "tin_type": "EIN",
                      "npi": "1012444340",
                      "organizationType": "NPI-2",
                      "meta": {
                            "resourceType": "Group",
                            "created": "2023-08-23 18:09:12.433365+00:00",
                            "lastModified": "2023-08-23 18:09:56.469752+00:00",
                            "location": "https://api.scim.example.com/scim/v2/Group/0a679400-349a-427f-ada2-0bc74dfb57f9"
                      }
                },
                {
                      "schemas": [
                            "urn:ietf:params:scim:schemas:core:2.0:Group"
                      ],
                      "id": "27758dab-fa4d-4094-af08-6a7c05257286",
                      "externalId": "acme-med",
                      "displayName": "ACME IMAGING",
                      "tin": "345678909",
                      "tin_type": "EIN",
                      "npi": "1547538558",
                      "organizationType": "NPI-2",
                      "meta": {
                            "resourceType": "Group",
                            "created": "2023-08-23 18:09:12.551333+00:00",
                            "lastModified": "2023-08-23 18:09:56.520691+00:00",
                            "location": "https://api.scim.example.com/scim/v2/Group/27758dab-fa4d-4094-af08-6a7c05257286"
                      }
                }
          ],
          "members": [
                 {
                      "schemas": [
                            "urn:ietf:params:scim:schemas:core:2.0:User",
                            "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"
                      ],
                      "id": "2c47510b-a7cb-4bc0-a8ba-1021bfefcaa7",
                      "externalId": "acme-med",
                      "userName": "1c47510b-a7cb-4bc0-a8ba-1021bfefcaa7",
                      "userType": "Employee",
                      "ial": "2",
                      "name": {
                            "formatted": "Alice Admin",
                            "familyName": "ADMIN",
                            "givenName": "ALICE"
                      },
                      "emails": [ {"value": "alice@example.com",
                                   "type": "work"}

        ],
                      "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User": {
                            "organization": "ACME Med",
                            "department": ""
                      },
                      "meta": {
                            "resourceType": "User",
                            "created": "2023-08-28 13:59:02.914541+00:00",
                            "modified": "2023-08-23 18:09:56.550943+00:00",
                            "location": "https://api.scim.example.com/scim/v2/Users/1c47510b-a7cb-4bc0-a8ba-1021bfefcaa7"
                      }
                },
                {
                      "schemas": [
                            "urn:ietf:params:scim:schemas:core:2.0:User",
                            "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"
                      ],
                      "id": "1c47510b-a7cb-4bc0-a8ba-1021bfefcaa7",
                      "externalId": "acme-med",
                      "userName": "1c47510b-a7cb-4bc0-a8ba-1021bfefcaa7",
                      "userType": "NPI-1",
                      "ial": "",
                      "name": {
                            "formatted": "Mary Collins, M.D.",
                            "familyName": "COLINS",
                            "givenName": "MARY",
                            "honorificPrefix": "Dr.",
                            "honorificSuffix": "--",
                            "credential": "M.D."
                      },
                      "npi": "1133167036",
                      "emails": [ ],
                      "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User": {
                            "organization": "ACME Med",
                            "department": ""
                      },
                      "meta": {
                            "resourceType": "User",
                            "created": "2023-08-28 13:59:02.914541+00:00",
                            "modified": "2023-08-23 18:09:56.550943+00:00",
                            "location": "https://api.scim.example.com/scim/v2/Users/1c47510b-a7cb-4bc0-a8ba-1021bfefcaa7"
                      }
                },
                {
                      "schemas": [
                            "urn:ietf:params:scim:schemas:core:2.0:User",
                            "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"
                      ],
                      "id": "a3774dae-8f0c-444d-af20-6a8a3541e7db",
                      "externalId": "acme-med",
                      "userName": "a3774dae-8f0c-444d-af20-6a8a3541e7db",
                      "userType": "NPI-1",
                      "ial": "",
                      "name": {
                            "formatted": "Andy Mann, M.D.",
                            "familyName": "MANN",
                            "givenName": "ANDY",
                            "honorificPrefix": "--",
                            "honorificSuffix": "--",
                            "credential": "M.D."
                      },
                      "npi": "1597743884",
                      "emails": [ ],
                      "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User": {
                            "organization": "ACME Med",
                            "department": ""
                      },
                      "meta": {
                            "resourceType": "User",
                            "created": "2023-08-28 13:59:02.955985+00:00",
                            "modified": "2023-08-23 18:09:56.595868+00:00",
                            "location": "https://api.scim.example.com/scim/v2/Users/a3774dae-8f0c-444d-af20-6a8a3541e7db"
                      }
                },

                {
                      "schemas": [
                            "urn:ietf:params:scim:schemas:core:2.0:User",
                            "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"
                      ],
                      "id": "21149e45-cd3d-4832-8df1-74afdf2b1b63",
                      "externalId": "acme-med",
                      "userName": "21149e45-cd3d-4832-8df1-74afdf2b1b63",
                      "userType": "NPI-1",
                      "ial": "",
                      "name": {
                            "formatted": "Amy Shark, MD",
                            "familyName": "SHARK",
                            "givenName": "AMY",
                            "honorificPrefix": "Dr.",
                            "honorificSuffix": "--",
                            "credential": "MD"
                      },
                      "npi": "1365431142",
                      "emails": [ ],
                      "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User": {
                            "organization": "AMCE Medical",
                            "department": ""
                      },
                      "meta": {
                            "resourceType": "User",
                            "created": "2023-08-23 18:09:15.456119+00:00",
                            "modified": "2023-08-23 18:09:57.448793+00:00",
                            "location": "https://api.scim.example.com/scim/v2/Users/21149e45-cd3d-4832-8df1-74afdf2b1b63"
                      }
                }
          ],
          "meta": {
                "resourceType": "Group",
                "created": "2023-08-23 18:09:12.268890+00:00",
                "lastModified": "2023-08-23 18:09:56.371075+00:00",
                "location": "https://api.scim.example.com/scim/v2/Group/b9335dd5-6770-4f42-81d1-c367492514a1"
          }
