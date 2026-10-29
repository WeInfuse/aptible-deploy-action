# AGENTS.md

Guidance for AI coding agents. This is WeInfuse's fork of
`aptible/aptible-deploy-action`; usage docs are in `README.md`.

## Releases

- Releases are plain tags (`v1`, `v2`, ... `v5`), created by hand on the merge
  commit. No semver. `.github/workflows/versioning.yml` only fires on
  `vX.Y.Z` tags, so it doesn't apply.
- Consumers pin the commit SHA with a `# vN` comment (e.g.
  `WeInfuse/aptible-deploy-action@<sha> # v5`). A new release means bumping
  that SHA in each consumer's deploy workflows (`weinfuse_api`,
  `weinfuse-frontend`, `rxtoolkit`, `infusetrack`, ...).

## Aptible CLI

- The Go CLI version is pinned in the `Dockerfile` (`CLI_FILE`). Bump it
  deliberately; don't go back to `latest`.
- The Go CLI records a failed "2FA not supplied" login on Aptible for every
  login, and repeated failures lock the account.

## Shared deploy user

The `weinfuse_api` and `weinfuse-frontend` production deploys log in as the
same Aptible user. Two logins in the same 30s TOTP window reuse a code and
one is rejected, so retries wait for the next window (`otp_retry_delay`).

## Testing

- On Apple Silicon build with `--platform linux/amd64`; the CLI package is
  amd64-only.
- Test with a fake seed and unreachable `INPUT_AUTH_ROOT_URL` /
  `INPUT_API_ROOT_URL`. Never use real Aptible credentials locally - failed
  logins lock the shared deploy user.
