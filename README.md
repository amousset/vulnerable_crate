# vulnerable_crate

This crate is intended for testing purposes only, and uses vulnerable dependencies on purpose.

## Testing

To check the current output in the tooling using advisories from RustSec (or imported from RustSec at some point),
read the following steps.

It provides a library and a binary to allow for testing the binary files too.

## Source audit

### cargo-deny

```shell
cargo install cargo-deny
# Ignore other checks
cargo deny check advisories
```

### cargo-audit

```shell
$ cargo install cargo-audit
$ cargo audit
❯ cargo audit
    Fetching advisory database from `https://github.com/RustSec/advisory-db.git`
      Loaded 559 security advisories (from /home/amousset/.cargo/advisory-db)
    Updating crates.io index
    Scanning Cargo.lock for vulnerabilities (87 crate dependencies)
Crate:     evm
Version:   0.35.0
Title:     evm incorrect state transition
Date:      2022-10-25
ID:        RUSTSEC-2022-0083
URL:       https://rustsec.org/advisories/RUSTSEC-2022-0083
Severity:  7.5 (high)
Solution:  Upgrade to >=0.36.0
Dependency tree:
evm 0.35.0
└── vulnerable_crate 0.1.0

Crate:     inconceivable
Version:   0.9.0
Warning:   notice
Title:     Library exclusively intended to inject UB into safe Rust.
Date:      2022-09-28
ID:        RUSTSEC-2022-0058
URL:       https://rustsec.org/advisories/RUSTSEC-2022-0058
Dependency tree:
inconceivable 0.9.0
└── vulnerable_crate 0.1.0

Crate:     users
Version:   0.11.0
Warning:   unmaintained
Title:     `users` crate is unmaintained
Date:      2023-06-01
ID:        RUSTSEC-2023-0040
URL:       https://rustsec.org/advisories/RUSTSEC-2023-0040
Dependency tree:
users 0.11.0
└── vulnerable_crate 0.1.0

Crate:     lmdb-rs
Version:   0.7.6
Warning:   unsound
Title:     impl `FromMdbValue` for bool is unsound
Date:      2023-06-26
ID:        RUSTSEC-2023-0047
URL:       https://rustsec.org/advisories/RUSTSEC-2023-0047
Dependency tree:
lmdb-rs 0.7.6
└── vulnerable_crate 0.1.0

error: 1 vulnerability found!
warning: 3 allowed warnings found
```

Note: This is the default behavior, users can pass a `--deny` flag to consider some warnings as errors.

### osv-scanner

OSV scanner uses osv.dev and finds advisories from both GitHub and RustSec databases.

```shell
$ osv-scanner .
Scanning dir .
Scanning /home/amousset/projects/vulnerable_crate/ at commit 00d2c0647947737aa8303dc98d4ea22295e593ec
Scanned /home/amousset/projects/vulnerable_crate/Cargo.lock file and found 87 packages
╭─────────────────────────────────────┬──────┬───────────┬───────────────┬─────────┬────────────╮
│ OSV URL                             │ CVSS │ ECOSYSTEM │ PACKAGE       │ VERSION │ SOURCE     │
├─────────────────────────────────────┼──────┼───────────┼───────────────┼─────────┼────────────┤
│ https://osv.dev/GHSA-hhc4-47rh-cr34 │ 5.9, │ crates.io │ evm           │ 0.35.0  │ Cargo.lock │
│ https://osv.dev/RUSTSEC-2022-0083   │ 7.5  │           │               │         │            │
│ https://osv.dev/RUSTSEC-2022-0058   │      │ crates.io │ inconceivable │ 0.9.0   │ Cargo.lock │
│ https://osv.dev/GHSA-f9g6-fp84-fv92 │      │ crates.io │ lmdb-rs       │ 0.7.6   │ Cargo.lock │
│ https://osv.dev/RUSTSEC-2023-0047   │      │           │               │         │            │
│ https://osv.dev/RUSTSEC-2023-0040   │      │ crates.io │ users         │ 0.11.0  │ Cargo.lock │
╰─────────────────────────────────────┴──────┴───────────┴───────────────┴─────────┴────────────╯

```

### GitHub Advisories / Dependabot / etc.

It looks like:

* Non-informational advisories are imported
  * e.g.: https://github.com/advisories/GHSA-hhc4-47rh-cr34
* `informational = "unsound"` advisories are imported
  * e.g.: https://github.com/advisories/GHSA-f9g6-fp84-fv92
* `informational = "unmaintaine" / "notice"` are not imported, hence not reported as vulnerabilities

## Binary audit

Build with:

```shell
cargo install cargo-auditable
cargo auditable build --release
```

### cargo-audit

```asciidoc
cargo audit bin target/release/vulnerable_crate
```

Same output as when run on the sources.

### osv-scanner

### trivy