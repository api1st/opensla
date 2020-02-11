# Open SLA

Within enterprises it is often difficult to build trust in an API within the community of would-be consumers. Trust is most often built through the experiences of early adopters and enforced by the number of open issues in GitHub or the number of stars or forks. Such community lead indicators are often not present in the enterprises controlled environment.

In order to build trust in the enterprise where there may not be a large critical user base, automated tools can be employed to perform the role of guarantor of ability of a particular API. However metrics alone are not enough to be a measure of an APIs ability, rather the metrics need to be judged against what the API is supposed to be delivering.

The Open SLA provides the bridge between the expectations of the consumer community and the achieved performance of the API.

## Quick Start

1. Read the [specification document](opensla-v1.0.0.md)

2. Create a public SLA `.yaml` file for the general consumers of your API. Fill the Yaml with as much or as little detail as is needed. Generally more is better.

    Use the [example](example-sla-v1.0.0-consumer1.yaml) as a basis for your own SLA if necessary.

3. (Optional) Create private 'variant' SLA versions for specific consumer tiers based on their usage requirements.

## Brief overview of the standard

An Open SLA is intended for use alongside an Open API Specification. Although there is no prescribed version of OAS that must be used at the date of this publish, `v3.0.2` was most current and therefore we recommend that as a target if possible.

The Open SLA documentation has been written in a fashion similar to that of the Open API Specification v3 documentation. This is a deliberate attempt to make the style familiar and make the adoption of the Open SLA along side the Open API specification a more coherent experience.

## Why formalise an SLA

By providing an Open SLA specification for an API it will be obvious to present and future consumers that those responsible for its production have thought about the processes and resources required to keep the API available to them as consumers.

Whilst filling out the SLA, any gaps in the producers knowledge about the capabilities of the API will become apparent. This provides the opportunity to discover the missing information, potentially before the APIs production release.

## Measuring Performance

Various elements of the Open SLA specification encourage detailing, in a numeric, non-subjective manner, the performance capabilities of an API.

It is not the intention of the Open SLA Specification to prescribe how these figures must be measured.

Defining such values in an Open SLA will, however, allow the runtime performance of an API to be measured over time, either by the client or at intermediate infrastructure such as an API Gateway.

## Contributing to the standard

If you feel that there is something missing, a logic flaw or simply a spelling mistake please open an Issue in order to get this addressed.
