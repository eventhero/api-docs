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

### Send your participant data to EventHero for on-site management and event analytics

### Simple Integration with Your Event Registration System

Your EventHero account has the ability to receive changes from your registration system whenever a change occurs to your event.

For a complete list of current registration system integrations, see our [Partner Directory](http://eventhero.io/partners/).

If your registration system is not listed contact us - or connect it yourself using our API.

This API documentation page is created from the [EventHero API Docs repository](https://github.com/eventhero/api-docs).

Feel free to log Issues or submit Pull Requests with improvements.

# Endpoint

`https://app.eventhero.io/api/registrations`

# Authentication

To authenticate API requests, event organizers (customers of EventHero) need to create an access key for an event and communicate the access key to developers accessing API.

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

Please note that incompatible media types (e.g. `application/json`) will be rejected with HTTP 415 error code.

# Request Payloads

## Registration Confirmed

> Given the following JSON body in `reg_confirmed.json` file

```json
{
  "type": "registration.confirmed",
  "data": {
    "ref": "reg:1",
    "type": {
      "id": "rt:234",
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
        "question": { "label": "What is your T-shirt size?" },
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
data.ref | None | Registration reference. Should be unique across all registrations for this event. You may use identifiers in your system as the ref value.
data.type | Object | Object representing [Registration Type](#registration-type)
data.event | Object | Object representing [Event](#event)
data.registrant | Object | Object representing [Registrant](#registrant)

### Registration Type

Key | Example | Description
--- | ------- | -----------
ref | "123" | ID representing registration type
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


## Registration Cancelled

> Given the following JSON body in `reg_cancelled.json` file

```json
{
  "type": "registration.cancelled",
  "data": {
    "ref": "reg:1"
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
data.ref | "123" | Registration reference. Should match previously submitted ref for `registration.confirmed` activity.
