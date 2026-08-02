---
name: Feed and control a VoiceGenie campaign
description: Add calls to a recurring campaign, pause/resume it, and sync inbound-call updates.
api: openapi/voicegenie-openapi.yml
operations: [pushCallToCampaign, editCampaign, inboundCallUpdate]
---

# Feed and control a VoiceGenie campaign

Automate an ongoing VoiceGenie campaign from a CRM or workflow tool. Base URL: `https://core-saas.voicegenie.ai`.

## Auth
Credentials go in the JSON body. Note the two credential field names:
- `token` — used by `pushCallToCampaign`.
- `apiKey` — used by the `publicRestApiActions/*` endpoints.
- `workspaceId` — always required.

## Steps

1. **Queue a call into a recurring campaign** — `pushCallToCampaign`
   (`POST /api/v1/pushCallToCampaign`). Body: `token`, `workspaceId`, `campaignId`,
   `customerNumber` (E.164), optional `customerInformation` (e.g. first_name, last_name).
   Trigger this when a new lead appears (e.g. a new Google Sheet / CRM row).

2. **Pause or resume the campaign** — `editCampaign` (`PUT /api/v1/publicRestApiActions/editCampaign`)
   with `apiKey`, `workspaceId`, `campaignId`, and `action` = `pause` or `resume`
   (e.g. pause outside working hours, resume after approval).

3. **Sync inbound calls** — `inboundCallUpdate` (`POST /api/v1/publicRestApiActions/inboundCallUpdate`)
   with `apiKey`, `workspaceId`, `campaignId` to pull recent inbound-call updates into your system.

## Rules
- `customerNumber` MUST be E.164 (no spaces/hyphens/brackets).
- All required fields must be present strings, or the request returns `400`.
- No pagination is documented; reconcile by `campaignId` + `customerNumber`.
- See `conventions/voicegenie-conventions.yml` for cross-cutting request/response rules.
