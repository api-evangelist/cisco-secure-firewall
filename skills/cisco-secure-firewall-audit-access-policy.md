---
name: cisco-secure-firewall-audit-access-policy
description: >-
  Walk every Cisco Secure Firewall access control policy and its rules to answer questions like "which rule
  permits this address", "what is our default action", or "which rules log". Read-only.
api: cisco-secure-firewall:cdfmc
generated: '2026-08-19'
method: generated
source: openapi/cisco-secure-firewall-cdfmc-openapi.yml
operations:
- getAllDomain
- getAllAccessPolicy
- getAccessPolicy
- getAllAccessRule
- getAccessRule
- getAllPolicyAssignment
- getAllNetworkObject
- getAllHostObject
- getAllNetworkGroup
---

# Audit a Secure Firewall access control policy

This is the safe, read-only counterpart to deployment. Every operation here is a `GET`.

## Steps

1. **Get the domain.** `getAllDomain` — `GET /v1/cdfmc/api/fmc_platform/v1/info/domain`. Everything below
   is scoped to a `domainUUID`.

2. **List policies.** `getAllAccessPolicy` —
   `GET /v1/cdfmc/api/fmc_config/v1/domain/{domainUUID}/policy/accesspolicies`.

3. **Read the policy header.** `getAccessPolicy` — `GET .../accesspolicies/{objectId}`. This is where
   `defaultAction`, `identityPolicySetting`, `prefilterPolicySetting`, `decryptionPolicySetting` and
   `securityIntelligence` live. The default action is the answer to "what happens to traffic no rule
   matches" and it is on the policy, not the rules.

4. **List the rules, in order.** `getAllAccessRule` —
   `GET .../accesspolicies/{containerUUID}/accessrules`. **Rule order is the semantics.** The first match
   wins, so a list returned out of order answers the question wrongly. Preserve the order the API returns.

5. **Expand a rule.** `getAccessRule` — `GET .../accesspolicies/{containerUUID}/accessrules/{objectId}`.
   `AccessRule` carries 28 references: `sourceNetworks`, `destinationNetworks`, `sourcePorts`,
   `destinationPorts`, `sourceZones`, `destinationZones`, `sourceSecurityGroupTags`,
   `destinationSecurityGroupTags`, `applications`, `urls`, `users`, `vlanTags`, `ipsPolicy`, `filePolicy`,
   `syslogConfig`, `snmpConfig` and more.

6. **Resolve object references.** Rule members are references, not literals. Resolve them with
   `getAllNetworkObject`, `getAllHostObject` and `getAllNetworkGroup` under
   `/domain/{domainUUID}/object/...` before answering an address question. A rule that names a group tells
   you nothing until the group is expanded.

7. **Find which devices this policy governs.** `getAllPolicyAssignment` —
   `GET /v1/cdfmc/api/fmc_config/v1/domain/{domainUUID}/assignment/policyassignments`.

## Rules

- Do not answer "is X allowed" from a single rule. Answer it from ordered rule evaluation plus the policy
  default action, and say which rule matched and at what position.
- Paging is FMC-native. Follow it to the end before concluding a rule does not exist — a partial page is
  the most common source of a wrong "no rule found".
- This skill is read-only. Any change to a rule must go through the deploy skill and a human.
