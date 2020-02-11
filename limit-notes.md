# Notes on Limits

The limits structure within the Open SLA specification allows for a significant level of customisation of the period and values associated with the quotas and rates. It is important to understand the reasons for customisation and the likely outcomes in order to define limits which actually fulfil the business requirement.

## Periods with Totals and Quotas

Defining time periods (validities) may feel like an effective way to enforce fine grained quota control, however there are a few situations where period and quotas yield an undefined result.

### Short Periods

Defining multiple short periods (validities) within a single limit will apply the quotas defined within to each period. When the periods are less than the duration of the total then the allowed quota will appear to be higher than specified.

In fact, it is not advisable to have any total value other than 'period' in a limit with validities.

## Overloading totals

It is possible to overload a consumers quota and build complex limits. This can be an effective manner of dealing the variances in the pattern of requests for a client.

When defining overloaded totals, care must be taken to ensure that unintended blocking of requests does not happen.

### When "Minutes * 60 != hours"

An example of overloading is to specify both minutes and hours in the same limit. If an hourly limit of 10,000 requests is required, there is little value in setting a minutely total of 167 as this will yield at maximum requests per minute, 10,000 calls per hour.

Instead, it would be better to look at the required burst rate of the consumer, and apply that at a minutely basis, and leave the hourly total enforcement in place.

```yaml
limits:
  - name: normal hours
    totals:
      hour: 100000
      minute: 250
```

This limit will allow the client to consistently burst to 250 requests per minute, but still only be allowed 10,000 requests per hour. Meaning that they may be throttled before the hour is up.

## Examples

This section contains a number of examples of limits which could form the basis for a customised definition.

**Peak Hours**

In situations where a client may have busy periods, specifying that a client can request at an increased rate can provide 'burst' like traffic shaping.

In the example below, the nominal request rate is limited to 5 per second, however between the hours of 9:00 - 10:30 the maximum request rate is raised to 20 per second.

```yaml
limits:
  - name: peak hours
    rate:
      value: 20
      duration: second
    validity:
      - name: peak hours morning
        start: 09:00
        end: 10:30
  - name: normal hours
    rate:
      value: 5
      duration: second
```

**Free Periods**

If a client is given periods during the day when they will not be 'billed' (in terms of quota usage) then this could be represented as detailed below.

In this example, the client will continue to be restricted to a maximum of 10 requests per second, although their hourly quota will not be used up during the two validity periods. Over the period of the day, they will still be limited to a maximum of 40,000 requests, with the requests during the 'free' periods also being counted towards this total.

```yaml
limits:
  - name: Free Periods
    validity:
      - name: Free Hours 1
        start: 03:00
        end: 05:00
      - name: Free Hours 2
        start: 11:00
        end: 13:00
    totals:
      hour: 0
  - name: Normal Quota
    totals:
      hour: 5000
      day: 40000
    rate:
      value: 10
      duration: second
```

If requests during the free periods should not count towards the daily limit, then the following definition will cause this behaviour.

```yaml
limits:
  - name: Free Periods
    validity:
      - name: Free Hours 1
        start: 03:00
        end: 05:00
      - name: Free Hours 2
        start: 11:00
        end: 13:00
    totals:
      hour: 0
      day: 0
  - name: Normal Quota
    totals:
      hour: 5000
      day: 40000
    rate:
      value: 10
      duration: second
```

**Expensive Operations**

When API operations are 'expensive' (they take a lot of time or resources to execute) it may be desirable to limit consumers quota for those endpoints only.

The example below enforces a quota (10 per minute and a maximum rate of 2 per second) only on the write (PUT, POST, DELETE) endpoints for an API. All the other endpoints (Read) are quotaless.

```yaml
limits:
  - name: Write Operations
    operationIds: [ writeAccountData, deleteAccountData, createAccount ]
    totals:
      minute: 10
    rate:
      value: 2
      duration: second
```

**Periods Spanning Multiple Hours**

If the quotas that need to be applied are calculated on spans of time then the totals period option may utilised.

In the example below, the day has be divided into 4 periods of 6 hours, Starting at 3:00, meaning that the final quarter spans midnight (21:00-03:00). Each 6 hour period has a quota of 27 total requests.

Across the entire day, the maximum rate is 10 requests per hour.

```yaml
limits:
  - name: Midnight Span
    validity:
      - name: morning
        start: 00:00
        end: 03:00
      - name: evening
        start: 21:00
        end: 24:00
    totals:
      period: 27
  - name: Daytime 1
    validity:
      - name: period1
        start: 03:00
        end: 09:00
    totals:
      period: 27
  - name: Daytime 2
    validity:
      - name: period2
        start: 09:00
        end: 15:00
    totals:
      period: 27
  - name: Daytime 3
    validity:
      - name: period3
        start: 15:00
        end: 21:00
    totals:
      period: 27
  - name: all
    rate:
      value: 10
      duration: hour
```

**Minute Quotas within Periods**

If totals quotas are applied per minute, then it is possible to adjust these using validity periods. Note: this will cause unpredictable results if larger durations are used.

The example below adjusts the per minute quotas for a consumer between the hours of 9:00 - 12:00. At all other times, the per minute quota of 10 per minute is enforced.

```yaml
limits:
  - name: Upped quota
    validity:
      - name: period1
        start: 09:00
        end: 12:00
    totals:
      minute: 20
  - name: normal
    totals:
      minute: 10
```

Note: Attempting to use larger durations in a limit with a validity may result in unexpected results. For example, utilising the `week` duration will likely not limit not being applied as expected.