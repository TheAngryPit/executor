# One Google account requirement across supported products

Draft proposal for [#2492](https://github.com/UsefulSoftwareCo/executor/issues/2492).
This document proposes adopting an app-level composition already exercised in a
private installed copy. It does not change the official app, hosted OAuth policy
or any account selection.

## User outcome

A person using Gmail, Calendar, Drive and other supported Google products in one
app should connect each Google identity through one account requirement. Choosing
another product should not introduce another provider slot for that same identity.
Different Google identities must remain distinct and explicitly selectable.

The tested composition retains the original Google product tools and mounts
Apps Script, BigQuery, Search Console, Ads and YouTube against the same selected
Google accounts. Original tool schemas, product instructions and native mutation
approvals are reused. Unsupported optional provider requirements are omitted.

## Proposed integration

Keep the original Google name and icon, one `google` account requirement, and the
original product modules. Route each additional supported product through the
same selected-account binding, with its own tool namespace. Derive the consent
scope union and permitted credential hosts from the products actually included.
An imported OpenAPI document must not add consent or trusted hosts by itself.

The tested candidate requests 32 scopes using the hosted Google client. Current
`v2` at `04f358c973790d5479374d6b97a50b79022a4c88` already allows the relevant
additional scopes in
[first-party-oauth.ts](../apps/hosted/server/src/implementation/first-party-oauth.ts).
This proposal does not ask to expand that allowlist again.

Changing a provider definition can change its identity. Keep old account records
and credentials intact, identify incompatible app links, and ask the person to
authorize the changed requirement. Do not substitute another identity or assume
that an old grant contains new scopes. In the exercised copy, stale app links
needed explicit cleanup before tool discovery recovered.

## Evidence and limits

The installed candidate used `apps@0.0.1-beta.117`. These results concern that
candidate, not a runtime implementation in this documentation-only PR:

| Check | Result | Boundary |
| --- | --- | --- |
| Strict TypeScript compilation | Passed | Candidate app source only |
| Focused local regressions | 80 passed; 917 assertions | Synthetic tests, not repository E2E scenarios |
| SDK evaluation | One requirement, five extension mounts, selected-account credential binding, foreign-account rejection before transport, account-free inspection | Synthetic account and transport |
| Live read-only calls across four reauthorized accounts | 22 of 24 passed | Six operations per account, not exhaustive product coverage |

Gmail profile, Apps Script processes, Search Console sites, YouTube channels and
BigQuery projects passed for all four accounts. Ads customer discovery passed for
two and returned HTTP 401 for two; the cause remains unresolved. The final test
pass sent no emails and made no Google writes. Rendered UI inspection and full
GOG command/flag parity are not established by these checks.

## Contribution target and related work

[app-catalog.ts](../apps/hosted/cloud/src/infrastructure/app-catalog.ts) identifies
`executor-apps` as the repository publishing the official app feed. That repository
was not accessible through the contributor's current GitHub account. Please
confirm where the app-source delta should be submitted; this draft keeps that
decision visible rather than introducing a second app implementation into the host.

The public checkout also lacks the `notes/coding-style.md` and
`notes/project-orientation.md` referenced by its agent instructions. No host
implementation is proposed without that guidance.

[#2480](https://github.com/UsefulSoftwareCo/executor/pull/2480) concerns verified
identity grouping and incremental consent; this app composition should cooperate
with that work. [#2460](https://github.com/UsefulSoftwareCo/executor/pull/2460)
proposes Admin scopes, a separate policy change. The companion catalog proposal
is tracked by [integrations#102](https://github.com/UsefulSoftwareCo/integrations/issues/102).

Accepting this proposal would establish the app-source destination and review
boundary. An implementation contribution would still need the original authors'
credit, reproducible checks, explicit account migration behavior and appropriate
live authorization proof. Remaining unsupported Google services and GOG workflows
belong in a separate follow-up, not in this initial composition.
