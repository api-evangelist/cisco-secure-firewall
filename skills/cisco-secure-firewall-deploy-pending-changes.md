---
name: cisco-secure-firewall-deploy-pending-changes
description: >-
  Find which Cisco Secure Firewall threat-defense devices have staged configuration changes, review those
  changes, deploy them, and confirm the deployment finished — including how to roll back.
api: cisco-secure-firewall:cdfmc
generated: '2026-08-19'
method: generated
source: openapi/cisco-secure-firewall-cdfmc-openapi.yml, openapi/cisco-secure-firewall-scc-firewall-manager-openapi.yml
operations:
- getDeployableDevice
- getPendingChanges
- createDeploymentRequest
- getAllJobHistory
- getJobHistory
- createRollbackRequest
- getTransaction
---

# Deploy pending changes to Secure Firewall devices

Configuration written through the cdFMC API is **staged, not live**. Nothing takes effect on a firewall
until it is deployed. This is the most consequential flow in the API and the one where a careless retry
does real damage.

## Before you start

- `Authorization: Bearer $API_TOKEN` on every request. The token must carry `ROLE_DEPLOY_ONLY` or higher —
  `ROLE_EDIT_ONLY` can stage changes but **cannot** deploy them.
- Base URL is regional: `https://api.us.security.cisco.com/firewall` (or eu / apj / au / in).
- You need the `domainUUID`. Get it from `getAllDomain`
  (`GET /v1/cdfmc/api/fmc_platform/v1/info/domain`) — `Global` is the default domain on most tenants.

## Steps

1. **See what is deployable.** `getDeployableDevice` —
   `GET /v1/cdfmc/api/fmc_config/v1/domain/{domainUUID}/deployment/deployabledevices`.
   Only devices listed here have staged changes. An empty list means there is nothing to do; stop.

2. **Read the changes before pushing them.** `getPendingChanges` —
   `GET /v1/cdfmc/api/fmc_config/v1/domain/{domainUUID}/deployment/deployabledevices/{containerUUID}/pendingchanges`.
   Do this per device. Deploying without reading the diff is how someone else's staged change ships under
   your name — the cdFMC stages changes from every editor on the tenant, not just yours.

3. **Deploy.** `createDeploymentRequest` —
   `POST /v1/cdfmc/api/fmc_config/v1/domain/{domainUUID}/deployment/deploymentrequests`, with the device
   list and version. This is an **action-verb POST**, so it is asynchronous.

4. **Confirm it finished.** Poll `getAllJobHistory` / `getJobHistory` on the cdFMC surface, or
   `getTransaction` (`GET /v1/transactions/{transactionUid}`) on the Firewall Manager surface. A 202 is
   an acknowledgement, not a result.

5. **Roll back if it went wrong.** `createRollbackRequest` —
   `POST /v1/cdfmc/api/fmc_config/v1/domain/{domainUUID}/deployment/rollbackrequests`.

## Rules

- **Never blind-retry step 3.** There is no idempotency key anywhere in this API. If a deployment request
  times out, poll job history or the transaction to find out whether it landed — do not resend it.
- Rate limits are undocumented and there is no `Retry-After`. On a 429 (`TOO_MANY_REQUESTS`), back off
  exponentially from a conservative starting point.
- Handle **two** error shapes: the documented `{errorCode, errorMsg, details}` and the edge gateway's
  `{timestamp, path, status, error, requestId}`. Log `requestId` — it is the only correlation id Cisco
  gives you, and it is in the body, not a header.
- Deploying is a fleet-affecting write. Ask a human before step 3 unless you have been explicitly
  authorized for this device set.
