# The Co-operators (cooperators)

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
> **On a security or compliance team?** Email
> [info@apievangelist.com](mailto:info@apievangelist.com) with *security* in the subject line and
> you will get a person, not a form. We will tell you exactly which public URLs this profile was
> built from so your team can see the same surface we did, and we will take the listing down on
> request while you work through it.
>
> Full detail: **[Where this data comes from](https://apievangelist.com/about/where-our-data-comes-from)**
<!-- API-EVANGELIST-PROVENANCE:END -->

The Co-operators is a Canadian insurance and financial services co-operative, formed in 1978 from the amalgamation of the Saskatchewan and Ontario co-operative insurers whose roots run back to the Co-operative Life Insurance Company founded in Regina in 1945. It is owned by Canadian co-operatives, credit union centrals and farm organizations rather than public shareholders, and writes multi-line property and casualty, home, auto, farm, travel and life insurance alongside group benefits, group retirement and savings, wealth management and institutional asset management — more than $62 billion in assets under administration, nearly 7,000 employees and more than 2,800 licensed insurance representatives across Canada.

**APIs.json:** [https://raw.githubusercontent.com/api-evangelist/cooperators/refs/heads/main/apis.yml](https://raw.githubusercontent.com/api-evangelist/cooperators/refs/heads/main/apis.yml)

## API Posture

There is no first-party developer portal. Every candidate developer subdomain on `cooperators.ca` — `developer`, `developers`, `docs`, `api`, `apis`, `partners` — fails to resolve in DNS, and `/developers`, `/developer`, `/api`, `/partners` and `/integrations` all return HTTP 404. The advisor and broker channel is a set of sign-in walls (`lifeportal.cooperators.ca`, `benefitsnowlogon.cooperators.ca`, `basis.cooperators.ca`, `illustration.cumis.com`), and that sign-in page names no API, no developer documentation and no data-exchange standard.

The only API surface in the group belongs to **Duuo**, the embedded insurance brand owned by The Co-operators. Duuo's partner APIs cover account, quote, payment and policy issuance for tenant and event insurance, but access is not self-serve: partners engage the partnerships team, sign a partnership agreement, are assigned a Partner Account Manager, and are then issued OAuth 2.0 client credentials and a partner-specific host. The developer portal Duuo still advertises at `https://developer.duuo.ca/` is a Postman-hosted documentation domain that returns **HTTP 404** as of 2026-07-25 — the collection it published has been unpublished, and no live public API reference remains.

**ACORD posture:** no ACORD reference found. Searches across Co-operators and Duuo public material for ACORD, AL3, ACORD XML, NGDS, IVANS and CSIO returned zero matches, and The Co-operators does not appear in the public [CSIO member directory](https://csio.com/membership/member-directory) — CSIO being Canada's P&C data-standards body and the domestic counterpart to ACORD.

**Quote / bind / issue / FNOL:** quote and bind/issue are covered, partner-only, for Duuo tenant and event insurance. No claims or FNOL API is published anywhere in the group.

No OpenAPI, Swagger, AsyncAPI, GraphQL, gRPC or live public Postman artifact is published, so this repo has no `openapi/` directory.

## Tags

- Insurance
- Canada
- Property and Casualty
- Life Insurance
- Group Benefits
- Embedded Insurance
- Co-operative
- Wealth Management
- Partner API

## Timestamps

- **Created:** 2026-07-25
- **Modified:** 2026-07-25

## APIs

### Duuo Platform API

Partner-gated embedded insurance API from Duuo, the digital insurance brand of The Co-operators, covering account, quote and policy, risk rating and payment so partners can embed tenant and event insurance into their own applications. Authentication is OAuth 2.0 `client_credentials` against a partner-issued token URL.

- **Human URL:** [https://duuo.ca/embedded-partners/](https://duuo.ca/embedded-partners/)
- **Base URL:** not published (partner-issued host)

#### Tags

- Embedded Insurance
- Quote
- Policy
- Tenant Insurance
- Event Insurance
- Partner API

#### Properties

- [Documentation](https://duuo.ca/embedded-partners/)
- [Website](https://duuo.ca/)
- [Sign Up](https://duuo.ca/new-partners/)

## Common Properties

- [Website](https://www.cooperators.ca/)
- [About](https://www.cooperators.ca/en/about-us/corporate-overview)
- [Sign In](https://www.cooperators.ca/en/advisors/sign-in)
- [Investor Relations](https://www.cooperators.ca/en/about-us/corporate-overview/investor-relations)
- [Security and Privacy](https://www.cooperators.ca/en/security-privacy)
- [LinkedIn](https://www.linkedin.com/company/co-operators)

## Review

See [review.yml](review.yml) for the full API Evangelist reviewer findings — every probed URL with its HTTP status, the ACORD/CSIO search result, the auth model, and the archived record of the now-unpublished Duuo developer portal.

## Maintainers

- Kin Lane — kin@apievangelist.com
