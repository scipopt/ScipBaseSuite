# ScipBaseSuite

A minimal SCIP suite with batteries included (the soplex LP solver) with defaults that should work for most settings.

## Installing

```shell
git submodule update --init --recursive
mkdir build
cd build
cmake ..
make -j
```

The first line fetches `scip` and `soplex` from their GitHub repositories.
The repositories can then be changed to another branch.

Use `cmake -DCMAKE_BUILD_TYPE=Debug` or `=Release` for debug or release mode respectively.
See `INSTALL.md` in the `scip` [repository](https://github.com/scipopt/scip) for more options.
