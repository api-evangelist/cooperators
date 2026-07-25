---
name: Quote and bind Duuo event insurance
description: Embed Duuo event insurance in a partner application — create the entity, price the event, show the quote, and take the customer to the Duuo-hosted payment page where the policy is bound and emailed.
api: openapi/cooperators-duuo-platform-openapi.yml
provider: cooperators
generated: '2026-07-25'
method: generated
source: openapi/cooperators-duuo-platform-openapi.yml
operations:
  - createEventEntity
  - createEventQuote
  - getEventQuote
  - createEventQuotePayment
  - createEventPolicy
  - emailEventPolicy
---

# Quote and bind Duuo event insurance

Duuo (The Co-operators' embedded insurance brand) sells single-day and multi-day event
liability insurance. This skill covers the whole partner flow.

## Before you start

- **Access is not self-serve.** You need a signed partnership agreement, a Partner Account
  Manager, an issued client id/secret, and a partner-issued base URL. There is no public host.
- **Get a token first.** `POST {"grant_type":"client_credentials"}` to the partner-issued token
  URL with `Authorization: Basic ` + Base64(`client_id:client_secret`). Send the returned token as
  `Authorization: Bearer {access_token}` on every call. It expires after 60 minutes — refresh it.
  Expiry shows up as `401 Access forbidden` or the non-standard `440 Login timeout`.
- **The order is mandatory.** Duuo states that failure to follow the order below will error.

## Steps

1. **`createEventEntity`** — `POST /api/v1/event/entity`. Send `firstName`, `lastName`, `email`
   (required) plus optional `name`, `province`, `phone`, `language`. Use identity data you already
   hold; do not re-ask. If the customer already has a Duuo account they are moved forward.

2. **`createEventQuote`** — `POST /api/v1/event/quote`. Link it with `entityEmail`. Required:
   `eventTypeLabel`, `dayFactor`, `eventAlcoholServed`, `numberAttendees`, `eventInsurance` (days),
   `eventProvince`, `address[]`.
   - `eventTypeLabel` comes from two closed Duuo lists — non-sporting (Wedding & Reception, Dance,
     Convention, Graduation, …) and sporting (Baseball, Curling, Golf, Soccer, …). Render a dropdown.
   - `numberAttendees` bands differ between sporting and non-sporting events. Pick the right band set.
   - `singleEvent` is 1–7 days; `multipleEvent` is 2–75 days. Round days up.
   - Alcohol rules: an event of 25 days or longer cannot be insured with alcohol served, and no
     sporting event can be insured with alcohol served.
   - **Compliance, non-negotiable:** the liquor-licence text must sit under the alcohol question,
     and Duuo's excluded-activities list must be shown and explicitly acknowledged ("I understand")
     before you move on.
   - Response returns `quoteId`, `premium`, `salesTax`, `totalCost` in CAD.

3. **`getEventQuote`** — `GET /api/v1/event/quote/{quoteId}`. Retrieve the quote for display.
   **Compliance:** the screen must show the quoted price, the event details the customer supplied
   (`eventTypeLabel`, address, start/end, attendees, alcohol), the three coverage limits, a link to
   the full policy wording, and the misrepresentation disclaimer. In the PAT (sandbox) environment
   the price is a test value only — never show a PAT price to a real customer.

4. **`createEventQuotePayment`** — `POST /api/v1/event/quote/{quoteId}/payment` with
   `bindOnPayment`, `generalConsent`, `emailConsent`. All three must be `true` (the consents must be
   `true` or the call fails). Collect both consents with Duuo's verbatim check-box text — the terms
   hyperlink on `generalConsent` is mandatory — before you redirect. The response carries a `url`
   for the Duuo-hosted payment page. Open it in a new tab and display Duuo's post-redirection text
   on the original page. **This step moves money and, with `bindOnPayment: true`, binds and emails
   the policy. There is no idempotency key — never blind-retry it.**

5. **Only if `bindOnPayment` was `false`** (rare; flag it to your Account Manager in advance):
   - **`createEventPolicy`** — `POST /api/v1/event/policy` with `quoteId`. Returns `policyId`
     (internal, for process mapping) and `policyNumber` (customer-facing). Never show `policyId`
     to a customer.
   - **`emailEventPolicy`** — `PUT /api/v1/event/policy/{policyId}/email` to send the confirmation
     of insurance.

## Reading responses

Event responses use the envelope `data.resolved.response` with the code at
`data.resolved.responseCode`. Success carries `"response": "success"`.

## Errors

`401` access forbidden → refresh the token. `440` login timeout → refresh the token. `500` carries
an operation-specific meaning (error creating profile / creating quote / getting quote / getting
pre-quote / retrieving quote / creating policy / sending email). See
`errors/cooperators-problem-types.yml`.

## Hard rules

- Any coverage question must be redirected to a licensed insurance representative
  (`info@duuo.ca`). Do not answer it yourself.
- Duuo's logo and the attribution line ("Duuo insurance products are exclusively underwritten by
  Co-operators General Insurance Company, and distributed by Duuo Insurance Services Inc.") must
  appear on every screen where coverage is discussed, along with the Duuo Customer Support link.
- The area where the customer answers Duuo's questions must be visually differentiated from the
  rest of your page.
- Your Partner Account Manager must review the implementation before go-live. Non-compliance can
  terminate API access.
