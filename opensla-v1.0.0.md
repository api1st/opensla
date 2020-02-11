# Open SLA

**Version 1.0.0**

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in BCP 14 RFC2119 RFC8174 when, and only when, they appear in all capitals, as shown here.

This document is licensed under [The Apache License, Version 2.0](http://www.apache.org/licenses/LICENSE-2.0.html).

## Introduction

The Open SLA is a proposal from [Apiway](https://www.apiway.net) for the formal documentation of Service Level Agreements for APIs. The Open SLA Specification (OSS) is a structured document that can be interpreted by humans and computers, although not all concepts captured by the specification are measurable by a computer as they define the methods of human interaction around support.

This documentation and the format itself relies heavily on the standards established by the [Open API Specification (v3)](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/3.0.2.md).

### Open SLA Measurements

The Open SLA document presents a variety of values by which the API is expected to perform. When measuring the performance of an API against the values within the Open SLA document, it is important to only include the successful requests as these represent the non-exceptional scenarios which are described within the Open SLA.

## Table of Contents

## Definitions

### OpenSLA Document

A document or set of documents that describe the Service a consumer can expect from an API.

### OpenAPI Document

An OAS document that describes the functionality of an API from a technical perspective. Throughout this document the OAS MUST refer to the same API (although not necessarily version) in all instances.

## Specification

### Versions

This Open SLA Documentation uses the Semantic Versioning 2.0.0 specification. All version numbers MUST not be prefixed.

### Format

The Open SLA utilises a JSON format which is representable using either JSON or YAML.

### Document Structure

### Data Formats

### Rich Text Formatting

Throughout the specification `description` fields are noted as supporting CommonMark markdown formatting. Where OpenAPI tooling renders rich text it MUST support, at a minimum, markdown syntax as described by [CommonMark 0.27](http://spec.commonmark.org/0.27/). Tooling MAY choose to ignore some CommonMark features to address security concerns.

### File names

When storing or delivering Open SLA documents the filename structure below is RECOMMENDED as a way of ensuring consistency across the API estate.

#### When the SLA does not have a variant

`<Api-name>-sla-<document-version>.yaml`

#### When a variant has been defined for the SLA

`<Api-name>-sla-<document-version>-<variant>.yaml`

**Filename example:**

An `orders` API where the SLA document is `v1.1.0` with a variant of `external`.

`orders-sla-1.1.0-external.yaml`

### Schema

In the following description, if a field is not explicitly **REQUIRED** or described with a MUST or SHALL, it can be considered OPTIONAL.

#### OpenSLA Object

This is the root document object for the OpenSLA.

##### Fixed Fields

| Field Name | Type | Description |
| ---------- | ---- | ----------- |
| opensla    | `string` | **REQUIRED**. This string MUST be the Semantic Version number of the Open SLA specification version that the Open SLA document is using. |
| info | [Info Object](#info-object) | **REQUIRED**. Provides metadata about the SLA. This metadata MAY be used by tooling as required. |
| oas | [Oas Object](#oas-object) | **REQUIRED**. Links the SLA document to one or more [Open API Specification versions](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/3.0.0.md). |
| support | [Support Object](#support-object) | Defines the non-technical support offering that underpins the SLA. |
| continuity | [Continuity Object](#continuity-object) | Defines business continuity provision that have been made for the API. |
| recovery | [Recovery Object](#recovery-object) | Defines the disaster recovery objectives for the API provision. |
| runtime | [[Runtime Object](#runtime-object)] | List of Runtime definitions. These define the technical runtime SLA objectives of the API. |
| limits | [[Limits Object](#limits-object)] | List of API consumption limitations for a consumer. |

#### Info Object

This object provides metadata about the Open SLA document. The metadata MAY be utilised by tooling and MAY be presented in editing and documentation for convenience.

##### Fixed Fields

| Field Name | Type | Description |
| ---------- | ---- | ----------- |
| title      | `string` | **REQUIRED**. The title of the SLA represented by the specification. |
| description | `string` | A short description of the SLA. CommonMark syntax MAY be used for rich text representation. |
| version | `string` | **REQUIRED**. The version of the OpenSLA document (which is distinct from the OpenSLA specification version or the OpenAPI documentation or specification version). |
| variant | `string` | Defines a name for the variant of the SLA the specification represents. This COULD utilise consumer name if the SLA is consumer specific. |

**Info Object example:**

``` yaml
info:
  title: SLA for the Apiway Weather API
  description: The Weather API from Apiway offers real time access to weather information. This SLA defines both the technical (latency/throughput) components and the human support interaction components of the API.
  version: 1.0.0
  variant: gold
```

#### Oas Object

This object links the SLA document to one or more versions of an Open API specification.

##### Fixed Fields

| Field Name | Type | Description |
| ---------- | ---- | ----------- |
| url | `string` | **REQUIRED**. A URL referring the the OAS v3 document that describes the API that the SLA impacts. The value of this field is considered to be the base URL for any relative URLs in the versions list. MUST be in the format of a URL. |
| versions | [[OAS Version Object](#oas-version-object)] | A list of Associated OAS version objects. SHOULD populate this object or the SLA will refer to all available OAS versions which are not explicitly listed in other SLA documents. |

**Oas Object example:**

``` yaml
oas:
  url: https://core.apinfinity.net/oas/
  versions:
  - version: 1.0.17
    url: 17?v=1.0.17
  - version: 1.1.0
    url: 21/content
  - version: 2.0.4
    url: https://www.github.com/myteam/myrepo/api.yaml?v=2.x
```

#### Oas Version Object

Holds metadata about the individual versions of the OAS that the SLA refers to.

##### Fixed Fields

| Field Name | Type | Description |
| ---------- | ---- | ----------- |
| version | `string` | **REQUIRED**. The Semantic version of the OAS document. MUST be a valid SemVer string. MUST be published version of the OAS. |
| url | `string` | **REQUIRED**. A fragment to append to the [Oas Object](#oas-object) `url` which specifies the full URL to the OAS, OR the absolute URL to the OAS. |

**Oas Version Object example:**

When the oas:version property is being suffixed with additional path or query parameters.

``` yaml
version: 1.0.17
url: ?v=1.0.17
```

When the Oas Version object represents an absolute URL.

``` yaml 
version: 2.0.4
url: https://core.apinfinity/net/oas/3881?v=2.x
```

#### Support Object

Contains non-technical information around the provision of support of the API.

##### Fixed Fields

| Field Name | Type | Description |
| ---------- | ---- | ----------- |
| channels   | [Channels Object](#channels-object) | Contains the support contact channels for the API. SHOULD be provided. |
| levels | [Levels Object](#levels-object) | Contains the classification levels for issues that may be raised via channels. MUST be provided if channels object is populated. |
| versions | [Support Versions Object](#support-versions-object) | Contains information on the support of different API versions. SHOULD be provided if there are intended on being multiple major versions of the API. |

**Support Object example:**

``` yaml
support:
  channels:
    - name: email
      uri: team_support.dl@example.com
      levels: [p1, p2]
    - name: jira
      uri: https://jira4.example.com/team_name/
      levels: [p2]
    - name: slack
      uri: #team_support
      levels: [p1, p2]
    - name: phone
      uri: +44 (0)1234 567890
      note: Phone active outside of normal UK working hours
      levels: [p1]
  levels:
    - name: p1
      description: Priority 1 support
      response: 60
      fix: 240
      relevance:
        - Production Issues
        - 5xx HTTP Status responses
    - name: p2
      description: Priority 2 support
      response: 720
  versions:
    concurrentSupported: 2
    supportedLifetime: 1 Year from date of release
```

#### Channels Object

##### Fixed Fields

| Field Name | Type | Description |
| ---------- | ---- | ----------- |
| name | `string` | **REQUIRED**. The name of the support contact channel. MUST be unique within the list of channel objects. |
| uri | `string` | **REQUIRED**. The manner of utilising the support channel. MUST unambiguously define how to utilise the support channel. |
| levels | [`string`] | **REQUIRED**. A list of the priority levels that can be raised using the support channel. Each entry MUST have a corresponding entry in the levels collection. |
| note | `string` | Miscellaneous information about the support channel that may aid a consumer in utilising it correctly. |

**Channels Object example:**

``` yaml
name: email
uri: team_support.dl@example.com
levels: [p1, p2]
```

#### Levels Object

##### Fixed Fields

| Field Name | Type | Description |
| ---------- | ---- | ----------- |
| name | `string` | **REQUIRED**. The name of the issue classification level. MUST be unique within the list of level objects. |
| description | `string` | Descriptive text about the level. |
| response | `string` | A susinct description of the time between raising an issue of this level and receiving a response from the support function. |
| fix | `string` | A susinct description of the time between raising an issue of this level and the issue being resolved. |
| relevance | [`string`] | A list of issue types that are considered relevant to the support level. |

**Levels Object example:**

``` yaml
name: p2
description: Priority 2 support
response: 90
relevance:
  - Login Issues
  - Printer Issues
```

#### Support Versions Object

##### Fixed Fields

| Field Name | Type | Description |
| ---------- | ---- | ----------- |
| concurrentSupported | `integer` | **REQUIRED**. The number of major versions of the API that will be supported concurrently. |
| supportedLifetime | `string` | The minimum length of time a major version of an API will be supported. SHOULD be described in plain English. |

**Support Versions Object example:**

``` yaml
concurrentSupported: 2
supportedLifetime: 1 Year from date of release
```

#### Continuity Object

Holds information on the Business Continuity design of the API.

##### Fixed Fields

| Field Name | Type | Description |
| ---------- | ---- | ----------- |
| multiInstance | `boolean` | Whether the API has multiple instances deployed. Defaults to `false` if not specified. |
| multiDatacentre | `boolean` | Whether the API is deployed across multiple data centres. Defaults to `false` if not specified. |
| configuration | `string` | The configuration of the API deployment. SHOULD be specified if `multiInstance` or `multiDatacentre` has been set to `true`. Possible values are "active-active" or "active-passive". |

**Continuity Object example:**

``` yaml
continuity:
  multiDatacentre: true
  multiInstance: true
  configuration: active-active
```

#### Recovery Object

Holds information on Data and System recovery objectives.

##### Fixed Fields

| Field Name | Type | Description |
| ---------- | ---- | ----------- |
| rpo | `integer` | The Recovery Point Objective in minutes for the APIs data. |
| rto | `integer` | The Recovery Time Objective in minutes for the APIs accessibility and functionality. |

**Recovery Object example:**

``` yaml
recovery:
  rpo: 5
  rto: 1
```

#### Runtime Object

Contains one or more runtime definitions which SHALL be evaluated in a top down manner until a match for the request context is found. It is therefore RECOMMENDED that the most generic (or fall back) definition appears last in the list. It is also RECOMMENDED that the most specific definitions appear earlier in the list.

##### Fixed Fields

| Field Name | Type | Description |
| ---------- | ---- | ----------- |
| name | `string` | **REQUIRED**. The name of the runtime definition. MUST be unique within the list. |
| operationIds | [`string`] | If specified, one or more `operationId` from the referenced OAS. The operationId MUST exist in **all** the OAS Versions defined in the [OAS Version Object](#oas-version-object).  |
| validity | [[Validity Object](#validity-object)] | A list of timespans that define the validity period of the runtime definition. If not specified the runtime definition SHALL apply at all times during the day. |
| availability | `number` | The expected 'uptime' of the API as a percentage during the validity periods. MUST be a number between `0` and `100` inclusive. |
| concurrency | `integer` | The maximum number of concurrent requests that can be processed. |
| throughput | [[Throughput Object](#throughput-object)] | A list of throughput metric definitions. |
| 5xxResponsePercent | `number` | The expected percentage of responses that will have a 5xx series Status code. MUST be a number between `0` and `100` inclusive. |

**Runtime Object example: 1**

``` yaml
runtime:
  - name: default
    availability: 99.5
    concurrency: 150
    throughput:
      - rps: 50
        latency:
          - percentile: 99
            maximum: 500
            mean: 80
    5xxResponsePercent: 3
```

**Runtime Object example: 2**

``` yaml
runtime:
  - name: Peak Hours
    availability: 100
    validity:
      - name: peak hours morning
        start: 06:00
        end: 10:13:50
    throughput:
      - rps: 80
        latency:
          - percentile: 99
            maximum: 400
            mean: 74
    5xxResponsePercent: 5
  - name: default
    availability: 99.5
    throughput:
      - rps: 50
        latency:
          - percentile: 99
            maximum: 500
            mean: 80
    5xxResponsePercent: 1
```

#### Validity Object

Represents times of the day where traffic volumes are different. All times MUST be represented as UTC.

A value of `00:00:00` and `24:00:00` are considered as the same value.

##### Fixed Fields

| Field Name | Type | Description |
| ---------- | ---- | ----------- |
| name | `string` | **REQUIRED**. The name of the validity. The name MUST be unique within the list of validity objects. |
| start | `string` | **REQUIRED**. The start time of the validity. Format MUST be `HH:MM` or `HH:MM:SS`. |
| end | `string` | **REQUIRED**. The end time of the validity. Format MUST be `HH:MM` or `HH:MM:SS`. MUST be distinct from the `start` field value. |

**Validity Object example:**

``` yaml
name: peak hours morning
start: 06:00
end: 10:13:50
```

#### Throughput Object

Holds computer measurable metrics on Requests per Second and latency. SHOULD be defined in lowest to highest ordering of the `rps` value.

##### Fixed Fields

| Field Name | Type | Description |
| ---------- | ---- | ----------- |
| rps | `number` | **REQUIRED**. The upper value for the Requests per Second metric that defines the latency figures. |
| latency | [[Latency Object](#latency-object)] | **REQUIRED**. A list of latency metric values. MUST have at least one entry. |

**Throughput Object example:**

``` yaml
throughput:
  - rps: 20
    latency:
      - percentile: 99
        maximum: 150
```

#### Latency Object

##### Fixed Fields

| Field Name | Type | Description |
| ---------- | ---- | ----------- |
| percentile | `number` | **REQUIRED**. The percentile of requests that will meet the metrics. |
| maximum | `integer` | The maximum latency (in milliseconds) for requests in the given percentile. MUST specify a `maximum` or `mean` (or both). |
| mean | `integer` | The mean latency (in milliseconds) for requests in the given percentile. MUST specify a `maximum` or `mean` (or both). |

**Latency Object example:**

``` yaml
percentile: 99
maximum: 500
mean: 100
```

#### Limits Object

Contains one or more limit definitions which SHALL be evaluated in a top down manner until a match for the request context is found. It is therefore RECOMMENDED that the most generic (or fall back) definition appears last in the list. It is also RECOMMENDED that the most specific definitions appear earlier in the list.

To disable a limit for a property, set the value to 0.

##### Fixed Fields

| Field Name | Type | Description |
| ---------- | ---- | ----------- |
| name | `string` | **REQUIRED**. The name of the limit definition. MUST be unique within the list. |
| operationIds | [`string`] | If specified, a list of one or more `operationId` from the referenced OAS. The operationId MUST exist in **all** the OAS Versions defined in the [OAS Version Object](#oas-version-object).  |
| totals | [Totals Object](#totals-object) | An object representing the 'totals' (or counts) and the periods over which they are applied. |
| concurrent | `integer` | The maximum number of concurrent requests that is allowed from one consumer. |
| rate | [Rate Object](#rate-object) | An object representing the maximum request rate for the consumer. |
| validity | [[Validity Object](#validity-object)] | A list of timespans that define the validity period of the limit definition. If not specified the runtime definition SHALL apply at all times during the day. |

**Limits Object example:**

``` yaml
limits:
  - name: read operations peak hours
    operationIds: [ getDataList, getDataItem ]
    totals:
      period: 4000
    rate:
      value: 100
      duration: minute
    concurrent: 4
    validity:
      - name: peak hours morning
        start: 06:00
        end: 10:13:50
      - name: peak hours evening
        start: 16:30
        end: 20:00
  - name: read operations all hours
    operationIds: [ getDataList, getDataItem ]
    totals:
      hour: 2000
      day: 50000
      week: 100000
    rate:
      value: 2
      duration: second
  - name: all operations
    totals:
      month: 100000
    concurrent: 2
    rate:
      value: 1
      duration: second
```

#### Totals Object

Contains the total cumulative number of requests that will be allowed by a consumer within the given time period. If more than one duration has a value assigned then all totals will be evaluated in isolation for each request.

##### Fixed Fields

| Field Name | Type | Description |
| ---------- | ---- | ----------- |
| period | `integer` | The maximum number of requests allowed over the entire period defined by the Validity entries. MUST be paired with `validity` entries. |
| minute | `integer` | The maximum number of requests allowed per minute. |
| hour | `integer` | The maximum number of requests allowed per hour. |
| day | `integer` | The maximum number of requests allowed per day. |
| week | `integer` | The maximum number of requests allowed per week. |

**Totals Object example: 1**

``` yaml
totals:
  hour: 2000
  day: 50000
  week: 100000
```

**Totals Object example: 2**

``` yaml
totals:
  period: 5000
  minute: 15
```

#### Rate Object

Contains the maximum ingress rate per consumer for the API. Distinct from the totals limits, the rate value refers to the frequency of requests rather than the number being made.

##### Fixed Fields

| Field Name | Type | Description |
| ---------- | ---- | ----------- |
| value | `integer` | **REQUIRED**.  The maximum number of requests allowed over unit of time as defined by the duration property. MUST be a value greater than 0. |
| duration | `string` | **REQUIRED**. The unit of time over which the `value` is applied.  Possible values are 'second' or 'minute'. |

**Rate Object example:**

``` yaml
rate:
  value: 2
  duration: second
```

## Appendix A: Revision history

| Version | Date | Comments |
| ------- | ---- | -------- |
| 1.0.0   | 11-Feb-2020 | Published |
