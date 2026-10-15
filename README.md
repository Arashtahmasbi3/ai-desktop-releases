# AI Desktop — release feed

This repository holds **only** the release binaries for AI Desktop and their checksums. It
exists so the app's built-in updater can read a release feed **without a token** (a private
repository answers 404 to an unauthenticated check).

The application source lives in a separate, private repository.

## What is in a release

| Asset | Purpose |
| --- | --- |
| `AI Desktop Setup <version>.exe` | NSIS installer. This is what the updater downloads and verifies. |
| `AI Desktop <version> Portable.exe` | Portable build. It cannot update itself. |
| `SHA256SUMS.txt` | SHA-256 of each asset. **The updater verifies the downloaded installer against this before running it**, and refuses the update on a mismatch. |

## How the updater works

1. `GET https://api.github.com/repos/Arashtahmasbi3/ai-desktop-releases/releases/latest`
2. Compare `tag_name` with the running version (numeric, so `3.10 > 3.9`).
3. Download the installer named `…Setup…exe` to the temp directory.
4. Fetch `SHA256SUMS.txt` from the same release and check the digest. A mismatch deletes the
   file and aborts.
5. Only then start the installer and quit the app so its files can be replaced.

Nothing is downloaded or executed without the user clicking, and a release that publishes no
checksum is reported as *unverified* rather than silently trusted.

## Verifying a download by hand

```powershell
Get-FileHash "AI Desktop Setup <version>.exe" -Algorithm SHA256
Get-Content SHA256SUMS.txt
```

The two must match. The Windows binaries are currently **unsigned**, so SmartScreen may warn on
first run — the checksum is the integrity guarantee, not a code signature.
