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
REVIEW: include in initial contract? (use existing ePRO SLT?)

## Security ##
ePro serves responses over [SSL](http://en.wikipedia.org/wiki/Secure_Sockets_Layer)
and performs user [Authentication](#authentication).

## Application Programming Interface (API)

* This service returns hypermedia-enriched responses. The media type of the response is `application/vnd.hale+json`.

### Authentication
Each request to the service should include either a username and password, or an authentication token.
If the request contains a username and password, it will be verified with iMedidata and a new authentication
token will be returned in the response.
Subsequent requests should include that authentication token until the token expires.

TODO: how to represent authentication params? (in req/resp). As attributes, shown in json examples below?

### List Available Form Data ###
REVIEW: the homepage entry point - name?

#### Route ####

**GET: `/v1/forms`**

*Description:*

View all forms and edit checks available for a given user.

*Query Parameters:*

* user_uuid: The iMedidata user UUID.

*Response Body:*

Returns a collection of link relations that can be used to retrieve the individual resources in subsequent requests.

* `form_items` - The list of individual form data URIs, corresponding to the given user.
* `edit_check_items` - The list of individual edit check data URIs, corresponding to the given user.

Note: The `profile`, `type` and `help` transitions are defined in
[RFC 5988 - Web Linking](http://tools.ietf.org/html/rfc5988). (TODO: use this link?)

```json
{
   "_links": {
      "self": { "href": "https://epro.example.com/v1/forms?user_uuid=20649fc0-1b71-4267-a921-42520b14815d" },
      "profile": { "href": "https://epro.example/alps/Forms" },
      "help": { "href": "https://github.com/mdsol/epro-app" },
      "form_items": [
          {
              "href": "https://epro.example.com/v1/forms/4efbcc84-658a-422e-9488-08b295501055",
              "type": "https://epro.example.com/alps/Forms#form"
          },
          {
              "href": "https://epro.example.com/v1/forms/643c94d8-8f33-446e-aeec-d0fc98952b3b",
              "type": "https://epro.example.com/alps/Forms#form"
          },
          {
              "href": "https://epro.example.com/v1/forms/3cc83609-ad1a-4add-8371-2794f0d262e2",
              "type": "https://epro.example.com/alps/Forms#form"
          }
      ],
      "edit_check_items": [
          {
              "href": "https://epro.example.com/v1/edit_checks/cdd07513-c561-46ba-9834-e3940602922d",
              "type": "https://plinth-sandbox.imedidata.net/alps/Forms#edit_check"
          },
          {
              "href": "https://epro.example.com/v1/edit_checks/43c94d8-8f33-446e-aeec-d0fc98952b3b",
              "type": "https://plinth-sandbox.imedidata.net/alps/Forms#edit_check"
          }
      ]
    }
}
```

*Response Codes:*

* 200 Success (also for an empty list)
* 401 Authentication failed
* 403 Authentication token expired


### Resource: Form
TODO: A form is...

#### Attributes ####
* `uuid` (read-only - managed by the service) - Unique identifier for the Form (length between 24 and 36 inclusive).
* REVIEW: Get attributes from current ePRO (assuming not the blob from ODM?)

#### Routes ####
**GET: `/v1/forms/:uuid`**

*Description:*

Fetches a Form given the Form's uuid.

*Query Parameters:*

* user_uuid: The iMedidata user UUID.

*Response Body:*

TODO: get actual form data example from current ePRO
JSON:
``` javascript
{
    "uuid": "b309d909-94ca-4534-95f6-d6db0319b3fa",
    "<form data here>": "...",
    "_links": {
        "self": { "href": "https://epro.example.com/v1/forms/b309d909-94ca-4534-95f6-d6db0319b3fa?user_uuid=20649fc0-1b71-4267-a921-42520b14815d" },
        "profile": { "href": "https://epro.example/alps/Forms" },
        "type": { "href": "https://epro.example.com/alps/Forms#form" },
        "help": { "href": "https://github.com/mdsol/epro-app" }
    }
}
```

*Response Codes:*

* 200 Successfully return JSON representation of form.
* 404 The form cannot be found
* 401 Authentication failed
* 403 Authentication token expired

TODO: include note about e-tag support?

**POST: `/v1/forms/:uuid/submit`**

*Description:*

Submits the form data for a subject.

*Request Body:*
REVIEW: study_uuid and rave_url can be determined by form uuid

``` javascript
{
    "user_uuid": "20649fc0-1b71-4267-a921-42520b14815d",
    "device_id": "1a112",
    "<form_data_here>": "..."
}
```

The following will cause a Form submission to fail:
* An invalid attribute is passed
REVIEW: include actual field validations?

*Response:*
REVIEW: a form submission won't have response body, same as current ePRO, correct?

*Response Codes:*
* 202 Submitted
* 422 For invalid requests as described above.
* 404 The form cannot be found
* 401 Authentication failed
* 403 Authentication token expired

### Resource: Edit Check
TODO: An edit check is...

#### Attributes ####
* `uuid` (read-only - managed by the service) - Unique identifier for the Edit Check (length between 24 and 36 inclusive).
* TODO: Get attributes from current ePRO

#### Routes ####
**GET: `/v1/edit_checks/:uuid`**

*Description:*

Fetches an Edit Check given the its uuid.

*Query Parameters:*

* user_uuid: The iMedidata user UUID.

*Response Body:*

TODO: get actual edit check example from current ePRO
JSON:
``` javascript
{
    "uuid": "cdd07513-c561-46ba-9834-e3940602922d",
    "<edit check here>": "...",
    "_links": {
        "self": { "href": "https://epro.example.com/v1/edit_checks/cdd07513-c561-46ba-9834-e3940602922d?user_uuid=20649fc0-1b71-4267-a921-42520b14815d" },
        "profile": { "href": "https://epro.example/alps/Forms" },
        "type": { "href": "https://epro.example.com/alps/Forms#edit_check" },
        "help": { "href": "https://github.com/mdsol/epro-app" }
    }
}
```

*Response Codes:*

* 200 Successfully return JSON representation of edit check.
* 404 The edit check cannot be found
* 401 Authentication failed
* 403 Authentication token expired
