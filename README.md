# zkEVM-framework

Framework for test verification of state of cluster based on EVM

## Content

* Message generator
* EVM assigner
* Proof generator

### Libraries

* `data_types` - definitions of data structures used for verification

## Dependencies

### Build dependencies

* [CMake](https://cmake.org/)
* [GCC 13+](https://gcc.gnu.org/) (required by SSZ++)
* [Ninja](https://ninja-build.org/) (recommended)

### SSZ++

* Hashtree [Repository](https://github.com/prysmaticlabs/hashtree)
* intx: GMP (intx dependency) [Homepage](https://gmplib.org/), intx [Repository](https://github.com/chfast/intx)
* SSZ++ [Repository](https://github.com/OffchainLabs/sszpp)

### EVM-assigner

* evmc [Repository](https://github.com/ethereum/evmc)
* =nil; crypto3 [Repository](https://github.com/NilFoundation/crypto3)
* =nil; zkllvm-blueprint [Repository](https://github.com/NilFoundation/zkllvm-blueprint)
* =nil; evm-assigner [Repository](https://github.com/NilFoundation/evm-assigner)

### Block generator

* Valijson [Repository](https://github.com/tristanpenman/valijson)

## Clone

Using SSH:

```plain
git clone https://github.com/NilFoundation/zkEVM-framework.git
```

Or if you are using GitHub PAT to authenticate using HTTPS:

```plain
git clone https://<GITGUB_TOKEN>@github.com/NilFoundation/zkEVM-framework.git
```

## Nix environment

Instead of installing all dependencies manually,
you can use [Nix](https://nixos.org/download#download-nix) environment with all installed deps.

For using experimantal features `flake`, `nix-command` add follow line
to your Nix configuration file (`/etc/nix/nix.conf`):

```plaintext
experimental-features = nix-command flakes
```

Because zkEVM-framework currently has EVM-assigner as a dependency which is private repository,
it is required to configure Nix with GitHub Personal Access Token (PAT).
In order to generate your PAT, go [here](https://github.com/settings/tokens). Your token
must have access to content of private repositories.

Then add this to your Nix configuration file (`/etc/nix/nix.conf`):

```plaintext
access-tokens = github.com=<YOUR PAT>
```

If for some reason you don't want to use PAT, you can fallback to using SSH authentication
using `--override-input` option. Add this to your Nix command:

```plaintext
--override-input nil_evm_assigner git+ssh://git@github.com/NilFoundation/evm-assigner.git
````

To activate development environment run:

```bash
nix develop
```

If you want Nix to use your local version of dependency instead of cloning it from GitHub,
you can use `--override-input`. E.g. if you want to use your local evm-assigner, use this:

```bash
nix develop --override-input nil_evm_assigner path:/path/to/evm-assigner
```

Same option applies to `nix build` and etc.

## Build

```plain
cmake --workflow --preset release-build
```

Or with Nix:

```bash
nix build
```

## Assigner

### Build && run

```bash
nix run .#assigner -- -b block -t assignments -e pallas
```

## Test

### Configure tests

Configure with tests enabled:

```bash
cmake -B ${BUILD_DIR:-build} -DENABLE_TESTS=TRUE ...
```

When configuring with enabled tests, you can specify, which tests to enable. By default all tests are enabled.

```bash
# Data types tests won't be enabled
cmake -B ${BUILD_DIR:-build} -DENABLE_TESTS=TRUE -DENABLE_DATA_TYPES_TESTS=FALSE ...
```

### Build tests

```bash
cmake --build ${BUILD_DIR:-build}
```

When using `Ninja` generator, you can build only tests using target `tests/all`:

```bash
cmake --build ${BUILD_DIR:-build} -t tests/all
```

### Run tests

```bash
ctest --test-dir ${BUILD_DIR:-build}/tests
```

When using Nix, you can configure, build and run tests with:

```bash
nix flake check
```

## Build API documentation

zkEVM-framework is using Doxygen to generate API documentaion.
To build it, firts enable building documentation at configuration:

```bash
cmake -B ${BUILD_DIR:-build} -DBUILD_DOCS=TRUE ...
```

Then build documentation:

```bash
cmake -B ${BUILD_DIR:-build} -t docs
```

To see HTML documentation, open `${BUILD_DIR}/html`.

## Usage

### Block call

Account storage contains one account with deployed contract.
Incoming block has one transaction which call deployed contract
Config file with initial state of account storage could be passed with `-s`

```bash
nix run .#assigner [-L] [--override-input nil_evm_assigner /path_to/evm-assigner] -- -b bin/assigner/example_data/call_block.ssz -t assignments -e pallas [-s bin/assigner/example_data/state.json] [--log-level debug]
```

### Block generation

Test block could be generated from config file in JSON format

```bash
nix run .#block_gen [-L] -i bin/assigner/example_data/call_block.json -o call_block.ssz
```
