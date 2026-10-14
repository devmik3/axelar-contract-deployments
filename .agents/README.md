# Orb startup

`setup` installs Node 24 (matching CI), Rust from `rust-toolchain.toml` with
rustfmt and clippy, native build/USB libraries, and both locked npm dependency
trees. It fetches the locked Solana Rust dependencies and builds the root
TypeScript package. Tool paths persist in repository-scoped login shells.

Amp snapshots this environment for reuse. When setup runs on a stale snapshot,
it reuses npm dependencies if the manifests, lockfiles, npm configuration, and
Node/npm versions match. Remove `node_modules/.amp-install-key` (or the equivalent
under `axelar-chains-config`) to force that package's reinstall. `resume` performs
no installs; there are no default services or credentials to restore.

Verification commands:

```sh
.agents/setup
.agents/resume
npm test
npm --prefix axelar-chains-config test -- --run --threads false
```

Setup does not create wallets, write `.env`, deploy contracts, or start networks.
Sui/Stellar/Solana network tools and validators remain task-specific; see the
chain documentation and CI workflows. Rust dependencies are cached, not compiled.

The optional `npm run build --prefix axelar-chains-config` currently fails when
root dependencies are installed: its TypeScript configuration picks up incompatible
`@types/glob`/`minimatch` declarations from the parent dependency tree. It is not
part of startup; the package's JavaScript entry point and five tests work without
that declaration build. Dependency audit findings are reported, not automatically
fixed or suppressed.

The lifecycle files must reach the project's default branch before future orbs
can use them. No project settings or snapshot deletion is needed for normal updates.
