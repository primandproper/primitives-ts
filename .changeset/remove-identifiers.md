---
---

Remove `@primandproper/identifiers`.

A client does not issue the server's identifiers: the server mints them, the client receives
opaque strings, and validating an ID the server just sent proves nothing. The package
generated 21-character nanoids, which `primitives-go`'s `Validate` rejects — so an ID
generated here and sent to a `platform-go` service was refused at the door.

Release-neutral for everything that remains: nothing imported it (its only consumer,
`idempotency`, was removed earlier), so no surviving package's code or public surface
changed. `nanoid` and `ulid` leave the dependency tree with it.
