---
---

Scope the module to the browser and Node scripts: removed the 13 server-tier packages
(`authentication`, `authorization`, `database`, `distributedlock`, `email`, `healthcheck`,
`idempotency`, `llm`, `messagequeue`, `notifications`, `search`, `secrets`, `uploads`).

Release-neutral for everything that remains — no kept package imported a dropped one, so no
surviving package's code or public surface changed. The dropped packages simply stop being
published; consumers of a previously published version keep it.

Pending changesets for `@primandproper/authorization`, `@primandproper/database` and
`@primandproper/idempotency` were removed along with their packages.
