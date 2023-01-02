### Day 1 - 1/2/2023

Modern systems programming language focusing on *safety*, *speed* and *concurrency*.
Accomplishes these goals by being memory safe without using garbage collection.

### [Rust editions](https://doc.rust-lang.org/edition-guide/editions/index.html)

Backport using cargo `--edition`, `cargo fix` can help  on code base migration

* Rust 2015 (May) - stability without stagnation
  * Do not have async or await keywords
* Rust 2018
* Rust 2021 (latest) - 1.56.0 - RFC #3085

Current version used:

```
$ cargo version
cargo 1.66.0 (d65d197ad 2022-11-15)
```

## Tools and binaries

Install Rust on ~/.cargo with the following command

```
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```

Shell configuration is already made, toolchain management is made via `rustup` (i.e. gimme or pyenv)

`cargo` (pkg manager) is the tool responsible to manage projects, install dependencies, run tests, etc.

```shell
cargo --help
Some common cargo commands are (see all commands with --list):
    build, b    Compile the current package
    check, c    Analyze the current package and report errors, but don't build object files
    clean       Remove the target directory
    doc, d      Build this package's and its dependencies' documentation
    new         Create a new cargo package
    init        Create a new cargo package in an existing directory
    add         Add dependencies to a manifest file
    remove      Remove dependencies from a manifest file
    run, r      Run a binary or example of the local package
    test, t     Run the tests
    bench       Run the benchmarks
    update      Update dependencies listed in Cargo.lock
    search      Search registry for crates
    publish     Package and upload this package to the registry
    install     Install a Rust binary. Default location is $HOME/.cargo/bin
    uninstall   Uninstall a Rust binary
```

Other binaries includes:

* rustc - rust compiler
* mdbook - create markdown books like this one
* rustfmt - linter and formatter
* rustup - toolchain installer
