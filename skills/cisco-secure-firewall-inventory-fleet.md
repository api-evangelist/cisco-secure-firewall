---
name: cisco-secure-firewall-inventory-fleet
description: >-
  Inventory every firewall, device manager and cloud service registered with Cisco Security Cloud Control,
  including health, connectivity and configuration-sync state. Read-only.
api: cisco-secure-firewall:scc-firewall-manager
generated: '2026-08-19'
method: generated
source: openapi/cisco-secure-firewall-scc-firewall-manager-openapi.yml
operations:
- getDevices
- getDevice
- getDeviceManagers
- getDeviceManager
- getFmcHealth
- getCloudServices
- getTemplateDevices
- getAsaHealthMetrics
- getAsaInterfaceHealthMetrics
- search
- getChangelogs
---

# Inventory the Secure Firewall fleet

## Steps

1. **List devices.** `getDevices` — `GET /v1/inventory/devices`. Paged with `limit` and `offset`; the
   envelope is `{count, limit, offset, items}`.

2. **Filter with Lucene.** The `q` parameter takes Lucene syntax: `q=deviceType:FTD`,
   `q=name:*edge*`, `q=connectivityState:ONLINE`. Searchable fields are **not published** — if you guess
   wrong the API returns HTTP 400 *with the list of searchable fields for that endpoint*. Use that 400 as
   discovery rather than treating it as a failure.

3. **Read the state fields that matter.** On each `Device`: `connectivityState` (is it reachable),
   `configState` (`SYNCED` / `NOT_SYNCED` — undeployed changes), `conflictDetectionState` (out-of-band
   changes made outside Security Cloud Control), `deviceType`, `softwareVersion`, `labels`.

4. **List the managers.** `getDeviceManagers` — `GET /v1/inventory/managers`. Use
   `q=deviceType:CDFMC` to find the cloud-delivered FMC; its `uid` and domain are what every cdFMC call
   needs. `getFmcHealth` (`GET /v1/inventory/managers/{fmcUid}/health/metrics`) returns FMC health
   including S2S VPN tunnel and RA VPN session data.

5. **Cross into configuration.** A `Device` carries `deviceRecordOnFmc` and `fmcAccessPolicy` — those are
   the bridge from this fleet model into the cdFMC configuration model. Follow them rather than trying to
   match devices by name across the two APIs.

6. **Search across everything.** `search` — `GET /v1/search` runs a tenant-wide query across devices,
   objects, policies and templates when you do not know which resource holds the answer.

7. **Explain a change.** `getChangelogs` — `GET /v1/changelogs` is a detailed history of every change made
   to the tenant, also `q`-searchable.

## Rules

- Everything here is a `GET`. Nothing in this skill changes state.
- Always page to the end before reporting a count. `count` in the envelope is the total; `items` is one page.
- Report `configState: NOT_SYNCED` explicitly — it means someone staged a change that has not been
  deployed, which is a live finding, not a detail.
