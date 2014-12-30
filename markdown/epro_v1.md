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
# ePRO Contract #

## Description ##
ePro - Medidata Patient Cloud Service

## Version ##
1.0.0

## Security ##
EPro serves responses over [SSL](http://en.wikipedia.org/wiki/Secure_Sockets_Layer) and authenticates requests using [MAuth](https://github.com/mdsol/mauth) unless otherwise documented.

TODO: Add authorization later, as there is yet no workable solution for device authentication at this time.

## Owners ##
* [Steven Myers](mailto:smyers@mdsol.com)
* [Isaac Wong](mailto:iwong@mdsol.com)

## Service Level Target (SLT) ##
TODO: establish SLTs for single, multiple, and write resources.
* **POST: /v1/**:  responds within 500ms for 99.9% of its requests at a peak load of 500 requests/sec
* **GET: /v1/**:  responds within 500ms for 99.9% of its requests at a peak load of 500 requests/sec

## Data bound to behavior (DBB) ##
Receives an ODM (Operational Data Model), and stores the ODM data for review by HelpDesk personnel or attempts to POST to RWS. Acts as a proxy interface to Rave, iMedidata, and Subjects Service.

## Application Programming Interface (API) ##
TODO: current API does not conform to the standards below.
* The content type of requests that contain a request body needs to be "application/json; charset=utf-8". Other content
types will lead to a 406 error. All responses which include a body will have `Content-Type` set to `application/json; charset=utf-8`
as well.
* A bad or malformed request would lead to a 400 error.
* All requests would lead to a 404 error if the requester has insufficient privileges.
* All API calls will respond with a 422 if a request's message body contains missing values or keys, or values with invalid data; or unsupported keys.

### Resource: ODM ###
ODM refers to CDISC (Clinical Data Interchange Standards Consortium) Operational Data Model, a vendor-independent format used to store, interchange between data management systems, or archive study data, study metadata or administrative data associated with clinical trials. It is the FDA standard for data archiving. EPro uses ODM to communicate electronically captured patient data. Created on the patient device, the ODM is relayed via the API to the Rave site via RWS.

#### Attributes ####

* `uuid` - The unique identifier of the ODM.
* `device_id` - OpenUDID of the client device.
* `field_data` - A JSON representation of the actual data that is being captured from the subject.
* `folder_oid` - The identifier associated with the Rave folder under which the form belongs.
* `form_data` - An encrypted JSON representation of the device data.
* `form_oid` - The identifier associated with the Rave form whose data is contained in the ODM.
* `log_line` - The ItemGroupRepeatKey from Rave.
* `rave_url` - The URL of the associated Rave application.
* `record_oid` - The identifier associated with the Rave record associated with the ODM
* `signature_date_time_entered` - The datetime at which the signature was entered.
* `signature_oid` - The identifier of the signature field. (Optional.)
* `site_oid` - The name of the site to which the subject belongs.
* `study_name` - The name of the study the subject is participating in.
* `study_uuid` - The unique identifier for an associated study.
* `subject_name` - The name of the subject.
* `subject_uuid` - The unique identifier for an associated subject.
* `version` - The version number of the ODM schema.


#### Headers ####

The `CONTENT-TYPE` header must include the string `application/json`.

#### Routes ####

**POST: `/api/odm`**

*Description:*

Create an ODM, and push its data to Rave.

*Request Body:*

``` javascript
{
    "form_data": "1d2033024b0d45713642460c153c0404654d5b7607113037"
}
```

Example of `form_data` field before encryption:
```json
{
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
```


*Response Codes:*

* 201 status code: New ODM Created, Returns ODM UUID
* 422 status code: Unprocessable Entity - Parameters are invalid / Header does not include "application/json"


**GET: `/api/odm_status/:uuid`**

*Description:*

Get the status of a request to store an ODM.

*Response Codes:*

* 200 status code: COMPLETE | INCOMPLETE
* 404 status code: Bad code

**GET: `/api/odm_group_status/:uuid`**

*Description:*

Get the status of a request to store an group of ODMs.

*Response:*

* 200 status code: COMPLETE|INCOMPLETE <number of ODMs in group>
* 404 status code: Bad code

**POST: `/api/odms/statuses`**

*Description:*

Get the status of a collection of requests to store an ODM.

*Request Body:*

``` javascript
{
    {"odms":[{"uuid":"odm1"},{"uuid":"odm2"},{"uuid":"badodm"}]}
}
```


*Response Codes:*

* 200 status code

*Response Body:*

``` javascript
{
    {"odms":[{"uuid":"odm1","status":"COMPLETE"},{"uuid":"odm2","status":"INCOMPLETE"},{"uuid":"badodm","status":"INVALID"}]}
}
```


### Resource: EPro Control ###
Supported EPro controls, for consumption by Rave Architect.
The EPro Controls are documented in file https://docs.google.com/a/mdsol.com/document/d/18WP14uWWOkq4TwkskbGXb5SB1XeT1Y9ORwAEOZeWztY/edit
(Note: Should be moved to EPro Medinet page)

#### Attributes ####

* `name` - Name of control
* `version` - Version of control
* `label` - Name of control
* `helptext` - Helptext to display
* `workflow_control` - Is this a workflow control? (Optional)
* `attributes` - Optional Attributes

#### Routes ####

**GET: `/api/controls(.:json)`**

*Description*

Gets the current EPro controls

*Response Body:*

``` javascript
{
    "WongBaker":
    {
      "name":"WongBaker","
      label":"Wong Baker",
      "version":"1.0",
      "helptext":"Wong Baker Pain Chart"
    }
}
```

*Response Codes:*
* 200 status code: OK, when a successful request occurs.


### Resource: Authentication ###
Authenticating a user

#### Attributes ####

* `user` - User Login
* `primary_password` - Password

#### Routes ####

**POST: `/api/:user/authenticate`**

*Description*

Authenticate a user.

*Request Body:*

``` javascript
{
    "password":{"primary_password":"Password*13"}
}
```

*Response Codes:*
* 200 status code: Upon a successful authentication.  The body will be the User packet from iMedidata
* 401 status code: Unauthorized if authentication failed.  The body will be in the explanation of authentication failure.

### Resource: ODM Status ###
Status of a specific ODM or groups of ODMS

#### Routes ####

**GET: `/api/odm_status/:uuid`**

*Description*

Returns: Status

*Response Codes:*
* 200 status code: When a successful request occurs. Status is returned in the body and can be:
COMPLETE: ODM was successfully POSTed to RWS
INCOMPLETE: ODM failure when POSTed
* 404 status code: When UUID is not found

### Resource: ODM Group Status ###
Status of a specific ODM

#### Routes ####

**GET: `/api/odm_group_status/<uuid>`**

*Description*

Get details about a group of ODMs

*Response Codes:*
* 200 status code: when a successful request occurs. Status is returned in the body and can be:
COMPLETE: ODM was successfully POSTed to RWS
INCOMPLETE: ODM failure when POSTed

* 404 status code: Not Found, when UUID is not found or when there is a discrepancy is the status of the group members, i.e. some complete, some incomplete

### Resource: ODM Statuses ###
Status of a collection of ODMs

#### Routes ####

**POST: `/api/odms/statuses`**

*Description*

Get details about a collection of ODMs

*Response Codes:*
* 200 status code. Status is returned in the body per ODM and can be:
COMPLETE: ODM was successfully POSTed to RWS
INCOMPLETE: ODM failure when POSTed
INVALID: ODM not located

### Resource: RWS Version ###
RWS version of the associated Rave site

#### Routes ####

**GET: `/api/rws_version?rws_url=<rave url>`**

*Description*

Returns: RWS Version

*Response Codes:*
* 200 status code: On success.  Versions in the body.
* 401 status code: On error, e.g. Rave URL not found


### Resource: Crash ###
Receive a crash report from the device

#### Attributes ####

* `device_id` - Device ID
* `device_model` - Device Model
* `patient_cloud_build` - Software Build #
* `crash_report_generated_at` - When the crash report was generated
* `crash_details` - Details of the crash

#### Routes ####

**POST: `/api/crash`**

*Description*

Get crash information.

*Request Body:*

``` javascript
{
    "device_id": "1a112"
}
```

*Response Codes:*
* 200 status code: Success


### Resource: Diagnostic Information ###
Receive Diagnostic Information from the device

#### Attributes ####

* `device_id` - Device ID
* `device_model` - Device Model
* `timestamp` - Timestamp
* `Other Tag/Value Pair(s)` - Other information to be stored

*Request Body:*

``` javascript
{
    "device_id": "1a112",
    "device_model": "ipod touch",
    "timestamp": "1-11-2013",
    "Tag1": "1",
    "Tag2": "2",
    "Tag3": "3",
    ...
}
```

#### Routes ####

**POST: `/api/add_diagnostic_info`**

*Response Codes:*
* 200 status code: Success

### Resource: Subject ###
Subject information.

#### Attributes ####

* `uuid` - Subject's UUID
* `name` - Subject's Name (Identifier)
* `study_uuid` - The unique identifier for the associated study.
* `site_uuid` - The unique identifier for the associated site.
* `study_site_uuid` - Study Site UUID
* `invited` - Is there an invitation for the Subject
* `associated` - Is the Subject associated with a User

#### Routes ####

**GET: `/api/studies/:study_uuid/subjects`**

*Description*

Returns the collections of subjects in the Study

*Response Body:*

``` javascript
{
    "uuid": "c721993c-0726-405c-aa78-d9771edc6e62",
    "name": "SUB15",
    "study_uuid": "a8659f1e-f9ad-44b2-b9cc-04258c9295b6",
    "site_uuid": "7dcafa87-7ac8-4241-92ca-9e0fe0e6d107",
    "study_site_uuid": "6caf9fff-37af-4465-aca1-e54f7f2efaba",
    "associated": false,
    "invited": false16769_devops_discussion@conf.hipchat.com
}
```

*Response Codes:*

* 200 status code: Success

**GET: `/api/studies/:study_uuid/sites/:site_uuid/studysites/:studysite_uuid/subjects`**

*Description:*

Returns the collection of Subjects in the Study/Site

*Response Codes:*
* 200 status code: Success


### Resource: Roles ###
User's role in the Epro Support application

#### Attributes ####

* `OID` - The unique identifier of the ODM.
* `name` - The unique identifier for an associated subject.
* `Other info as from imedidata` -


#### Routes ####

**GET: `/api/users/:user_uuid/studies/:study_uuid/epro_roles`**

*Description*

Returns the collections of roles for the user in the specific study, for the Patient Cloud.

*Response Codes:*

* 200 status code: Success
* 404 status code: User not assigned to study.

**GET: `/api/users/:user_uuid/epro_roles`**

*Description:*

Returns the collection of distinct roles for the user across all studies, for the Patient Cloud support app.

*Response Codes:*

* 200 status code: Success
* 404 status code: User not assigned to study.

### Resource: Users ###
Representation of a user using the application

#### Attributes ####

* `user_email` - Email of the user.


#### Routes ####

**GET: `/api/:user_email/study/:study_uuid/availability`**

*Description*

Determine if the user email is in use or associated with a study.

*Response Codes:*

* 200 status code: `available` if the user is not found in iMedidata.
* 200 status code: `is_in_study` if the user is associated with a study.
* 200 status code: `not_in_study` if the user is not associated with a study.
* 403 status code: `Service Error` if there was an error communicating with Subject Service

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

