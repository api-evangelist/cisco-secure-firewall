---
name: cisco-secure-firewall-upgrade-ftd
description: >-
  Find which software versions a Cisco Secure Firewall Threat Defense device can move to, trigger the
  upgrade for one device or up to 50 at once, and track it to completion.
api: cisco-secure-firewall:scc-firewall-manager
generated: '2026-08-19'
method: generated
source: openapi/cisco-secure-firewall-scc-firewall-manager-openapi.yml
operations:
- getDevices
- getCompatibleFtdVersions
- getCompatibleFtdVersionsForMultipleFtds
- upgradeFtdDevice
- upgradeFtdDevices
- updateFtdUpgradePackagesCache
- getTransaction
- getAsaUpgradeVersions
- upgradeAsaDevice
---

# Upgrade Secure Firewall Threat Defense devices

## Steps

1. **Identify the devices.** `getDevices` with `q=deviceType:FTD`. Record each `uid`.

2. **Ask what each device can upgrade TO.** Never assume a target version.
   - One device: `getCompatibleFtdVersions` —
     `GET /v1/inventory/devices/ftds/{deviceUid}/upgrades/versions`.
   - Many devices: `getCompatibleFtdVersionsForMultipleFtds` —
     `GET /v1/inventory/devices/ftds/upgrades/versions`.
   If the compatible-version list looks stale, `updateFtdUpgradePackagesCache`
   (`PUT /v1/inventory/devices/ftds/upgrades/packages/build-cache`) rebuilds the tenant's package cache.

3. **Trigger the upgrade.**
   - One device: `upgradeFtdDevice` — `POST /v1/inventory/devices/ftds/{deviceUid}/upgrades/trigger`.
   - Up to 50 devices: `upgradeFtdDevices` — `POST /v1/inventory/devices/ftds/upgrades/trigger`.
     The 50-device ceiling was raised from 10 in API version 1.13.0 (2025-05-23).

4. **Track it.** The trigger is asynchronous. Poll `getTransaction` —
   `GET /v1/transactions/{transactionUid}` — until it reports completion. Upgrades take a long time; poll
   on an interval, do not busy-loop.

5. **ASA is a separate path.** For ASA devices use `getAsaUpgradeVersions` then `upgradeAsaDevice`.

## Rules

- **This is a disruptive, fleet-affecting operation.** A firewall upgrade takes the device out of service.
  Get explicit human authorization for the exact device list before step 3.
- **Never retry a trigger blind.** There is no idempotency key. If step 3 times out, poll the transaction
  to learn whether it started before deciding anything.
- Upgrading and deploying are different operations. Staged configuration changes are pushed with
  `createDeploymentRequest` (see the deploy skill), not with an upgrade trigger.
- Verify the target version came from step 2's response. Do not construct a version string.
