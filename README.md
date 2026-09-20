# primitives-ts

Isomorphic infrastructure abstractions for TypeScript — the sibling of `primitives-go`.

Each package exposes a stable interface with swappable providers selected by config. Most
packages are **isomorphic**: the same import resolves to the right implementation whether
it runs on Node or in the browser, so call-site code (e.g. logging) is copy-paste portable
between a script and a page.

**Scope: the browser and Node scripts.** No service is built in TypeScript — `platform-go`
is the only server tier there is — so this module carries nothing that exists to run beside
a database, a broker or a secret manager. Code that needs to *talk* to a service built on
`platform-go` wants `platform-client-ts`, not this.

## Packages

Every package is exactly one of two modalities (see `CLAUDE.md`): **universal** (pure
logic, one build) or **isomorphic** (same import resolves per-environment).

### Universal

| Package                          | Purpose                                                                 |
| -------------------------------- | ----------------------------------------------------------------------- |
| `@primandproper/errors`          | Message extraction, prefixed wrapping, and a typed `PlatformError` base |
| `@primandproper/retry`           | Retry policies (exponential backoff + jitter)                           |
| `@primandproper/numbers`         | Number utilities (rounding, scaling, yield math)                        |
| `@primandproper/bitmask`         | Immutable bigint-backed bitmask over unsigned integers                  |
| `@primandproper/identifiers`     | Unique ID generation + validation (nanoid random, ulid sortable)        |
| `@primandproper/fake`            | Seeded test-data generation (thin `@faker-js/faker` wrapper)            |
| `@primandproper/encoding`        | `Encoder`/`ServerEncoderDecoder` over JSON, YAML, XML, TOML             |
| `@primandproper/circuitbreaking` | Circuit breakers (noop + partitioned)                                   |
| `@primandproper/version`         | Build-time version and VCS metadata                                     |
| `@primandproper/qrcodes`         | QR code generation, for TOTP setup flows                                |

### Isomorphic

| Package                        | Purpose                                                                   |
| ------------------------------ | ------------------------------------------------------------------------- |
| `@primandproper/observability` | `Logger` (pino on Node, console in browser) + OTel tracer/meter aliases   |
| `@primandproper/cache`         | `Cache<T>` (memory/redis on Node, memory/web-storage in browser)          |
| `@primandproper/cryptography`  | `Encryptor` + `Hasher` over WebCrypto                                     |
| `@primandproper/random`        | Cryptographically secure random (hex, base32, base64url) over WebCrypto   |
| `@primandproper/compression`   | `Compressor` interface with swappable providers                           |
| `@primandproper/cookies`       | `CookieStore` interface with swappable providers                          |
| `@primandproper/httpclient`    | Thin `fetch` wrapper with OpenTelemetry spans                             |
| `@primandproper/ratelimiting`  | `RateLimiter` interface with swappable providers                          |
| `@primandproper/eventstream`   | `EventStream` over SSE and WebSocket                                      |
| `@primandproper/analytics`     | `EventReporter` interface with swappable providers                        |
| `@primandproper/eventcapture`  | Non-blocking high-volume event capture draining to a swappable sink       |
| `@primandproper/featureflags`  | `FeatureFlagManager` with typed evaluation, OpenFeature-backed            |

## Parity with primitives-go

`primitives-go` is the source of truth for behaviour, but **not for scope**: parity is
deliberately partial. A package lands here when something in the browser or a Node script
needs it, and packages that only make sense beside server infrastructure are absent on
purpose rather than pending.

## Development

```bash
pnpm install
pnpm build && pnpm typecheck && pnpm test && pnpm lint
```

See `CLAUDE.md` for the package-modality rules and house style.
