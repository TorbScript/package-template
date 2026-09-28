# torb/hello

A one-function [TorbScript](https://torb.dev) library, kept just large enough to show what a published package looks
like: a manifest, one public function with a doc comment, a test, an example, and the two CI workflows a real package
needs. Fork it and turn it into your own.

## Using this template

1. **Fork or copy this repository** under your own owner - on
   [GitHub](https://github.com/TorbScript/package-template) with the "Use this template" button, or on
   [git.torb.dev](https://git.torb.dev/torb/package-template) by forking it there.
2. **Rename the package.** In `project.trb`, change `name = "torb/hello"` to `name = "<your-owner>/<your-package>"`.
   `owner` is the account or organisation your registry package belongs to, not your source forge's username unless
   they happen to match.
3. **Rewrite `src/lib.trb`.** `greeting` is the one thing this package exports; replace it (and `tests/` and
   `examples/`) with your own. Keep the doc comment on every `public` declaration - `torb doc` and the registry's
   package page are built from it.
4. **Update `description`, `license` and this README.** `torb publish` refuses a package without a `description` and
   a `license`.
5. **Replace the copyright line of `LICENSE`** with your own name and the current year - it still says `YEAR YOUR
   NAME` until you do.
6. **Set up trusted publishing** on the registry before you push a tag - see below.

## Developing

```console
$ torb format --check .    # the layout torb format writes
$ torb check .              # types, exhaustiveness, everything torb check finds
$ torb test                 # tests/greeting.test.trb
$ torb doc --check .         # every doc comment link resolves, every example runs
$ torb run examples/greet.trb
```

`torb format .` writes the layout instead of only checking it. `.github/workflows/ci.yml` and
`.forgejo/workflows/ci.yml` run the same four checks on every push and pull request.

## Publishing

`torb publish` needs `name`, `version`, `description` and `license` in `project.trb`, and it refuses a version that is
published already - a published version is never replaced, so publishing again means raising `version` first.

**Try it without uploading anything:**

```console
$ torb publish --dry-run
```

**Publishing by hand** needs a token of the registry: create one with the `publish` action and this package in its
scope, then `TORB_TOKEN=<token> torb publish`.

**Publishing from CI (this template's `publish.yml`) needs no token at all - trusted publishing.** Before the first
tag, an owner of the package's account on the registry (packages.torb.dev) has to add a trusted publisher for the
package that names:

| Setting | GitHub Actions (`.github/workflows/publish.yml`) | Forgejo Actions (`.forgejo/workflows/publish.yml`) |
|---|---|---|
| Repository | `<your-owner>/<your-repo>` | `git.torb.dev/<your-owner>/<your-repo>` |
| Workflow file | `publish.yml` | `publish.yml` |
| Ref | `refs/tags/v*` | `refs/tags/v*` |
| Environment | `release` (or delete the `environment:` line of the workflow to name none) | none - a Forgejo token never names one |

Once that is configured, pushing a tag `v0.1.0` (matching the `version` in `project.trb`) runs `publish.yml`, which
installs `torb` and runs `torb publish` with an identity token instead of a secret.

## Files

```text
package-template/
├─ project.trb              name, version, description, license
├─ src/
│  └─ lib.trb                the library: "torb/hello" resolves to this file
├─ tests/
│  └─ greeting.test.trb      torb test finds *.test.trb anywhere below the package
├─ examples/
│  └─ greet.trb               torb run examples/greet.trb
├─ .github/workflows/         ci.yml (push, pull request), publish.yml (tag v*)
├─ .forgejo/workflows/        the same, for git.torb.dev
├─ LICENSE                    MIT - replace the copyright line
└─ .gitignore
```

## Related

- [docs/design/PROJECT.md](https://git.torb.dev/torbscript/language/src/branch/main/docs/design/PROJECT.md) - what a
  package produces and what `project.trb` says.
- [torb publish](https://torb.dev/docs/tooling/torb-publish) - what publishing checks, and trusted publishing in full.
- [tools/registry/README.md](https://git.torb.dev/torbscript/language/src/branch/main/tools/registry/README.md) - the
  registry's API, including trusted publishers.
