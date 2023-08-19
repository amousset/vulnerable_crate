# vulnerable_crate

This crate is intended for testing purposes only, and uses vulnerable dependencies on purpose.

## Testing

To check the current output in the tooling using advisories from RustSec (or imported from RustSec at some point),
read the following steps.

It provides a library and a binary to allow for testing the binary files too.

### cargo-deny

```shell
cargo install cargo-deny
# Ignore other checks
cargo deny check advisories
```

### cargo-audit

```shell
cargo install cargo-audit
cargo audit
```

