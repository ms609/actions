# Build documentation with pkgdown

This GitHub action checks that changes to a package do not break reverse
dependencies.

# Using this workflow

1. Create a file `.github/actions/pkgdown.yml` with the content:

```yml
on:
  push:
    branches:
      - main
      - master
    paths-ignore:
      - "Meta**"
      - "data-raw**"
      - "docs**"
      - "inst**"
      - "man**"
      - "memcheck**"
      - "tests**"
      - "vignettes**"
      - "**.git"
      - "**.json"
      - "**.md"
      - "**.yml"
      - "!**revdepcheck.yml"
      - "**.R[dD]ata"
      - "**.Rpro*"
  pull_request:
    branches:
      - main
      - master
    paths-ignore:
      - "Meta**"
      - "memcheck**"
      - "data-raw**"
      - "docs**"
      - "inst**"
      - "man**"
      - "tests**"
      - "vignettes**"
      - "**.git"
      - "**.json"
      - "**.md"
      - "**.yml"
      - "!**revdepcheck.yml"
      - "**.R[dD]ata"
      - "**.Rpro*"

name: revdep-check

jobs:
  mem-check:
    runs-on: macOS-latest

    name: revdepcheck, macOS, R release

    env:
      _R_CHECK_CRAN_INCOMING_: true # Seemingly not set by --as-cran
      _R_CHECK_FORCE_SUGGESTS_: false # CRAN settings
      R_COMPILE_AND_INSTALL_PACKAGES: 'never'
      _R_CHECK_THINGS_IN_CHECK_DIR_: false
      R_REMOTES_STANDALONE: true
      R_REMOTES_NO_ERRORS_FROM_WARNINGS: true
      RSPM: ${{ matrix.config.rspm }}
      GITHUB_PAT: ${{ secrets.GITHUB_TOKEN }}
```

For a simple run with few reverse dependencies, add:

```yml
    steps:
    - uses: ms609/actions/revdepcheck@main
```

For a package with many reverse dependencies, parallelize the process by adding
instead:

```yml
    strategy:
    fail-fast: false
    matrix:
      config:
        - {deps: '"Depends"'}
        - {deps: '"Suggests"'}
        - {deps: '"Imports"'}
        - {deps: '"LinkingTo"'}

    steps:
    - uses: ms609/actions/revdepcheck@main
      with:
        deps: ${{ matrix.config.deps }}
```


1. List any extra packages required to check reverse dependencies in a line
 of your package's `DESCRIPTION` file reading

`Config/Needs/revdeps: package1, package2`

# What fails the job

The job fails if a reverse dependency:

- has new errors, warnings or notes against the development version
  (`revdep/problems.md`);
- could not be checked, e.g. it or the CRAN release failed to install or
  timed out (`revdep/failures.md`); or
- has `R CMD check` errors against both the CRAN and the development version.
  These aren't new, but they usually mean the check environment is broken,
  and an erroring check can't detect new breakage.

The error text is printed in the job log, and `revdep/*.md` is always
uploaded as an artifact.

If a reverse dependency is currently broken on CRAN for reasons of its own,
tolerate its existing errors with:

```yml
    - uses: ms609/actions/revdepcheck@main
      with:
        allow-errors: BrokenPkg, OtherPkg
```
