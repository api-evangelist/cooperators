---
name: Quote and bind Duuo tenant insurance
description: Embed Duuo tenant (renters) insurance in a partner application — check eligibility with consent, price the risk into four options, add insureds, and take the customer to the Duuo-hosted payment page where the policy binds and the certificate is emailed.
api: openapi/cooperators-duuo-platform-openapi.yml
provider: cooperators
generated: '2026-07-25'
method: generated
source: openapi/cooperators-duuo-platform-openapi.yml
operations:
  - checkTenantEligibility
  - updateTenantQuote
  - addTenantQuoteInsured
  - createTenantQuotePayment
---

# Quote and bind Duuo tenant insurance

Duuo (The Co-operators' embedded insurance brand) sells tenant/renters insurance in two tiers
(standard and enhanced), each monthly or annual. This skill covers the whole partner flow.

## Before you start

- **Access is not self-serve.** Signed partnership agreement, Partner Account Manager, issued
  client id/secret, partner-issued base URL. There is no public host.
- **Get a token first.** `POST {"grant_type":"client_credentials"}` to the partner-issued token URL
  with `Authorization: Basic ` + Base64(`client_id:client_secret`), then send
  `Authorization: Bearer {access_token}` on every call. 60-minute lifetime.
- **The order is mandatory.** Out-of-order calls error.
- **Two ids.** `checkTenantEligibility` returns both a customer-facing `quoteId` (`TQ-…`) and a
  `quoteSubmissionId`. Every later path parameter uses `quoteSubmissionId`.

## Steps

1. **`checkTenantEligibility`** — `POST /api/v1/tenant/eligibility` with `personalInfoConsent`,
   `creditScoreConsent`, `contactInfoEmail`, `insuredAddressPostalCode`, optionally
   `insuredAddressProvince` (AB, BC, MB, NB, NS, ON, PE, SK) and `languageLocal`.
   - **This runs a soft credit check on a real consumer.** Both consents must be `true`, each
     captured on its own check box, each with Duuo's verbatim disclosure text. Duuo requires the
     personal-information consent text to be shown in full and to not share a screen with other
     fields, with your company name substituted into the `[PARTNER NAME]` placeholders and your own
     privacy policy linked.
   - Postal code must be `LNL NLN` (e.g. `A1A 1A1`).
   - On success you get `quoteId`, `quoteSubmissionId`, `quoteStartDate`, `quoteExpirationDate`,
     `statusReferenceId`.

2. **`updateTenantQuote`** — `PUT /api/v1/tenant/quote/{quoteSubmissionId}`. Adds the full risk
   detail and prices it.
   - Three separate addresses are modelled: the insured address, the policy holder's current
     address, and the mailing address. Pre-populate whatever you already hold.
   - Risk flags: `claimsPriorHistory`, `monitoredSecuritySystem`, `monitoredFireDetectionSystem`,
     `monitoredWaterLeakDetectionSystem`.
   - `policyEffectiveDate` is `YYYY-MM-DD`, from tomorrow up to 30 days out — **never today**.
   - Closed value sets: `personalContentCoverageLimit` 10,000 / 25,000 / 50,000 / 75,000 /
     100,000 / 150,000; `liabilityCoverageLimit` 1,000,000 / 2,000,000; `deductibleLimit` 1,000 /
     2,000 / 3,000 / 5,000.
   - Returns six price fields. `heChargedPremTotalStandard` and `heChargedPremTotalPremium` are the
     pre-tax figures; the `…Yearly` and `…Monthly` variants include tax. Tax = the `…Yearly` value
     minus the base (or the `…Monthly` value minus base/12).
   - **Compliance:** show all four options, restate the key details (name, email, phone,
     `policyEffectiveDate`, the three limits), link the standard and enhanced policy wordings, and
     show the misrepresentation disclaimer. PAT prices are test values only.

3. **`addTenantQuoteInsured`** — `PUT /api/v1/tenant/quote/{quoteSubmissionId}/addinsured` with
   `packageSelection` (`premium`|`standard`), `paymentFrequency` (`monthly`|`annually`),
   `paymentMethod` (`credit`), `languageLocal`, and `thirdPartySharingConsent: true`.
   Optionally a landlord (name + email) and up to three additional insureds — these are flat
   positional fields (`first…`, `second…`, `third…`), not an array.
   **Compliance:** collect `thirdPartySharingConsent` with Duuo's verbatim check-box text before
   the payment redirect.

4. **`createTenantQuotePayment`** — `PUT /api/v1/tenant/quote/{quoteSubmissionId}/payment` with
   `bindOnPayment` and `paymentAndTermsOfUseConsent`. The call only succeeds when
   `personalInfoConsent`, `creditScoreConsent`, `thirdPartySharingConsent` and
   `paymentAndTermsOfUseConsent` are **all** true. Show Duuo's "I accept the Terms of Use and
   Payment Agreement" check box first. The response carries a `url` — open it in a new tab, show
   Duuo's post-redirection text on the original page, and switch it to a confirmation once payment
   succeeds. On success the policy binds and the certificate of insurance is emailed automatically.
   **This step moves money and binds an insurance contract. There is no idempotency key — never
   blind-retry it.**

## Reading responses

Tenant responses use `data.resolved.message` on success with the code at `data.resolved.status`,
and `data.resolved.responseDetails` on failure. Every response carries a `statusReferenceId` — log
it; it is the only correlation id this API gives you.

## Errors

- `400` — `responseDetails` is an **array** of `{field, message, statusReferenceId}`. Missing or
  false consent booleans are the most common cause.
- `403` — access denied, requires access management. Call your Partner Account Manager.
- `404` — **plain text** `Module not found`, not JSON. Type-check before parsing.
- `422` — unprocessable entity (documented on `updateTenantQuote`).

See `errors/cooperators-problem-types.yml`.

## Not available

There is no claims/FNOL operation, no policy read, and no quote-status read. Duuo documented
"Get Quote Status" and "Get Policies" as coming later; neither shipped publicly. Do not plan
reconciliation around them.

## Hard rules

- Coverage questions go to a licensed insurance representative (`info@duuo.ca`), never to you.
- Duuo logo + attribution line + customer-support link on every screen where coverage is discussed.
- Partner Account Manager review before go-live; non-compliance can terminate API access.
