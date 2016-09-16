---
title: EventHero API Reference

toc_footers:
  - <a href='#'>Sign Up for a Developer Key</a>
  - <a href='https://github.com/tripit/slate'>Documentation Powered by Slate</a>

includes:
  - errors

search: false
---

# Introduction

### Send your registration data to EventHero for on-site management and event analytics

Your EventHero account has the ability to receive changes from your registration system whenever a change occurs to your event.

For a complete list of current registration system integrations, see our [Partner Directory](http://eventhero.io/partners/).

If your registration system is not listed contact us - or connect it yourself using our API.

This API documentation page is created from the [EventHero API Docs repository](https://github.com/eventhero/api-docs).
Feel free to log Issues or submit Pull Requests with improvements.

# Endpoint

`https://app.eventhero.io/api/registrations`

# Authentication

To publish registration data to an EventHero event, each event requires its own API Access Key.
Event organizers (customers of EventHero) need to create an access key for an event and communicate the access key to developers accessing API.
Access keys are event specific, and perform dual function: authentication of the caller of the API, and authorization of the caller to access a particular event.

Access key needs to be passed in the HTTP Authorization header using Bearer scheme: `Authorization: Bearer <ACCESS_KEY>`

> Example API call with authorization header:

```shell
curl "https://app.eventhero.io/api/registrations" \
  -H "Authorization: Bearer <ACCESS_KEY>" \
  ...
```
<aside class="notice">
Remember to replace &lt;ACCESS_KEY&gt; in this documentation and examples with your event access key!
</aside>

## Generating an API Access Key

To generate the key for a registration system, visit the Event Summary screen for your event (https://app.eventhero.io/manage/xxxxxxxxxxxxxxxxxxxxxxxx).

Enter the name for your key (e.g. MyRegSystem-EventName) and select Create.

You will be presented with an API Access Key that is 64 characters in length.

# Versioning and Media Types

EventHero API uses different content types for versioning.

Currently the v1 version of the API accepts only `application/vnd.eventhero.registrations.v1+json` media type.

When calling API endpoints the HTTP requests need to include the following Content-Type header:
`Content-Type: application/vnd.eventhero.registrations.v1+json`

> Example API call with an acceptable Content-Type header:

```shell
curl "https://app.eventhero.io/api/registrations" \
  -H "Content-Type: application/vnd.eventhero.registrations.v1+json" \
  ...
```

Please note that incompatible media types (e.g. very common `application/json`) will be rejected with HTTP 415 error code.

# Registration ID

All submitted registration activities need to include "id" field, which represents an instance of the registration in your system.
The "id" value may be used to reference previously submitted registrations for modifications or cancellations.
For example to cancel a previously submitted registration, you can send 'registration.cancelled' request that contains the same registration "id".

You are free to choose how you generate registration ID for requests submitted to EventHero API.
It does not have to mean anything to event organizer, although you may choose to use order number or registration number as the registration id.

The value of registration ID must be unique within the scope of event (or withing the same access key).

# Request Payloads

## Registration Confirmed

> Given the following JSON body in `reg_confirmed.json` file

```json
{
  "type": "registration.confirmed",
  "data": {
    "id": "reg-1",
    "type": {
      "id": "gen-admission-1",
      "name": "General Admission"
    },
    "event": {
      "name": "My Event",
      "url": "http://eventbrite.com/..."
    },
    "registrant": {
      "first_name": "Lindsey",
      "last_name": "Tessmer",
      "job_title": "QA",
      "company": "Redargyle",
      "phone": "(585) 412-2153",
      "email": "lindsey@redargyle.com",
      "address": {
        "street_address": "2220 Sylvania Avenue",
        "extended_address": "#33",
        "locality": "Knoxville",
        "region": "TN",
        "postal_code": "37920",
        "country": "USA"
      }
    },
    "answers": [
      {
        "question": { "id": "q-123", "text": "What is your T-shirt size?" },
        "answer": "XXL"
      }
    ]
  }
}
```

```shell
cat reg_confirmed.json > curl -i -X POST -d @- \
  -H 'Content-Type: application/vnd.eventhero.registrations.v1+json' \
  -H 'Authorization: Bearer <ACCESS_KEY>' \
  https://app.eventhero.io/api/registrations
```

> The above command returns HTTP 200 success and JSON structure like this:

```json
{
  "status": 200
}
```

Key | Example | Description
--- | ------- | -----------
type | "registration.confirmed" | Name of the activity
data |  | Object that contains information on the activity
data.id | "reg-1" | Registration reference. Should be unique across all registrations for this event.
data.type | Object | Object representing [Registration Type](#registration-type)
data.event | Object | Object representing [Event](#event)
data.registrant | Object | Object representing [Registrant](#registrant)
data.answers | Array | Array of [Answers](#answer)

### Registration Type

Key | Example | Description
--- | ------- | -----------
id | "123" | ID representing registration type identifier
name | "General Admission" | Name for the registration type

### Event

Key | Example | Description
--- | ------- | -----------
url | "http://example.com/my-event" | URL for the source event
name | "My Event" | Name for the event

### Registrant

Key | Example | Description
--- | ------- | -----------
first_name | "Lindsey" | |
last_name | "Tessmer" |  |
job_title | "QA" | |
company | "Redargyle" | |
phone | "(585) 412-2153" | |
email | "lindsey@redargyle.com" | |
address | Object | Object representing address of the registrant |
address.street_address | "2220 Sylvania Avenue" | |
address.extended_address | "#33" | |
address.locality | "Knoxville" | |
address.region | "TN" | |
address.postal_code | "37920" | |
address.country | "USA" | |

### Answer

Key | Example | Description
--- | ------- | -----------
question.id | "q-123" | |
question.text | "What is your T-shirt size?" | |
answer | "XXL" | |


## Registration Cancelled

> Given the following JSON body in `reg_cancelled.json` file

```json
{
  "type": "registration.cancelled",
  "data": {
    "id": "reg-1"
  }
}
```

```shell
cat cancelled.json > curl -i -X POST -d @- \
  -H 'Content-Type: application/vnd.eventhero.registrations.v1+json' \
  -H 'Authorization: Bearer <ACCESS_KEY>' \
  https://app.eventhero.io/api/registrations
```

> The above command returns HTTP 200 success and JSON structure like this:

```json
{
  "status": 200
}
```

Key | Example | Description
--- | ------- | -----------
type | "registration.cancelled" | Name of the activity
data |  | Object that contains information on the activity
data.id | "reg-1" | Registration ID. Should match previously submitted id for `registration.confirmed` activity.
