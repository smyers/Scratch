<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

# ePRO Service Contract

## Description
ePro - Medidata Patient Cloud Service

## Version ##
1.0.0

## Owners ##

* [Steve Myers](mailto:smyers@mdsol.com)
* [Isaac Wong](mailto:iwong@mdsol.com)

## DBB (Data Bound to Behavior)
Retrieves form data and edit check information from RWS, and accepts subject's form submissions
which are then transmitted to RWS.

## SLT
TBD

## Security ##
ePro serves responses over [SSL](http://en.wikipedia.org/wiki/Secure_Sockets_Layer)
and performs user [Authentication](#authentication).

## Application Programming Interface (API)

* This service is intended to be consumed in hypermedia style. After the initial GET to the entry point at `/mobile_crf`,
a consuming app can use the links provided to complete their workflow, without having to hardcode any further URLs
or URL templates. However, we will maintain the `/v1` URLs to support clients who prefer different workflows.
* This service returns hypermedia-enriched responses with media type `application/vnd.hale+json`
(see [Hale](https://github.com/mdsol/hale)).

### Authentication
Each request to the service should include either a username and password, or an authentication token.
If the request contains a username and password, it will be verified with iMedidata and a new authentication
token will be returned in the response.
Subsequent requests should include that authentication token until the token expires.

The username and password are sent as an `Authorization` header according to
[Basic Access Authentication](http://en.wikipedia.org/wiki/Basic_access_authentication).
For token requests, the `Authorization` header is populated with the token, such as

 `Authorization: Token token="h480djs93hd8"`


### ETags
For GET routes that support ETags, the response will include an ETag value (in the HTTP `ETag` field) which
is specific to the current version of a resource.
On subsequent requests, if the client includes the ETag (in a `If-None-Match` field), then the resource will
only be returned if there is a later version, otherwise `304 Not Modified` will be returned.

### Compression
Routes will honor an `Accept-Encoding` type of `gzip` in requests.


### Resource: Mobile CRF ###

The Mobile CRF contains all Mobile Forms for a given user. As the home resource, it is the entry point to
discover links to other resources.


#### Route ####

**GET: `/mobile_crf`**

*Description:*

View all Mobile Forms for a given user.
ETags are supported such that a change on an individual Mobile Form in the CRF will result in a new ETag (modified).

*Query Parameters:*

* user_uuid: The iMedidata user UUID.

*Response Body:*

Returns a collection of link relations that can be used to retrieve the individual Mobile Form resources in subsequent
requests.

* `items`: The list of individual Mobile Form URIs, corresponding to the given user.

```json
{
   "_links": {
      "self": { "href": "https://epro.example.com/mobile_crf?user_uuid=20649fc0-1b71-4267-a921-42520b14815d" },
      "profile": { "href": "https://epro.example/alps/MobileCRF" },
      "help": { "href": "https://github.com/mdsol/epro-app" },
      "items": [
          {
              "href": "https://epro.example.com/v1/mobile_forms/4efbcc84-658a-422e-9488-08b295501055",
              "type": "https://epro.example.com/alps/MobileForms#form"
          },
          {
              "href": "https://epro.example.com/v1/mobile_forms/643c94d8-8f33-446e-aeec-d0fc98952b3b",
              "type": "https://epro.example.com/alps/MobileForms#form"
          },
          {
              "href": "https://epro.example.com/v1/mobile_forms/3cc83609-ad1a-4add-8371-2794f0d262e2",
              "type": "https://epro.example.com/alps/MobleForms#form"
          }
      ]
    }
}
```

*Response Codes:*

* 200 Success (also for an empty list)
* 401 Authentication failed
* 403 Authentication token expired
* 304 Mobile CRF not modified (ETag match)


### Resource: Mobile Form
A Mobile Form has the form data as well as any edit checks associated with fields on the form.

#### Attributes ####
* `uuid`: (read-only - managed by the service) Unique identifier for the Mobile Form (length between 24 and 36 inclusive).
* `dataset`: JSON structure containing all form data. Edit checks are nested under the field which triggers it.


#### Routes ####
**GET: `/v1/mobile_forms/:uuid`**

*Description:*

Fetches a Mobile Form given its uuid. ETags are supported.

*Response Body:*

JSON:
```json
{
    "uuid": "b309d909-94ca-4534-95f6-d6db0319b3fa",
    "dataset": {
        "SubjectName": "ba93",
        "SiteNumber": "Site",
        "DataPage": [
          {
            "DataPageID": "18073",
            "FormOid": "PCFORM",
            "FormName": "PCFORM",
            "DatapageRepeat": "0",
            "FolderOID": "",
            "InstanceRepeat": "",
            "FolderOrdinal": "",
            "InstanceGuid": "",
            "DataPageGuid": "507831cd-36b4-47d1-8c58-c92761ef4f6e",
            "Record": [
              {
                "RaveRecordID": "33010",
                "RecordPosition": "1",
                "RecordName": "PCFORM (1)",
                "RecordRepeat": "1",
                "Field": [
                  {
                    "FieldOID": "TEXTFIELD",
                    "Data": "",
                    "RaveID": "",
                    "FieldNumber": "",
                    "PreText": "Text Field",
                    "PostText": "",
                    "HelpText": "",
                    "ControlType": "Text",
                    "FieldOrdinal": "1",
                    "IsLogField": "1",
                    "EproFormat": "",
                    "IsVisible": 1,
                    "IsTouched": "",
                    "IsNonConformant": "",
                    "VariableFormat": "$30"
                  },
                  {
                    "FieldOID": "MUN_FIELD",
                    "Data": "",
                    "RaveID": "",
                    "FieldNumber": "",
                    "PreText": "Numeric Field",
                    "PostText": "",
                    "HelpText": "",
                    "ControlType": "Text",
                    "FieldOrdinal": "2",
                    "IsLogField": "1",
                    "EproFormat": "",
                    "IsVisible": 1,
                    "IsNonConformant": "",
                    "VariableFormat": "5"
                  },
                  {
                    "FieldOID": "WBF",
                    "Data": "",
                    "RaveID": "",
                    "FieldNumber": "",
                    "PreText": "Wong Baker Field",
                    "PostText": "",
                    "HelpText": "",
                    "ControlType": "Text",
                    "FieldOrdinal": "3",
                    "IsLogField": "1",
                    "EproFormat": {
                      "control_name": "WongBaker",
                      "control_version": "1.0"
                    },
                    "IsVisible": 1,
                    "IsNonConformant": "",
                    "VariableFormat": "4"
                  },
                  {
                    "FieldOID": "BFIELD",
                    "Data": "",
                    "RaveID": "",
                    "FieldNumber": "",
                    "PreText": "Bristol Field",
                    "PostText": "",
                    "HelpText": "",
                    "ControlType": "Text",
                    "FieldOrdinal": "4",
                    "IsLogField": "1",
                    "EproFormat": {
                      "control_name": "Bristol",
                      "control_version": "1.0"
                    },
                    "IsVisible": 1,
                    "IsNonConformant": "",
                    "VariableFormat": "4"
                  },
                  {
                    "FieldOID": "GENDER",
                    "Data": "",
                    "RaveID": "",
                    "FieldNumber": "",
                    "PreText": "Gender",
                    "PostText": "",
                    "HelpText": "",
                    "ControlType": "RadioButton",
                    "FieldOrdinal": "5",
                    "IsLogField": "1",
                    "EproFormat": "",
                    "IsVisible": 1,
                    "IsNonConformant": "",
                    "VariableFormat": "$5",
                    "DataDictionaryOID": "MF",
                    "DataDictionaryID": "2785",
                    "DataDictionary": [
                      {
                        "DictionaryCodedData": "m",
                        "DictionaryValue": "m"
                      },
                      {
                        "DictionaryCodedData": "f",
                        "DictionaryValue": "f"
                      }
                    ],
                    "EditCheck": {
                      "CheckID": "9841",
                      "CheckName": "PCFORM_EC",
                      "CheckOID": "PCFORM_EC",
                      "CheckUpdated": "12/10/2014 8:28:06 PM",
                      "Actions": [
                        {
                          "ActionID": "12791",
                          "CheckAction": "SetDataPointVisible",
                          "ActionFieldOID": "PREG11",
                          "ActionOptions": "1"
                        }
                      ],
                      "Steps": [
                        {
                          "CheckStepID": "36164",
                          "StepOrdinal": "1",
                          "StepFieldOID": "GENDER",
                          "CompareValue": "",
                          "CheckFunction": ""
                        },
                        {
                          "CheckStepID": "36165",
                          "StepOrdinal": "3",
                          "StepFieldOID": "",
                          "CompareValue": "",
                          "CheckFunction": "IsEqualTo"
                        },
                        {
                          "CheckStepID": "36166",
                          "StepOrdinal": "2",
                          "StepFieldOID": "",
                          "CompareValue": "f",
                          "CheckFunction": ""
                        }
                      ]
                    }
                  }
                ]
              },
              {
                "FieldOID": "PREG11",
                "Data": "",
                "RaveID": "",
                "FieldNumber": "",
                "PreText": "Pregnant?",
                "PostText": "",
                "HelpText": "",
                "ControlType": "RadioButton",
                "FieldOrdinal": "6",
                "IsLogField": "1",
                "EproFormat": "",
                "IsVisible": 1,
                "IsNonConformant": "",
                "VariableFormat": "$5",
                "DataDictionaryOID": "NOYES",
                "DataDictionaryID": "2787",
                "DataDictionary": [
                  {
                    "DictionaryCodedData": "0",
                    "DictionaryValue": "No"
                  },
                  {
                    "DictionaryCodedData": "1",
                    "DictionaryValue": "Yes"
                  }
                ]
              },
              {
                "FieldOID": "DOB",
                "Data": "",
                "RaveID": "",
                "FieldNumber": "",
                "PreText": "Date Field",
                "PostText": "",
                "HelpText": "",
                "ControlType": "DateTime",
                "FieldOrdinal": "7",
                "IsLogField": "1",
                "EproFormat": "",
                "IsVisible": 1,
                "IsNonConformant": "",
                "VariableFormat": "dd MMM yyyy"
              },
              {
                "FieldOID": "ACK_FIELD",
                "Data": "",
                "RaveID": "",
                "FieldNumber": "",
                "PreText": "Acknowledge Field",
                "PostText": "",
                "HelpText": "",
                "ControlType": "Text",
                "FieldOrdinal": "8",
                "IsLogField": "1",
                "EproFormat": {
                  "control_name": "Signature",
                  "control_version": "1.0"
                },
                "IsVisible": 1,
                "IsNonConformant": "",
                "VariableFormat": "$30"
              },
              {
                "FieldOID": "SUM1A",
                "Data": "",
                "RaveID": "",
                "FieldNumber": "",
                "PreText": "Field1A",
                "PostText": "",
                "HelpText": "If sum of Field1A and Field1B is equal to 10, then show field 1C",
                "ControlType": "Text",
                "FieldOrdinal": "9",
                "IsLogField": "1",
                "EproFormat": "",
                "IsVisible": 1,
                "IsNonConformant": "",
                "VariableFormat": "5"
              },
              {
                "FieldOID": "SUM1B",
                "Data": "",
                "RaveID": "",
                "FieldNumber": "",
                "PreText": "Field1B",
                "PostText": "",
                "HelpText": "If sum of Field1A and Field1B is equal to 10, then show field 1C",
                "ControlType": "Text",
                "FieldOrdinal": "10",
                "IsLogField": "1",
                "EproFormat": "",
                "IsVisible": 1,
                "IsNonConformant": "",
                "VariableFormat": "5",
                "EditCheck": {
                  "CheckID": "9842",
                  "CheckName": "PCFORM_EC2",
                  "CheckOID": "PCFORM_EC2",
                  "CheckUpdated": "12/10/2014 8:28:06 PM",
                  "Actions": [
                    {
                      "ActionID": "12792",
                      "CheckAction": "SetDataPointVisible",
                      "ActionFieldOID": "SUM1C",
                      "ActionOptions": "1"
                    }
                  ],
                  "Steps": [
                    {
                      "CheckStepID": "36167",
                      "StepOrdinal": "1",
                      "StepFieldOID": "SUM1A",
                      "CompareValue": "",
                      "CheckFunction": ""
                    },
                    {
                      "CheckStepID": "36168",
                      "StepOrdinal": "2",
                      "StepFieldOID": "SUM1B",
                      "CompareValue": "",
                      "CheckFunction": ""
                    },
                    {
                      "CheckStepID": "36169",
                      "StepOrdinal": "3",
                      "StepFieldOID": "",
                      "CompareValue": "",
                      "CheckFunction": "Add"
                    },
                    {
                      "CheckStepID": "36170",
                      "StepOrdinal": "4",
                      "StepFieldOID": "",
                      "CompareValue": "10",
                      "CheckFunction": ""
                    },
                    {
                      "CheckStepID": "36171",
                      "StepOrdinal": "5",
                      "StepFieldOID": "",
                      "CompareValue": "",
                      "CheckFunction": "IsEqualTo"
                    }
                  ]
                }
              },
              {
                "FieldOID": "SUM1C",
                "Data": "",
                "RaveID": "",
                "FieldNumber": "",
                "PreText": "Field1C",
                "PostText": "",
                "HelpText": "Now the field is visible. Enter any value",
                "ControlType": "Text",
                "FieldOrdinal": "11",
                "IsLogField": "1",
                "EproFormat": "",
                "IsVisible": 1,
                "IsNonConformant": "",
                "VariableFormat": "5"
              },
              {
                "FieldOID": "DATE2FIELD",
                "Data": "",
                "RaveID": "",
                "FieldNumber": "",
                "PreText": "date2",
                "PostText": "",
                "HelpText": "",
                "ControlType": "DateTime",
                "FieldOrdinal": "12",
                "IsLogField": "1",
                "EproFormat": "",
                "IsVisible": 1,
                "IsNonConformant": "",
                "VariableFormat": "dd MMM yyyy hh:nn:ss:rr"
              },
              {
                "FieldOID": "TIMEST",
                "Data": "",
                "RaveID": "",
                "FieldNumber": "",
                "PreText": "timestamp",
                "PostText": "",
                "HelpText": "",
                "ControlType": "DateTime",
                "FieldOrdinal": "13",
                "IsLogField": "1",
                "EproFormat": {
                  "control_name": "Timestamp",
                  "control_version": "1.0"
                },
                "IsVisible": 1,
                "IsNonConformant": "",
                "VariableFormat": "dd MMM yyyy hh:nn:ss rr"
              }
            ]
          }
        ]
    },
    "_links": {
        "self": { "href": "https://epro.example.com/v1/mobile_forms/b309d909-94ca-4534-95f6-d6db0319b3fa" },
        "profile": { "href": "https://epro.example/alps/MobleForm" },
        "type": { "href": "https://epro.example.com/alps/MobileForms#form" },
        "help": { "href": "https://github.com/mdsol/epro-app" }
    }
}
```

*Response Codes:*

* 200 Successfully return JSON representation of Mobile Form.
* 404 The Mobile Form cannot be found
* 401 Authentication failed
* 403 Authentication token expired
* 304 Mobile Form not modified (ETag match)


### Resource: Mobile Data Page (REVIEW: confirm name)
A Mobile Data Page represents the submission of clinical data for a Mobile Form.

#### Attributes ####
* `mobile_form_uuid`: The associated Mobile Form for which data is being submitted.
* `data_page`: Encrypted clinical data submission.

#### Routes ####

**POST: `/v1/mobile_forms/:mobile_form_uuid/mobile_data_page/`**

*Description:*

Submits the clinical data page entry for a subject. The data is encrypted.

*Request Body:*

```json
{
    "data_page": "517E7C5A11E079146458C194CA5BB5B84D1FDEF878736B1185082E510E438F"
}
```

Example of `data_page` field before encryption:
```json
{
  "form_data": {
    "subject_name": "ba93",
    "log_line": 1,
    "study_uuid": "d44d5de5-35f2-4202-aa38-7d9e7aee3445",
    "signature_date_time_entered": "2014-12-10T17:03:24",
    "folder_oid": "SUBJECT",
    "form_oid": "PCFORM",
    "field_data": [
      {
        "data_value": "Response for text field!",
        "item_oid": "TEXTFIELD",
        "date_time_entered": "2014-12-10T17:02:39"
      },
      {
        "data_value": "1234",
        "item_oid": "MUN_FIELD",
        "date_time_entered": "2014-12-10T17:02:42"
      },
      {
        "data_value": "8",
        "item_oid": "WBF",
        "date_time_entered": "2014-12-10T17:02:45"
      },
      {
        "data_value": "5",
        "item_oid": "BFIELD",
        "date_time_entered": "2014-12-10T17:02:47"
      },
      {
        "data_value": "f",
        "item_oid": "GENDER",
        "date_time_entered": "2014-12-10T17:02:50"
      },
      {
        "data_value": "1",
        "item_oid": "PREG11",
        "date_time_entered": "2014-12-10T17:02:52"
      },
      {
        "data_value": "10 Dec 2014",
        "item_oid": "DOB",
        "date_time_entered": "2014-12-10T17:02:59"
      },
      {
        "data_value": "1",
        "item_oid": "SUM1A",
        "date_time_entered": "2014-12-10T17:03:04"
      },
      {
        "data_value": "9",
        "item_oid": "SUM1B",
        "date_time_entered": "2014-12-10T17:03:07"
      },
      {
        "data_value": "5555",
        "item_oid": "SUM1C",
        "date_time_entered": "2014-12-10T17:03:10"
      },
      {
        "data_value": "10 Dec 2014 06:03:10:PM",
        "item_oid": "DATE2FIELD",
        "date_time_entered": "2014-12-10T17:03:15"
      },
      {
        "data_value": "10 Dec 2014 05:03:24 PM",
        "item_oid": "TIMEST",
        "date_time_entered": "2014-12-10T17:03:24"
      }
    ],
    "site_oid": "Site",
    "version": "1.0",
    "device_id": "CEEBA156-C012-4AD2-957C-9995829BC3A7",
    "signature_oid": "ACK_FIELD",
    "rave_url": "https://epro-validation.mdsol.com",
    "study_name": "SamplePCStudy",
    "record_oid": "PCFORM_LOG_LINE",
    "subject_uuid": "95147ec5-e5c0-41f0-8146-880393d7b64e"
  }
}
```

The following will cause a Form submission to fail:
* An invalid attribute is passed

*Response Codes:*
* 202 Submitted
* 422 For invalid requests as described above.
* 404 The Mobile Form cannot be found
* 401 Authentication failed
* 403 Authentication token expired


<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
