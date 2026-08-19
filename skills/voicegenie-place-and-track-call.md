---
name: Place a VoiceGenie call and track its outcome
description: Place an outbound voice call to a customer inside a campaign, then retrieve the call's analysis or status.
api: openapi/_original/voicegenie-openapi.yml
operations: [placeCall, callAnalysisOrStatus, checkCallTransferStatus, pullCustomerFromCampaign]
---

# Place a VoiceGenie call and track its outcome

Use the VoiceGenie Public REST API to place a call to a customer within an existing campaign and follow up on the result. Base URL: `https://core-saas.voicegenie.ai`.

## Auth
Every request carries credentials in the JSON body (not headers):
- `apiKey` — Developer API key (dashboard: System -> APIs).
- `workspaceId` — your workspace id (constant even if the token is regenerated).

## Steps

1. **Place the call** — `placeCall` (`POST /api/v1/publicRestApiActions/placeCall`).
   Body: `apiKey`, `workspaceId`, `campaignId`, `customerNumber` (E.164, e.g. `+1234567890`),
   optional `customerInformation` (string key/values merged as dynamic variables, e.g. first_name, email).

2. **Check the result** — `callAnalysisOrStatus` (`POST /api/v1/publicRestApiActions/callAnalysisOrStatus`).
   Body adds `action`: use `"status"` to poll call state, or `"analysis"` for the transcript once complete.

3. **If the call was transferred to a human** — `checkCallTransferStatus`
   (`POST /api/v1/publicRestApiActions/checkCallTransferStatus`) with `apiKey`, `workspaceId`, `campaignId`
   to confirm the transfer is pending / completed / failed.

4. **Stop contacting a customer** — `pullCustomerFromCampaign`
   (`PUT /api/v1/publicRestApiActions/pullCustomerFromCampaign`) with `customerNumber` once they have
   converted or opted out.

## Rules
- `customerNumber` MUST be E.164: leading `+` country code, no spaces, hyphens or brackets.
- No idempotency key is supported — calling `placeCall` twice places two calls. De-duplicate on your side.
- Placing a call is a real-world action (a live phone call to a person); confirm the number and consent first.
- Errors return client-side `4xx`; see `errors/voicegenie-problem-types.yml`.
- Post-call data can also arrive via the outbound post-call webhook (`asyncapi/voicegenie-webhooks.yml`).
