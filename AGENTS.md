## Cursor Cloud specific instructions

This is the **Rust compiler (rustc)** source repository. The build system is a custom Python/Rust bootstrap driven by `x.py` (or the `./x` shell wrapper).

### Build configuration

- `bootstrap.toml` in the repo root controls the build. The compiler dev profile (from `src/bootstrap/defaults/bootstrap.compiler.toml`) is pre-configured with `llvm.download-ci-llvm = true` to avoid building LLVM from source.
- If `bootstrap.toml` is missing, copy the compiler profile: `cp src/bootstrap/defaults/bootstrap.compiler.toml bootstrap.toml` and add `change-id = 148671` at the top.

### Key commands

| Task | Command |
|---|---|
| Full build (stage1 compiler + std + rustdoc) | `./x.py build` |
| Fast type-check (no codegen) | `./x.py check` |
| Lint / tidy | `./x.py test tidy` |
| Run UI tests (subset) | `./x.py test tests/ui/<subdir>` |
| Run all compiler tests | `./x.py test compiler` |
| Run standard library tests | `./x.py test library` |
| Build docs | `./x.py doc` |

### Gotchas

- **C++ headers required**: The build needs `libstdc++-14-dev` (or equivalent) installed. Without it, the `libcxx-version` check fails with `'iostream' file not found`.
- **Stage1 compiler location**: After `./x.py build`, the built compiler is at `build/host/stage1/bin/rustc`.
- **Incremental rebuilds**: The compiler profile enables incremental compilation. After code changes, `./x.py build` only recompiles affected crates. Use `./x.py check` for even faster feedback (type-checking only).
- **`x.py test tidy`**: Runs formatting and style checks. This is the primary lint command. It does not require a full build.
- **Test filtering**: Use `./x.py test tests/ui/<subdir>` to run a subset of UI tests. The full UI test suite (`tests/ui/`) has 20,000+ tests and takes significant time.
- **`--no-doc`**: Append `--no-doc` to test commands to skip doc tests and speed up test runs.
- **System Rust vs built Rust**: The system `rustc` (from rustup) is used by bootstrap to compile the build system itself. The stage1 compiler at `build/host/stage1/bin/rustc` is the one you are developing.
