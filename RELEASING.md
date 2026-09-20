# Releasing

**`main` is not a release channel.** Nothing publishes when a PR merges. A release happens
when you push a `v*` tag, and only then — the same policy `primitives-go`, `primitives-kt`
and `primitives-swift` follow, because Go modules, JitPack and SwiftPM resolve tags and
nothing else. npm does not work that way on its own, so this repo imposes it.

There is no bot. Nothing opens a PR at you, and nothing decides on your behalf what ships
or when.

## Cutting a release

1. **Decide what ships.** `pnpm changeset status` lists any changesets sitting in
   `.changeset/`. They are optional — a PR is not required to declare one — so this is a
   starting point, not the whole answer. `git log --oneline <last-tag>..main` is the
   other half.

2. **Set the versions.** Either consume the changesets:

   ```bash
   pnpm changeset version     # applies changesets, bumps package.json, writes CHANGELOG.md
   ```

   …or edit the `version` field in the packages you mean to ship and write their
   `CHANGELOG.md` entries yourself. Both are fine; the tag is what matters, not how the
   number got there.

3. **Commit, tag, push.**

   ```bash
   git commit -am "release: <what shipped>"
   git tag -a v0.2.0 -m "<release notes>"
   git push --follow-tags
   ```

4. **CI publishes.** `.github/workflows/release.yml` lints, typechecks, tests, builds, and
   runs `changeset publish`, which pushes **only** the packages whose version is not
   already on the registry. A tag that fails the checks publishes nothing — fix it on
   `main` and cut another tag.

5. **Write the GitHub release.** `gh release create v0.2.0 --notes-file notes.md`, or from
   the tag annotation with `--notes-from-tag`. This is deliberately not automated: the
   notes are the part worth a human (or an LLM with the diff in front of it), and a
   generated changelog is not release notes.

## Notes

- Versions are per package, not per repo. The `v*` tag marks _a release event_; which
  packages moved is recorded in their `package.json` and `CHANGELOG.md`.
- `changeset publish` runs with `--no-git-tag`: you cut the one tag that means something
  rather than collecting a per-package tag for every publish.
- Auth is npm **Trusted Publishing** (OIDC) — no `NPM_TOKEN` secret, and provenance is
  automatic. Each package needs a trusted publisher configured on npm (Settings → Trusted
  publisher: this repo + `release.yml`), which can only be set after that package's first
  publish. Until then the baseline goes out via `scripts/first-publish.sh`.
