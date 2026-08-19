# Cisco Secure Firewall

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

Cisco Secure Firewall is the product line built on the Sourcefire technology Cisco acquired in 2013 — Firepower/Secure Firewall appliances and Threat Defense (FTD) software, the Secure Firewall Management Center (FMC), the on-box device manager (FDM), and the cloud-delivered Firewall Management Center (cdFMC) operated under Cisco Security Cloud Control. Two first-party REST contracts are published: the cdFMC API (1,311 operations) and the Security Cloud Control Firewall Manager API (160 operations), both OpenAPI 3.0.1 and both committed by Cisco to github.com/CiscoDevNet/scc-public-api-docs. Automation is further supported by a Python SDK, an Ansible collection, a Terraform provider, Postman collections, an always-on DevNet sandbox, and community MCP servers.

## Ownership

Part of the Cisco family (acquired 2013).

## Contract status

**Corrected 2026-08-19.** An earlier round of this profile recorded "no central anonymously fetchable
specification." That was wrong and has been retracted. Cisco publishes both cloud contracts first-party, in
the open, in the repository that backs developer.cisco.com:

- [`cdo/cdfmc-openapi.yaml`](https://github.com/CiscoDevNet/scc-public-api-docs/blob/main/cdo/cdfmc-openapi.yaml)
  — Cloud-delivered Firewall Management Center API, OpenAPI 3.0.1, 706 paths, **1,311 operations**, 1,510 schemas.
- [`cdo/openapi.yaml`](https://github.com/CiscoDevNet/scc-public-api-docs/blob/main/cdo/openapi.yaml)
  — Security Cloud Control Firewall Manager API, OpenAPI 3.0.1, 119 paths, **160 operations**.

Both are harvested verbatim into `openapi/_original/` with an `x-provenance` block recording where they came
from. The on-premises Secure Firewall Management Center still serves its own OpenAPI per appliance at
`/api/api-explorer`, and that copy remains customer-served — but the same contract surface is public.

## Verified links

- [Portal](https://developer.cisco.com/docs/security-cloud-control/)
- [Documentation](https://developer.cisco.com/docs/security-cloud-control/)
- [APIReference](https://developer.cisco.com/docs/security-cloud-control/)
- [ParentCompany](https://apis.io/providers/cisco/)
- [Ansible](https://github.com/CiscoDevNet/FMCAnsible)
- [MCPServer](https://github.com/CiscoDevNet/CiscoFMC-MCP-server-community)
- [Portal](https://developer.cisco.com/)

All URLs above returned HTTP 200 when probed on 2026-08-19.
