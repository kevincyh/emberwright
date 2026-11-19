# Security

## Secrets

- Never commit keys, keystores, certificates, `.env` files or service-account JSON. `.gitignore` blocks the common ones.
- CI secrets live in GitHub **Settings → Secrets and variables → Actions**. See [docs/CI_CD.md](docs/CI_CD.md).
- `OPENAI_API_KEY` for the impressions tools is read from your shell environment, never from a file in the repo.
- opencode is configured to deny agents from reading keystores and `.env` files.

## Game security model

- The host is authoritative for the shared world. It treats every client message as untrusted. See [docs/ARCHITECTURE.md](docs/ARCHITECTURE.md#host-authority).
- Saves are local, so a player can edit their own save. This only affects that player. Checksums catch corruption and casual edits.
- Discovery broadcasts only on the local network. The host shows its IP address so joining players can check it.

## Reporting

To report a vulnerability, open a private security advisory in this repository. Do not open a public issue.
