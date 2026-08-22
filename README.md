# ENTSO-E (entso-e)

<!-- API-EVANGELIST-PROVENANCE:BEGIN -->
> ### About this repository
>
> **This is not our API.** This repository is an independent, third-party profile of a company's
> **publicly available** API surface, maintained by [API Evangelist](https://apievangelist.com).
> API Evangelist does not operate, host, resell, or support this company's APIs, and is not
> affiliated with or endorsed by the company unless stated on the profile.
>
> **Where the information came from.** Everything here is assembled from material a member of the
> public can reach with a browser and no credentials — the company's own website, developer portal
> and documentation, the specifications it publishes for public use (OpenAPI, AsyncAPI, JSON Schema,
> `apis.json`, `llms.txt` and similar), its public repositories, and its public status, pricing and
> changelog pages. **Nothing here is obtained by breaching a system, defeating an access control, or
> using credentials of any kind.**
>
> **The rating is an independent assessment.** The Kin Score and Agent Readiness rating are
> independently calculated scores of a company's *public* API artifacts, produced by API Evangelist
> against a published rubric. They are not certifications, endorsements, security assessments, or
> audits, and they score published artifacts — not the quality, safety, or security of the software.
>
> **Corrections, re-scores, and removal are free.** No partnership, contract, or purchase is
> required, and you do not need to justify the request.
>
> - **Something wrong?** Open an issue on this repository, or email
>   [info@apievangelist.com](mailto:info@apievangelist.com).
> - **Published something new?** Ask for a re-score and we will re-run the rating.
> - **Want the listing taken down?** Say so and we will honor it. The profile is reduced to your
>   company name, a factual description, and a link to your own site, and the company is recorded as
>   **unrated** — never scored zero for having asked.
>
> **Response times.** Acknowledgement within **one business day**; removal or restriction within
> **two business days**; corrections and re-scores within **five business days**.
>
> **Not from the company, and here with a question?** You are welcome here — we would rather be the
> front line and point you the right way than have a good report go nowhere. What this repository
> can answer is narrow, though, so it is worth knowing who you are actually looking for:
>
> - **A question about how the API works, an account, billing, or a bug in the service** — that is
>   the company's own support, not us. We profile this API; we do not operate it and cannot see
>   your account.
> - **A bug in an open-source project we only catalog** — file it on that project's own repository.
>   This has happened with a real and correct bug report that reached us instead of the people who
>   could fix it, which helped nobody.
> - **Anything about this listing itself** — the description, the tags, the rating, a missing or
>   wrong artifact — is ours. Open an issue here.
> - **Not sure, or something general about API Evangelist or APIs.io** — open an issue on the
>   [APIs.io Inbox](https://github.com/api-search/inbox) and we will route it.
>
> **This repository contains no software, and we will never ask you to download anything.** There is
> no build, release, installer, or binary here — only text and machine-readable API descriptions, so
> there is nothing here that can be "corrupt" or need "repairing". Any issue, comment, or email
> claiming otherwise and offering a download link is not from us and is hostile. Do not follow the
> link; it is a lure. Report it to GitHub and, if you like, tell us at
> [info@apievangelist.com](mailto:info@apievangelist.com) so we can take it down.
>
> **On a security or compliance team?** Email
> [info@apievangelist.com](mailto:info@apievangelist.com) with *security* in the subject line and
> you will get a person, not a form. We will tell you exactly which public URLs this profile was
> built from so your team can see the same surface we did, and we will take the listing down on
> request while you work through it.
>
> Full detail: **[Where this data comes from](https://apievangelist.com/about/where-our-data-comes-from)**
<!-- API-EVANGELIST-PROVENANCE:END -->

ENTSO-E, the European Network of Transmission System Operators for Electricity, operates the Transparency Platform - the central publication point for pan-European electricity market data under EU Regulation 543/2013. Its free RESTful API returns day-ahead prices, system load, generation, balancing, and cross-border transmission data for every European bidding zone and control area as IEC 62325 XML market documents, selected by coded documentType and processType parameters against a single endpoint.

**APIs.json:** [https://raw.githubusercontent.com/api-evangelist/entso-e/refs/heads/main/apis.yml](https://raw.githubusercontent.com/api-evangelist/entso-e/refs/heads/main/apis.yml)

## Access Model

The Transparency Platform web API is free, but gated behind registration: create an account on [transparency.entsoe.eu](https://transparency.entsoe.eu/), email transparency@entsoe.eu with "Restful API access" in the subject line, and once granted, generate a personal token under My Account Settings. The token travels as the `securityToken` query parameter on GET requests (or the `SECURITY_TOKEN` header on POST). There are no paid tiers - the platform is a regulatory publication mandated by EU Regulation 543/2013 - but requests are limited to 400 per minute per IP address / token (a 10-minute ban follows), standard data items are capped at a one-year range per request, and document-based items at 100 documents per request (200 for outages, pageable via `offset` up to 4,900).

## Interface Model

This is deliberately not a resource-per-path REST API. Everything is one endpoint - `https://web-api.tp.entsoe.eu/api` - and the data item is selected by coded query parameters drawn from the IEC 62325 / ENTSO-E EDI code lists: `documentType` (A44 = day-ahead price document, A65 = system total load, A75 = actual generation per type, A85 = imbalance prices, A11 = physical flows, ...), `processType` (A01 = day ahead, A16 = realised, ...), `psrType` (production types B01-B24), `businessType`, and domain parameters (`in_Domain`, `out_Domain`, `outBiddingZone_Domain`, `controlArea_Domain`) populated with EIC area codes such as `10YCZ-CEPS-----N`. Time windows are `periodStart`/`periodEnd` in `yyyyMMddHHmm` UTC. Responses are XML market documents per the IEC 62325-451 standards - `Publication_MarketDocument` (prices, flows, capacities), `GL_MarketDocument` (load, generation), `Balancing_MarketDocument` - with a few items (imbalance prices/volumes, outages) delivered as ZIP archives of XML. Rejections and empty results come back as an `Acknowledgement_MarketDocument` (Reason code 999 = no matching data). The OpenAPI in this repository models that single-endpoint, parameter-driven contract honestly rather than inventing REST paths.

## Tags

- Electricity
- Energy
- Energy Markets
- Day-Ahead Prices
- Balancing
- Transmission
- Grid Data
- Europe

## Timestamps

- **Created:** 2026-07-11
- **Modified:** 2026-07-11

## APIs

### ENTSO-E Day-Ahead Prices API

Wholesale day-ahead electricity prices (Transparency Regulation article 12.1.D) for every European bidding zone, per market time unit in currency per MWh - the reference price behind consumer kWh rates and dynamic tariffs. Query with documentType=A44 and the bidding zone EIC code in both in_Domain and out_Domain; up to one year per request, returned as a Publication_MarketDocument.

- **Human URL:** [https://documenter.getpostman.com/view/7009892/2s93JtP3F6](https://documenter.getpostman.com/view/7009892/2s93JtP3F6)
- **Base URL:** `https://web-api.tp.entsoe.eu/api`

#### Tags

- Day-Ahead Prices
- Electricity Prices
- Spot Prices
- Energy Markets
- kWh Rates

#### Properties

- [Documentation](https://transparencyplatform.zendesk.com/hc/en-us)
- [API Reference](https://documenter.getpostman.com/view/7009892/2s93JtP3F6)
- [OpenAPI](openapi/entso-e-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/entso-e.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/entso-e.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

### ENTSO-E Load API

Actual system total load and day-ahead, week-ahead, month-ahead, and year-ahead load forecasts per bidding zone (articles 6.1.A-6.1.E). Query with documentType=A65, processType A16 (realised) or A01/A31/A32/A33 (forecasts), and outBiddingZone_Domain; returned as a GL_MarketDocument per market time unit.

- **Human URL:** [https://documenter.getpostman.com/view/7009892/2s93JtP3F6](https://documenter.getpostman.com/view/7009892/2s93JtP3F6)
- **Base URL:** `https://web-api.tp.entsoe.eu/api`

#### Tags

- Electricity Load
- Demand
- Forecasts
- Consumption

#### Properties

- [Documentation](https://transparencyplatform.zendesk.com/hc/en-us)
- [API Reference](https://documenter.getpostman.com/view/7009892/2s93JtP3F6)
- [OpenAPI](openapi/entso-e-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/entso-e.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/entso-e.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

### ENTSO-E Generation API

Actual generation per production type (documentType=A75, processType=A16) and per generation unit (A73), day-ahead generation forecasts (A71), wind and solar forecasts (A69), installed capacity per type (A68) and per unit, and water reservoir filling levels (A72) - filterable by psrType across production types from nuclear (B14) to solar (B16) and onshore wind (B19).

- **Human URL:** [https://documenter.getpostman.com/view/7009892/2s93JtP3F6](https://documenter.getpostman.com/view/7009892/2s93JtP3F6)
- **Base URL:** `https://web-api.tp.entsoe.eu/api`

#### Tags

- Electricity Generation
- Renewables
- Wind
- Solar
- Installed Capacity

#### Properties

- [Documentation](https://transparencyplatform.zendesk.com/hc/en-us)
- [API Reference](https://documenter.getpostman.com/view/7009892/2s93JtP3F6)
- [OpenAPI](openapi/entso-e-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/entso-e.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/entso-e.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

### ENTSO-E Balancing API

Balancing market data per control area (articles 17.1.B-17.1.J) - contracted balancing reserves and their prices (documentType A81/A89), accepted aggregated offers (A82), activated balancing energy and prices (A83/A84), imbalance prices (A85) and total imbalance volumes (A86, both delivered as ZIP of XML), cross-border balancing (A88), and procured balancing capacity (A15) - filterable by reserve type (FCR, aFRR, mFRR, RR) via businessType and processType.

- **Human URL:** [https://documenter.getpostman.com/view/7009892/2s93JtP3F6](https://documenter.getpostman.com/view/7009892/2s93JtP3F6)
- **Base URL:** `https://web-api.tp.entsoe.eu/api`

#### Tags

- Balancing
- Balancing Data
- Imbalance Prices
- Reserves
- Grid Frequency

#### Properties

- [Documentation](https://transparencyplatform.zendesk.com/hc/en-us)
- [API Reference](https://documenter.getpostman.com/view/7009892/2s93JtP3F6)
- [OpenAPI](openapi/entso-e-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/entso-e.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/entso-e.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

### ENTSO-E Transmission API

Cross-border physical flows between areas (documentType=A11, one direction per request), forecasted transfer capacities at daily through yearly horizons (A61 with contract_MarketAgreement.Type A01-A04), day-ahead commercial schedules (A09), offered and allocated capacity, and net positions - keyed by in_Domain/out_Domain EIC border pairs.

- **Human URL:** [https://documenter.getpostman.com/view/7009892/2s93JtP3F6](https://documenter.getpostman.com/view/7009892/2s93JtP3F6)
- **Base URL:** `https://web-api.tp.entsoe.eu/api`

#### Tags

- Transmission
- Cross-Border Flows
- Transfer Capacity
- Interconnectors

#### Properties

- [Documentation](https://transparencyplatform.zendesk.com/hc/en-us)
- [API Reference](https://documenter.getpostman.com/view/7009892/2s93JtP3F6)
- [OpenAPI](openapi/entso-e-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/entso-e.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/entso-e.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

## Common Properties

- [LinkedIn](https://www.linkedin.com/company/entso-e)
- [Website](https://www.entsoe.eu/)
- [Portal](https://transparency.entsoe.eu/)
- [Documentation](https://transparencyplatform.zendesk.com/hc/en-us)
- [Plans](plans/entso-e-plans-pricing.yml)
- [Rate Limits](rate-limits/entso-e-rate-limits.yml)
- [Fin Ops](finops/entso-e-finops.yml)
- [Blog](https://www.entsoe.eu/news/)

## Maintainers

**FN:** Kin Lane
**Email:** kin@apievangelist.com
