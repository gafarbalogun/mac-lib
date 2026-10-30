# mac-lib

A single command for managing and auditing every package manager on your Mac — Homebrew, pipx, pip3, npm (global), gem, cargo, and mas — plus a built-in vulnerability scanner.

Instead of remembering which manager owns which package and running `brew outdated`, `pipx upgrade-all`, `npm update -g`, etc. separately, `mac-lib` routes everything through one interface and includes guardrails for protected macOS system paths.

## Reusable for

- **Package inventory:** identify packages and which manager owns them using `mac-lib list` and `mac-lib which <package>`.
- **Routine maintenance:** check outdated packages with `mac-lib outdated`; upgrade individual packages after reviewing changes.
- **Local vulnerability review:** run `mac-lib scan-quick`, `mac-lib scan`, or `mac-lib scan-path <directory>` and save reports for comparison.

This is a **living utility**, not a frozen or universally tested release. Its value is in these repeatable tasks, and the commands can evolve.

## Try it from the repository

On macOS with Bash and Git:

```bash
git clone https://github.com/gafarbalogun/mac-lib.git
cd mac-lib
bash ./mac-lib version
bash ./mac-lib doctor
bash ./mac-lib list
```

Review the script before installing optional scanning dependencies or running commands that change packages. `bash ./mac-lib setup` installs supported scanners through Homebrew; `scan-quick` and full scans need the relevant tools installed.

For an audit of a chosen folder after setup:

```bash
bash ./mac-lib scan-path ~/Applications
```

**Boundaries:** results depend on installed package managers, scanner databases, filesystem access, and the macOS version. The protected-path checks are safeguards, not a promise that arbitrary package upgrades or removals are risk-free. Review changes and keep backups; this is not a replacement for endpoint security management.

## Features

- **Unified package management** — list, check outdated, upgrade, or remove packages across brew (formula + cask), pipx, pip3, npm, gem, cargo, and mas without knowing which manager owns them.
- **Vulnerability scanning** — wraps [grype](https://github.com/anchore/grype) and [pip-audit](https://github.com/pypa/pip-audit) to scan `/Applications`, `$HOME`, `/opt/homebrew`, and `/usr/local` for known CVEs, with a fast package-manager-only mode and a full-disk deep scan.
- **Safe by default** — hardcoded protected paths (`/usr/bin`, `/bin`, `/System`, `/Library/Apple`, etc.) that `mac-lib rm` will always refuse to touch, even if a package manager thinks it owns something there.
- **Self-healing setup** — `mac-lib doctor` reports what's installed, `mac-lib repair` auto-fixes missing scanners, broken pipx installs, and PATH issues.
- **Readable reports** — every scan is saved as a timestamped report under `~/.mac-lib/reports/`, with severity badges, CVSS scores, and fix versions shown inline.

## Install

```bash
curl -o /usr/local/bin/mac-lib https://raw.githubusercontent.com/gafarbalogun/mac-lib/main/mac-lib
chmod +x /usr/local/bin/mac-lib
mac-lib setup
```

`mac-lib setup` installs the underlying scanners (`grype`, `syft`, `trivy`, `osv-scanner`, `pip-audit`) via Homebrew — it's a one-time step.

## Usage

```
mac-lib setup                  Install scanners (grype, trivy, syft, osv-scanner)
                                and any optional helpers via brew. One-time.
mac-lib repair                 Auto-fix common issues: missing scanners, broken
                                pipx, PATH problems. Safe to run anytime.
mac-lib doctor                 Check which underlying tools are installed.

mac-lib scan                   Scan everywhere that matters (default).
                                Hits /Applications, $HOME, /opt/homebrew, /usr/local
                                with smart exclusions (caches, /System, etc).
mac-lib scan-deep              Scan / (root) minus SIP-protected paths.
                                Slow, may need Full Disk Access in System Settings.
mac-lib scan-quick             Skip filesystem; just query package managers.
mac-lib scan-path <dir>        Scan a specific path.

mac-lib list                   List installed packages across all managers.
mac-lib outdated               Show what's outdated everywhere.
mac-lib upgrade [pkg]          Upgrade one package, or all if no pkg given.
mac-lib rm <pkg>               Remove a package (refuses system binaries).
mac-lib which <pkg>            Show which manager owns <pkg>.
```

Scan reports are saved to `~/.mac-lib/reports/` with timestamps.

## Known managers

brew (formula + cask), pipx, pip3, npm (global), gem, cargo, mas, MacPorts (`port`), conda (base env), Yarn (global), pnpm (global)

## Requirements

- macOS
- Bash
- [Homebrew](https://brew.sh) (used to install the vulnerability scanners)
- Python 3 (for formatted scan output; falls back gracefully without it)

## Writeup

[I let Claude write my code — the work that mattered was somewhere else](https://medium.com/@gafar.cloud/i-let-claude-write-my-code-the-work-that-mattered-was-somewhere-else-d53750d4359f)

## License

MIT
